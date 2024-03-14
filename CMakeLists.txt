cmake_minimum_required(VERSION 3.10)

project(freetype C)

set(CMAKE_C_STANDARD 99)
set(CMAKE_C_STANDARD_REQUIRED ON)
set(CMAKE_C_EXTENSIONS OFF)

set(outputdir "bin")

if(WIN32)
    set(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS TRUE)
endif()

set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/${outputdir}/freetype")
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/${outputdir}/freetype")
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/${outputdir}/freetype")

file(GLOB_RECURSE FREETYPE_SOURCES
    "freetype/include/ft2build.h"
    "freetype/include/freetype/*.h"
    "freetype/include/freetype/config/*.h"
    "freetype/include/freetype/internal/*.h"
    "freetype/src/autofit/autofit.c"
    "freetype/src/base/ftbase.c"
    "freetype/src/base/ftbbox.c"
    "freetype/src/base/ftbdf.c"
    "freetype/src/base/ftbitmap.c"
    "freetype/src/base/ftcid.c"
    "freetype/src/base/ftdebug.c"
    "freetype/src/base/ftfstype.c"
    "freetype/src/base/ftgasp.c"
    "freetype/src/base/ftglyph.c"
    "freetype/src/base/ftgxval.c"
    "freetype/src/base/ftinit.c"
    "freetype/src/base/ftmm.c"
    "freetype/src/base/ftotval.c"
    "freetype/src/base/ftpatent.c"
    "freetype/src/base/ftpfr.c"
    "freetype/src/base/ftstroke.c"
    "freetype/src/base/ftsynth.c"
    "freetype/src/base/ftsystem.c"
    "freetype/src/base/fttype1.c"
    "freetype/src/base/ftwinfnt.c"
    "freetype/src/bdf/bdf.c"
    "freetype/src/bzip2/ftbzip2.c"
    "freetype/src/cache/ftcache.c"
    "freetype/src/cff/cff.c"
    "freetype/src/cid/type1cid.c"
    "freetype/src/gzip/ftgzip.c"
    "freetype/src/lzw/ftlzw.c"
    "freetype/src/pcf/pcf.c"
    "freetype/src/pfr/pfr.c"
    "freetype/src/psaux/psaux.c"
    "freetype/src/pshinter/pshinter.c"
    "freetype/src/psnames/psnames.c"
    "freetype/src/raster/raster.c"
    "freetype/src/sdf/sdf.c"
    "freetype/src/sfnt/sfnt.c"
    "freetype/src/smooth/smooth.c"
    "freetype/src/truetype/truetype.c"
    "freetype/src/type1/type1.c"
    "freetype/src/type42/type42.c"
    "freetype/src/winfonts/winfnt.c"
)

include_directories(freetype PUBLIC
	${CMAKE_CURRENT_SOURCE_DIR}/freetype/include
)

add_library(freetype STATIC ${FREETYPE_SOURCES})

target_compile_definitions(freetype PRIVATE FT2_BUILD_LIBRARY _CRT_SECURE_NO_WARNINGS _CRT_NONSTDC_NO_WARNINGS)

if(WIN32)
    target_compile_definitions(freetype PRIVATE _CRT_SECURE_NO_WARNINGS _CRT_NONSTDC_NO_WARNINGS)
    target_compile_options(freetype PRIVATE /W4)
else()
    target_compile_options(freetype PRIVATE -Wall -Wextra -pedantic)
endif()

if(CMAKE_BUILD_TYPE MATCHES Debug)
    target_compile_options(freetype PRIVATE -g)
    target_compile_definitions(freetype PRIVATE DEBUG=1)
elseif(CMAKE_BUILD_TYPE MATCHES Release)
    target_compile_options(freetype PRIVATE -O3)
endif()

project(msdfgen CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

set(outputdir "bin")

if(WIN32)
    set(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS TRUE)
endif()

set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/${outputdir}/msdfgen")
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/${outputdir}/msdfgen")
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/${outputdir}/msdfgen")

file(GLOB_RECURSE MSDFGEN_SOURCES "core/*.h" "core/*.hpp" "core/*.cpp" "ext/*.h" "ext/*.hpp" "ext/*.cpp" "lib/*.cpp" "include/*.h")

include_directories(msdfgen PUBLIC
    ${CMAKE_CURRENT_SOURCE_DIR}/include
)

add_library(msdfgen STATIC ${MSDFGEN_SOURCES})

target_compile_definitions(msdfgen PRIVATE MSDFGEN_USE_CPP11)

target_link_libraries(msdfgen PUBLIC freetype)

# Surpress all warnings for freetype (Visual Studio)
target_compile_options(freetype PUBLIC /w)

if(WIN32)
    target_compile_definitions(msdfgen PRIVATE _CRT_SECURE_NO_WARNINGS)
    target_compile_options(msdfgen PRIVATE /W4)
else()
    target_compile_options(msdfgen PRIVATE -Wall -Wextra -pedantic)
endif()

if(CMAKE_BUILD_TYPE MATCHES Debug)
    target_compile_options(msdfgen PRIVATE -g)
    target_compile_definitions(msdfgen PRIVATE DEBUG=1)
elseif(CMAKE_BUILD_TYPE MATCHES Release)
    target_compile_options(msdfgen PRIVATE -O3)
endif()
