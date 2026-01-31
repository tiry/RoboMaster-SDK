cmake_minimum_required(VERSION 3.15)
project (robomaster_media_codec)
set(CMAKE_CXX_STANDARD 14)
set(CMAKE_BUILD_TYPE Release)
SET(LIBRARY_OUTPUT_PATH ${CMAKE_SOURCE_DIR}/output)

# Use FetchContent to get modern pybind11 instead of submodule
include(FetchContent)
FetchContent_Declare(
    pybind11
    GIT_REPOSITORY https://github.com/pybind/pybind11.git
    GIT_TAG v2.11.1
)
FetchContent_MakeAvailable(pybind11)

pybind11_add_module(libmedia_codec src/media_codec.cpp)

IF (CMAKE_SYSTEM_NAME MATCHES "Windows")
	SET(OPUS_INCLUDE_DIRS "${CMAKE_SOURCE_DIR}/src/opus-share/include")
	SET(OPUS_LIBRARIES "${CMAKE_SOURCE_DIR}/src/opus-share/lib/opus.lib")
	SET(FFMEPG_INCLUDE_DIRS "${CMAKE_SOURCE_DIR}/src/ffmpeg-4.2.2-win64-dev/include")
	SET(FFMEPG_LIBRARIES_PATH "${CMAKE_SOURCE_DIR}/src/ffmpeg-4.2.2-win64-dev/lib")
	target_link_libraries(libmedia_codec PRIVATE
			${FFMEPG_LIBRARIES_PATH}/avcodec.lib
			${FFMEPG_LIBRARIES_PATH}/swscale.lib
			${FFMEPG_LIBRARIES_PATH}/avutil.lib
			${OPUS_LIBRARIES})
	target_include_directories(libmedia_codec PRIVATE 
			${FFMEPG_INCLUDE_DIRS} 
			${OPUS_INCLUDE_DIRS})
ELSE ()
	target_link_libraries(libmedia_codec PRIVATE opus avcodec swscale avutil)
ENDIF (CMAKE_SYSTEM_NAME MATCHES "Windows")
