cmake_minimum_required(VERSION 3.2)

project(CompactNSearch)

# Visual studio solution directories.
set_property(GLOBAL PROPERTY USE_FOLDERS on)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

if (UNIX AND NOT APPLE)
	find_package(OpenMP)
elseif (APPLE)
	set(CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/CMake" ${CMAKE_MODULE_PATH})
	include(PatchOpenMPApple)
elseif (WIN32)
	set(OpenMP_CXX_FLAGS "-fopenmp")
	set(OPENMP_FOUND true)
endif()



SET(CMAKE_DEBUG_POSTFIX "_d")

set (HEADER_FILES 
		include/Config.h
		include/CompactNSearch.h
		include/PointSet.h
		include/DataStructures.h)

add_library(CompactNSearch
		${HEADER_FILES}
		src/CompactNSearch.cpp
)

target_include_directories(CompactNSearch PUBLIC include ${OpenMP_INCLUDE_DIR})
if (OPENMP_FOUND)
	target_compile_options(CompactNSearch PUBLIC ${OpenMP_CXX_FLAGS})
	target_link_libraries(CompactNSearch PUBLIC ${OpenMP_CXX_FLAGS})
endif(OPENMP_FOUND)

install(FILES "include/CompactNSearch" ${HEADER_FILES}
	DESTINATION include/)

install(TARGETS CompactNSearch
	RUNTIME DESTINATION bin
	LIBRARY DESTINATION lib
	ARCHIVE DESTINATION lib)
	
OPTION(USE_DOUBLE_PRECISION "Use double precision"	OFF)
if (USE_DOUBLE_PRECISION)
	target_compile_definitions(CompactNSearch PUBLIC -DUSE_DOUBLE)
endif (USE_DOUBLE_PRECISION)	

option(BUILD_DEMO "Build example of how to use this library."
		ON)
if(BUILD_DEMO)
	add_subdirectory(demo)
endif(BUILD_DEMO)

