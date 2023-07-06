cmake_minimum_required (VERSION 2.8.11)

project (msckf)

set(CMAKE_BUILD_TYPE "RelWithDebInfo")

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11 -fpermissive")
if (MSVC)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /bigobj")
endif ()

# set (CMAKE_GENERATOR_PLATFORM x64)
option(USING_VIZ "Using opencv viz for 3D display" off)

if (USING_VIZ)
	add_definitions(-DUSING_VIZ)
endif ()

add_definitions(-DPROJECT_DIR="${CMAKE_CURRENT_LIST_DIR}")

# add OpenCV library
find_package (OpenCV REQUIRED)

# add Eigen library
find_package (Eigen3 REQUIRED)

include_directories(${OpenCV_INCLUDE_DIRS}
		${EIGEN3_INCLUDE_DIRS})



# compile msckf_vio library
include_directories (
	./include/core
	./include/system
	./include/util)

add_library (
	msckf_vio STATIC
	./src/core/image_processor.cpp
	./src/core/msckf_vio.cpp
	./src/system/msckf.cpp
	./src/system/params.cpp)

add_executable (
	msckf_system
	./msckf_vio/entry/main.cpp)

target_link_libraries (msckf_system msckf_vio ${OpenCV_LIBS})
if (NOT MSVC)
target_link_libraries (msckf_system -pthread)
endif ()
