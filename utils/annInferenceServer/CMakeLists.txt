# Copyright (c) 2015 Advanced Micro Devices, Inc. All rights reserved.
# 
# Permission is hereby granted, free of charge, to any person obtaining a copy
# of this software and associated documentation files (the "Software"), to deal
# in the Software without restriction, including without limitation the rights
# to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
# copies of the Software, and to permit persons to whom the Software is
# furnished to do so, subject to the following conditions:
#  
# The above copyright notice and this permission notice shall be included in
# all copies or substantial portions of the Software.
#  
# THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
# IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
# FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.  IN NO EVENT SHALL THE
# AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
# LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
# OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
# THE SOFTWARE.


cmake_minimum_required (VERSION 2.8)
project (annInferenceServer)

set (CMAKE_CXX_STANDARD 11)

find_package(OpenCL REQUIRED)
find_package(OpenCV REQUIRED)

include_directories(../../deps/amdovx-core/openvx/include ../../vx_nn)

list(APPEND SOURCES
	common.cpp
	arguments.cpp
	netutil.cpp
	configure.cpp
	compiler.cpp
	inference.cpp
	server.cpp
	main.cpp
        profiler.cpp
        region.cpp
        shadow.cpp
)

add_executable(annInferenceServer ${SOURCES})
target_link_libraries(annInferenceServer vx_nn openvx pthread)
install (TARGETS annInferenceServer DESTINATION bin)

if (OpenCL_FOUND)
	include_directories(${OpenCL_INCLUDE_DIRS}/CL ${OpenCL_INCLUDE_DIRS}/Headers)
	target_link_libraries(annInferenceServer ${OpenCL_LIBRARIES})
endif(OpenCL_FOUND)

if (OpenCV_FOUND)
	target_compile_definitions(annInferenceServer PUBLIC ENABLE_OPENCV=1)
	include_directories(${OpenCV_INCLUDE_DIRS})
	target_link_libraries(annInferenceServer ${OpenCV_LIBRARIES})
else(OpenCV_FOUND)
	target_compile_definitions(annInferenceServer PUBLIC ENABLE_OPENCV=0)
endif(OpenCV_FOUND)

if( POLICY CMP0054 )
  cmake_policy( SET CMP0054 OLD )
endif()
if ("${CMAKE_CXX_COMPILER_ID}" STREQUAL "MSVC")
	set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} /MT")
	set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} /MTd")
else()
        set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11 -msse4.2 -mf16c")
endif()
