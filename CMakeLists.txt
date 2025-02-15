cmake_minimum_required(VERSION 3.6)

if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE Release CACHE STRING "Release/Debug")
endif()

if(PROJECT_SOURCE_DIR STREQUAL CMAKE_SOURCE_DIR)
    set(SUBPROCESS_MASTER_PROJECT ON)
endif()

option(BUILD_TESTS "Build tests" ${SUBPROCESS_MASTER_PROJECT})

set(CMAKE_EXPORT_COMPILE_COMMANDS ON)
include(CheckCXXCompilerFlag)

function(enable_cxx_compiler_flag_if_supported flag)
    string(FIND "${CMAKE_CXX_FLAGS}" "${flag}" flag_already_set)
    if(flag_already_set EQUAL -1)
        check_cxx_compiler_flag("${flag}" flag_supported)
        if(flag_supported)
            set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${flag}" PARENT_SCOPE)
        endif()
        unset(flag_supported CACHE)
    endif()
endfunction()

project(subprocess VERSION 0.4.0 LANGUAGES CXX C)

if (NOT CMAKE_CXX_STANDARD)
    set(CMAKE_CXX_STANDARD 17)
endif()
message(STATUS "CMAKE_CXX_STANDARD = ${CMAKE_CXX_STANDARD}")
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

enable_cxx_compiler_flag_if_supported(-pedantic)

if(MSVC)
    add_compile_options(-Zc:__cplusplus)
endif()




add_subdirectory(src/cpp subprocess)

# TODO testing
if(BUILD_TESTS)
    enable_testing()
    add_subdirectory(test)
endif()
