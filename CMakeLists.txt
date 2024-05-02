cmake_minimum_required (VERSION 3.7)
cmake_policy(VERSION 3.7...3.20)

project(ActionsTest)
list(APPEND CMAKE_MODULE_PATH ${CMAKE_CURRENT_SOURCE_DIR}/CMake)
#MESSAGE(STATUS "CMAKE_MODULE_PATH: " ${CMAKE_MODULE_PATH})

set(CMAKE_CXX_STANDARD 17)

file(GLOB_RECURSE SOURCES src/*.cpp src/*.c src/*.hpp src/*.h)
include_directories(src)

if(VCPKG_TOOLCHAIN)
    if(EXISTS "${Z_VCPKG_ROOT_DIR}/triplets/${VCPKG_TARGET_TRIPLET}.cmake")
        include("${Z_VCPKG_ROOT_DIR}/triplets/${VCPKG_TARGET_TRIPLET}.cmake")
    elseif(EXISTS "${Z_VCPKG_ROOT_DIR}/triplets/community/${VCPKG_TARGET_TRIPLET}.cmake")
        include("${Z_VCPKG_ROOT_DIR}/triplets/community/${VCPKG_TARGET_TRIPLET}.cmake")
    else()
        message(FATAL_ERROR "Could not find a vcpkg triplet file for ${VCPKG_TARGET_TRIPLET}.")
    endif()
endif()

if(WIN32)
    add_executable(ActionsTest WIN32 ${SOURCES})
elseif(APPLE)
    add_executable(ActionsTest MACOSX_BUNDLE ${SOURCES})
else()
    add_executable(ActionsTest ${SOURCES})
endif()

if (MSVC)
    find_library(GENERIC_CODE_GEN_LIB_DEBUG GenericCodeGend
            PATHS ${VCPKG_DEBUG_LIB_DIR}
            NO_DEFAULT_PATH)
else()
    find_library(GENERIC_CODE_GEN_LIB_DEBUG GenericCodeGen
            PATHS ${VCPKG_DEBUG_LIB_DIR}
            NO_DEFAULT_PATH)
endif()
find_library(GENERIC_CODE_GEN_LIB_RELEASE GenericCodeGen
        PATHS ${VCPKG_RELEASE_LIB_DIR}
        NO_DEFAULT_PATH)
add_library(GenericCodeGen STATIC IMPORTED)
set_target_properties(GenericCodeGen PROPERTIES
        IMPORTED_LOCATION ${GENERIC_CODE_GEN_LIB_DEBUG}
        IMPORTED_LOCATION_DEBUG ${GENERIC_CODE_GEN_LIB_DEBUG}
        IMPORTED_LOCATION_RELEASE ${GENERIC_CODE_GEN_LIB_RELEASE}
        INTERFACE_INCLUDE_DIRECTORIES "${VCPKG_INCLUDE_DIR}")

find_library(SHADERC_LIB_DEBUG shaderc
        PATHS ${VCPKG_DEBUG_LIB_DIR}
        NO_DEFAULT_PATH)
find_library(SHADERC_LIB_RELEASE shaderc
        PATHS ${VCPKG_RELEASE_LIB_DIR}
        NO_DEFAULT_PATH)
add_library(shaderc STATIC IMPORTED)
set_target_properties(shaderc PROPERTIES
        IMPORTED_LOCATION ${SHADERC_LIB_DEBUG}
        IMPORTED_LOCATION_DEBUG ${SHADERC_LIB_DEBUG}
        IMPORTED_LOCATION_RELEASE ${SHADERC_LIB_RELEASE}
        INTERFACE_INCLUDE_DIRECTORIES "${VCPKG_INCLUDE_DIR}")

message(STATUS ${GENERIC_CODE_GEN_LIB_DEBUG})
message(STATUS ${SHADERC_LIB_DEBUG})
