cmake_minimum_required(VERSION 3.10...4.0)

project(GlfwTest)

set(CMAKE_CXX_STANDARD 17)
file(GLOB_RECURSE SOURCES src/*.cpp src/*.c src/*.hpp src/*.h)
add_executable(GlfwTest ${SOURCES})

find_package(glfw3 REQUIRED)
target_link_libraries(GlfwTest PRIVATE glfw)

try_compile(
        GLFW_SUPPORTS_X11
        "${CMAKE_BINARY_DIR}/TestGlfw/X11"
        "${CMAKE_SOURCE_DIR}/CMake/TestGlfwBackend.cpp"
        LINK_LIBRARIES glfw
        COMPILE_DEFINITIONS -DTEST_X11)
try_compile(
        GLFW_SUPPORTS_WAYLAND
        "${CMAKE_BINARY_DIR}/TestGlfw/Wayland"
        "${CMAKE_SOURCE_DIR}/CMake/TestGlfwBackend.cpp"
        LINK_LIBRARIES glfw
        COMPILE_DEFINITIONS -DTEST_WAYLAND)

message(STATUS "GLFW_SUPPORTS_X11: ${GLFW_SUPPORTS_X11}")
message(STATUS "GLFW_SUPPORTS_WAYLAND: ${GLFW_SUPPORTS_WAYLAND}")
message(STATUS "OUT_X11: ${OUT_X11}")
if (NOT ${GLFW_SUPPORTS_X11})
    message(FATAL_ERROR "Expected X11 support to be present.")
endif()
