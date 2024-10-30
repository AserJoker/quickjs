cmake_minimum_required(VERSION 3.12)
project(quickjs)

string(TIMESTAMP COMPILE_TIME %Y-%m-%d)

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wall -MMD -MF -Wno-array-bounds -Wno-format-truncation -fwrapv")

add_definitions(-DCONFIG_VERSION="${COMPILE_TIME}")
add_definitions(-DCONFIG_BIGNUM)
file(WRITE repl.c "")
file(WRITE qjscalc.c "")
add_custom_target(repl.c  COMMAND ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/qjsc -c -o repl.c -m repl.js DEPENDS qjsc WORKING_DIRECTORY ${PROJECT_SOURCE_DIR})
add_custom_target(qjscalc.c COMMAND ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/qjsc -c -o qjscalc.c -m qjscalc.js DEPENDS qjsc  WORKING_DIRECTORY ${PROJECT_SOURCE_DIR})

add_executable(qjsc qjsc.c quickjs.c libregexp.c libunicode.c cutils.c quickjs-libc.c libbf.c)

add_executable(qjs qjs.c repl.c quickjs.c libregexp.c libunicode.c cutils.c quickjs-libc.c libbf.c qjscalc.c)

add_library(quickjs SHARED quickjs.c libregexp.c libunicode.c cutils.c quickjs-libc.c libbf.c)

target_link_libraries(qjsc pthread m)
target_link_libraries(qjs pthread m)
target_link_libraries(quickjs pthread m)

add_dependencies(qjs repl.c qjscalc.c)