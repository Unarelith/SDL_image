cmake_minimum_required(VERSION 3.3.2)
project(SDL_image C)

option(SUPPORT_JPG "Support loading JPEG images" ON)
option(SUPPORT_PNG "Support loading PNG images" ON)
option(SUPPORT_WEBP "Support loading WEBP images" OFF)
option(BUILD_SHOWIMAGE "Build the showimage sample program" OFF)
option(BUILD_SHARED_LIBS "Build the library as a shared library" ON)

if (BUILD_SHARED_LIBS)
	add_library(SDL2_image SHARED)
else()
	add_library(SDL2_image STATIC)
endif()

add_dependencies(SDL2_image SDL2-static)

target_sources(SDL2_image PRIVATE IMG.c IMG_png.c IMG_bmp.c IMG_gif.c
		IMG_jpg.c IMG_lbm.c IMG_pcx.c IMG_pnm.c IMG_svg.c IMG_tga.c
		IMG_tif.c IMG_webp.c IMG_WIC.c IMG_xcf.c IMG_xpm.c IMG_xv.c IMG_xxx.c)

target_compile_definitions(SDL2_image PRIVATE
		-DLOAD_BMP -DLOAD_GIF -DLOAD_LBM -DLOAD_PCX -DLOAD_PNM
		-DLOAD_TGA -DLOAD_XCF -DLOAD_XPM -DLOAD_XV -DLOAD_XPM)

if (SUPPORT_JPG)
	target_compile_definitions(SDL2_image PRIVATE -DLOAD_JPG)
	add_subdirectory(external/jpeg-9c)
	target_link_libraries(SDL2_image PRIVATE jpeg)
endif()

if (SUPPORT_PNG)
	# missing libpng.vers
	set(HAVE_LD_VERSION_SCRIPT OFF CACHE BOOL "" FORCE)
	target_compile_definitions(SDL2_image PRIVATE -DLOAD_PNG)

	if (MSVC OR WIN32)
		add_subdirectory(external/zlib-1.2.11 ${CMAKE_CURRENT_BINARY_DIR}/external/zlib-1.2.11)
		include_directories(external/zlib-1.2.11 ${CMAKE_CURRENT_BINARY_DIR}/external/zlib-1.2.11)
		target_link_libraries(SDL2_image PRIVATE zlib)

		set(SKIP_INSTALL_ALL ON CACHE BOOL "" FORCE)
		set(ZLIB_LIBRARY "zlib" CACHE STRING "" FORCE)
		set(PNG_BUILD_ZLIB ON CACHE BOOL "" FORCE)
		set(PNG_SHARED OFF CACHE BOOL "" FORCE)
	endif ()

	set(PNG_TESTS OFF CACHE BOOL "" FORCE)

	add_subdirectory(external/libpng-1.6.37)
	include_directories(external/libpng-1.6.37)
	target_link_libraries(SDL2_image PRIVATE png_static)
endif()

if (SUPPORT_WEBP)
	target_compile_definitions(SDL2_image PRIVATE -DLOAD_WEBP)
	# missing cpufeatures
	add_subdirectory(external/libwebp-1.0.3)
	target_link_libraries(SDL2_image PRIVATE webp)
endif()

add_library(SDL2::image ALIAS SDL2_image)

target_include_directories(SDL2_image PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})

target_link_libraries(SDL2_image PRIVATE SDL2-static)

if(BUILD_SHOWIMAGE)
	add_executable(showimage showimage.c)
	target_link_libraries(showimage PRIVATE SDL2-static SDL2::image)
endif()
