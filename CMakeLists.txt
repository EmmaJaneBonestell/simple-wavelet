cmake_minimum_required(VERSION 3.12.0)
project(simple_wavelet
	HOMEPAGE_URL https://github.com/EmmaJaneBonestell/simple-wavelet
	DESCRIPTION "Simple library for wavelet transformations"
	VERSION 1.0
)

include(GNUInstallDirs)

find_program(PKGCONF pkg-config)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED True)

add_library(simple_wavelet OBJECT matrix.cpp wavelet.cpp)
set_target_properties(simple_wavelet
	PROPERTIES
	    PUBLIC_HEADER "matrix.h;vector.h;wavelet.h"
		POSITION_INDEPENDENT_CODE 1
)

target_include_directories(simple_wavelet PRIVATE "${PROJECT_SOURCE_DIR}")

target_sources(simple_wavelet 
    PRIVATE matrix.cpp wavelet.cpp
)

add_executable(sw sw.cpp $<TARGET_OBJECTS:simple_wavelet>)

add_library(simple_wavelet_shared SHARED $<TARGET_OBJECTS:simple_wavelet>)
set_target_properties(simple_wavelet_shared
    PROPERTIES
    PUBLIC_HEADER "matrix.h;vector.h;wavelet.cpp"
    OUTPUT_NAME simple_wavelet
)

IF (WIN32)
    add_library(simple_wavelet_static STATIC $<TARGET_OBJECTS:simple_wavelet>)
ELSE()
    add_library(simple_wavelet_static STATIC $<TARGET_OBJECTS:simple_wavelet>)
ENDIF()

set_target_properties(simple_wavelet_static
    PROPERTIES
    PUBLIC_HEADER "matrix.h;vector.h;wavelet.h"
    OUTPUT_NAME simple_wavelet
)
install(TARGETS simple_wavelet_shared simple_wavelet_static
	LIBRARY DESTINATION lib
	ARCHIVE DESTINATION lib
	PUBLIC_HEADER DESTINATION include/simple_wavelet
)

IF(PKGCONF)
    set(sw_pkg simple_wavelet)
    set(pc_libs_private)
    set(pc_req_private)
    set(pc_req_public) 
    configure_file(simple_wavelet.pc.in simple_wavelet.pc @ONLY) 
    install(FILES ${CMAKE_BINARY_DIR}/simple_wavelet.pc
    	DESTINATION ${CMAKE_INSTALL_LIBDIR}/pkgconfig)
ENDIF()   
