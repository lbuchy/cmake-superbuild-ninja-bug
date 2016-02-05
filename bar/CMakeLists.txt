cmake_minimum_required(VERSION 3.4)

project(bar)

find_package(foo)
#include(${FOO_DIR}/foo-exports.cmake)

add_executable(bar main.cpp)
target_link_libraries(bar foo)
