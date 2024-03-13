cmake_minimum_required(VERSION 3.10)

project(msdfgen CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

add_subdirectory(freetype)

set(outputdir "bin")

if(WIN32)
    set(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS TRUE)
endif()

set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/${outputdir}/${PROJECT_NAME}")
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/${outputdir}/${PROJECT_NAME}")
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/${outputdir}/${PROJECT_NAME}")

file(GLOB_RECURSE SOURCES "core/*.h" "core/*.hpp" "core/*.cpp" "ext/*.h" "ext/*.hpp" "ext/*.cpp" "lib/*.cpp" "include/*.h")

include_directories(
    "include"
    "freetype/include"
)

add_library(${PROJECT_NAME} STATIC ${SOURCES})

target_compile_definitions(${PROJECT_NAME} PRIVATE MSDFGEN_USE_CPP11)

target_link_libraries(${PROJECT_NAME} PRIVATE freetype)

# Surpress all warnings for target library (Visual Studio)
target_compile_options(freetype PRIVATE /w)

if(WIN32)
    target_compile_definitions(${PROJECT_NAME} PRIVATE _CRT_SECURE_NO_WARNINGS)
    target_compile_options(${PROJECT_NAME} PRIVATE /W4)
else()
    target_compile_options(${PROJECT_NAME} PRIVATE -Wall -Wextra -pedantic)
endif()

if(CMAKE_BUILD_TYPE MATCHES Debug)
    target_compile_options(${PROJECT_NAME} PRIVATE -g)
    target_compile_definitions(${PROJECT_NAME} PRIVATE DEBUG=1)
elseif(CMAKE_BUILD_TYPE MATCHES Release)
    target_compile_options(${PROJECT_NAME} PRIVATE -O3)
endif()
