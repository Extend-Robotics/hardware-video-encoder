cmake_minimum_required(VERSION 3.0)

project(
    hve
)

list(APPEND CMAKE_MODULE_PATH "/home/meglickib/test/vcpkg/installed/x64-linux-er/share/ffmpeg")
set(CMAKE_INSTALL_RPATH $ORIGIN/lib)

find_package(FFMPEG REQUIRED)

add_library(hve hve.c)
target_link_libraries(hve ${FFMPEG_LIBRARIES})

install(TARGETS hve DESTINATION lib)
install(DIRECTORY ${FFMPEG_LIBRARY_DIRS}/lib DESTINATION lib FILES_MATCHING PATTERN "*.so*")
install(FILES hve.h DESTINATION include)

add_executable(hve-encode-raw-h264 examples/hve_encode_raw_h264.c)
target_link_libraries(hve-encode-raw-h264 hve)

add_executable(hve-encode-raw-hevc10 examples/hve_encode_raw_hevc10.c)
target_link_libraries(hve-encode-raw-hevc10 hve)
