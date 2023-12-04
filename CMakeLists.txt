cmake_minimum_required(VERSION 3.0)

project(
    hve
)

# system vs custom LGPL FFmpeg build

## optionally point to your path or pass as -DFFMPEG_MODULE_PATH="..." from CLI
# set(FFMPEG_MODULE_PATH "/home/meglickib/test/vcpkg/installed/x64-linux/share/ffmpeg")
# set(FFMPEG_MODULE_PATH "/home/extend/vcpkg/installed/arm64-linux/share/ffmpeg")

## if not explicitly passed to CMake try to read from environment variable
if(NOT DEFINED FFMPEG_MODULE_PATH)
  set(FFMPEG_MODULE_PATH $ENV{FFMPEG_MODULE_PATH})
endif()

if(DEFINED FFMPEG_MODULE_PATH) # point to custom FindFFMPEG.cmake with LGPL build
  list(APPEND CMAKE_MODULE_PATH ${FFMPEG_MODULE_PATH}) 
  set(CMAKE_INSTALL_RPATH $ORIGIN/lib)
  find_package(FFMPEG REQUIRED)
  LIST(GET FFMPEG_LIBRARY_DIRS 0 FFMPEG_LIBRARY_RELEASE_DIR)
else()
  MESSAGE(WARNING "trying to use system FFMmpeg libraries, define FFMPEG_MODULE_PATH for custom distributable FFmpeg LGPL build")
  set(FFMPEG_LIBRARIES avcodec avutil avfilter)
endif()

# build

add_library(hve hve.c)
target_include_directories(hve PRIVATE ${FFMPEG_INCLUDE_DIRS})
target_link_libraries(hve ${FFMPEG_LIBRARIES})

add_executable(hve-encode-raw-h264 examples/hve_encode_raw_h264.c)
target_link_libraries(hve-encode-raw-h264 hve)

add_executable(hve-encode-raw-hevc10 examples/hve_encode_raw_hevc10.c)
target_link_libraries(hve-encode-raw-hevc10 hve)

# install

install(TARGETS hve DESTINATION 	lib)
install(FILES hve.h DESTINATION include)

if(DEFINED FFMPEG_MODULE_PATH)
  install(DIRECTORY ${FFMPEG_LIBRARY_RELEASE_DIR} DESTINATION lib FILES_MATCHING PATTERN "*.so*")
endif()
