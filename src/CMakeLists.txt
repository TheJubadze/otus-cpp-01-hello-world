cmake_minimum_required(VERSION 3.17)

set(PATCH_VERSION "1" CACHE INTERNAL "Patch version")
set(PROJECT_VESRION 0.0.${PATCH_VERSION})

project(helloworld VERSION ${PROJECT_VESRION})

find_package(Boost COMPONENTS unit_test_framework REQUIRED)

configure_file(version.hxx.in version.hxx)

add_executable(helloworld_cli main.cxx)
add_library(helloworld lib.cxx)
add_executable(test_version test_version.cxx)

set_target_properties(helloworld_cli helloworld test_version PROPERTIES
        CXX_STANDARD 14
        CXX_STANDARD_REQUIRED ON
        )

set_target_properties(test_version PROPERTIES
        COMPILE_DEFINITIONS BOOST_TEST_DYN_LINK
        INCLUDE_DIRECTORIES ${Boost_INCLUDE_DIR}
        )

target_include_directories(helloworld PRIVATE "${CMAKE_BINARY_DIR}")

target_link_libraries(helloworld_cli PRIVATE helloworld)
target_link_libraries(test_version ${Boost_LIBRARIES} helloworld)

if (MSVC)
    target_compile_options(helloworld_cli PRIVATE
            /W4
            )
    target_compile_options(helloworld PRIVATE
            /W4
            )
    target_compile_options(test_version PRIVATE
            /W4
            )
else ()
    target_compile_options(helloworld_cli PRIVATE
            -Wall -Wextra -pedantic -Werror
            )
    target_compile_options(helloworld PRIVATE
            -Wall -Wextra -pedantic -Werror
            )
    target_compile_options(test_version PRIVATE
            -Wall -Wextra -pedantic -Werror
            )
endif ()

install(TARGETS helloworld_cli RUNTIME DESTINATION bin)

set(CPACK_GENERATOR DEB)

set(CPACK_PACKAGE_VERSION_MAJOR "${PROJECT_VERSION_MAJOR}")
set(CPACK_PACKAGE_VERSION_MINOR "${PROJECT_VERSION_MINOR}")
set(CPACK_PACKAGE_VERSION_PATCH "${PROJECT_VERSION_PATCH}")

set(CPACK_DEBIAN_PACKAGE_MAINTAINER "Vitaly Senokosov")

include(CPack)

enable_testing()
add_test(test_version test_version)