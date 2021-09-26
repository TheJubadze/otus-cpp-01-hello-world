cmake_minimum_required(VERSION 3.17)

set(PATCH_VERSION "1" CACHE INTERNAL "Patch version")
set(PROJECT_VESRION 0.0.${PATCH_VERSION})

project(helloworld VERSION ${PROJECT_VESRION})

configure_file(version.hxx.in version.hxx)

add_executable(helloworld_cli main.cxx)
add_library(helloworld lib.cxx)

target_include_directories(helloworld PRIVATE "${CMAKE_BINARY_DIR}")
target_link_libraries(helloworld_cli PRIVATE helloworld)