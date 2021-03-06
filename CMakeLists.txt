cmake_minimum_required(VERSION 3.9)

project(renoir LANGUAGES CXX C)

set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/bin/$<CONFIG>")
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/bin/$<CONFIG>")
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/bin/$<CONFIG>")

set(CMAKE_EXPORT_COMPILE_COMMANDS ON)
set(CMAKE_POSITION_INDEPENDENT_CODE ON)
set(CMAKE_INSTALL_RPATH "$ORIGIN")
set(CMAKE_BUILD_RPATH "$ORIGIN")
set(CMAKE_INSTALL_RPATH_USE_LINK_PATH TRUE)

option(RENOIR_BUILD_EXAMPLES "Build example applications that showcase the renoir libraries." ON)
option(RENOIR_UNITY_BUILD "Combine all renoir source files into one jumbo build." ON)
option(RENOIR_USE_LOCAL_MN "Uses the local mn submodule in renoir" ON)
option(RENOIR_DEBUG_LAYER "Turn on debug layer in underlying graphics api" OFF)
option(RENOIR_LEAK "Turn on leak detector for graphics resources" OFF)

if (RENOIR_USE_LOCAL_MN)
	add_subdirectory(external/mn EXCLUDE_FROM_ALL)
endif()
add_subdirectory(external/glew EXCLUDE_FROM_ALL)

add_subdirectory(renoir-window)
add_subdirectory(renoir-gl450)

add_library(renoir INTERFACE)
add_library(MoustaphaSaad::renoir ALIAS renoir)
target_include_directories(renoir INTERFACE ${CMAKE_CURRENT_SOURCE_DIR}/include)
add_dependencies(renoir renoir-gl450)

if (WIN32)
	add_subdirectory(renoir-dx11)
	add_dependencies(renoir renoir-dx11)
endif (WIN32)

if (RENOIR_BUILD_EXAMPLES)
	add_subdirectory(examples)
endif()
