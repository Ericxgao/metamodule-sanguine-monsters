cmake_minimum_required(VERSION 3.24)

if(NOT "${METAMODULE_SDK_DIR}" STREQUAL "")
	message("METAMODULE_SDK_DIR set by CMake variable ${METAMODULE_SDK_DIR}")
elseif (DEFINED ENV{METAMODULE_SDK_DIR})
    set(METAMODULE_SDK_DIR "$ENV{METAMODULE_SDK_DIR}")
	message("METAMODULE_SDK_DIR set by environment variable ${METAMODULE_SDK_DIR}")
else()
    set(METAMODULE_SDK_DIR "${CMAKE_CURRENT_LIST_DIR}/../metamodule-plugin-sdk")
	message("METAMODULE_SDK_DIR set to default: ${METAMODULE_SDK_DIR}")
endif()
include(${METAMODULE_SDK_DIR}/plugin.cmake)

project(SanguineMonsters VERSION 1.0.0 DESCRIPTION "SanguineMonsters for Rack" LANGUAGES C CXX)

# Set C++17 standard
set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

# Add option for virtual build
option(BUILD_VIRTUAL "Build for virtual environment" ON)

# Define source directories
set(SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/SanguineMonsters)

# Main library target
add_library(SanguineMonsters STATIC)

# Compiler definitions
target_compile_definitions(SanguineMonsters PRIVATE 
    __V1x 
    __PLUGIN
    $<$<BOOL:${BUILD_VIRTUAL}>:BUILD_VIRTUAL>
    _MB_CAPABLE=1
)

# Compiler options
target_compile_options(SanguineMonsters PRIVATE
    -fpermissive
    -fdata-sections
    -ffunction-sections
    -fno-exceptions
    -fno-threadsafe-statics
    -fno-use-cxa-atexit
    $<$<CXX_COMPILER_ID:GNU>:-fvisibility=hidden>
    $<$<CXX_COMPILER_ID:GNU>:-fvisibility-inlines-hidden>
)

# Linker options
target_link_options(SanguineMonsters PRIVATE
    -Wl,--gc-sections
)

target_sources(SanguineMonsters PRIVATE
    ${SOURCE_DIR}/src/sanguinecomponents.cpp
    ${SOURCE_DIR}/src/sanguinehelpers.cpp
    ${SOURCE_DIR}/src/themes.cpp
    ${SOURCE_DIR}/src/plugin.cpp
    ${SOURCE_DIR}/src/bukavac.cpp
    ${SOURCE_DIR}/src/dungeon.cpp
    ${SOURCE_DIR}/src/kitsune.cpp
    ${SOURCE_DIR}/src/medusa.cpp
    ${SOURCE_DIR}/src/oraculus.cpp
    ${SOURCE_DIR}/src/sphinx.cpp
    ${SOURCE_DIR}/src/werewolf.cpp
    ${SOURCE_DIR}/src/aion.cpp
    ${SOURCE_DIR}/src/superswitch18.cpp
    ${SOURCE_DIR}/src/superswitch81.cpp
    ${SOURCE_DIR}/src/oubliette.cpp
)

# Include directories
target_include_directories(SanguineMonsters PRIVATE 
    ${SOURCE_DIR}/src
    ${SOURCE_DIR}/pcg
)

# Create the plugin
create_plugin(
    SOURCE_LIB      SanguineMonsters
    PLUGIN_NAME     SanguineMonsters
    PLUGIN_JSON     ${SOURCE_DIR}/plugin.json
    SOURCE_ASSETS   ${CMAKE_CURRENT_LIST_DIR}/assets
    DESTINATION     ${CMAKE_CURRENT_LIST_DIR}/metamodule-plugins
) 