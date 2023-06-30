cmake_minimum_required(VERSION 3.11)
project(vstream_yolov7_example_cpp)

set(CMAKE_CXX_STANDARD 20)
set(COMPILE_OPTIONS -Werror -Wall -Wextra -Wconversion -O3 -Wno-reorder -std=c++11 -Wno-ignored-qualifiers -Wno-extra -Wno-unused-parameter)

set(CMAKE_THREAD_LIBS_INIT "-lpthread")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -pthread")
set(CMAKE_HAVE_THREADS_LIBRARY 1)
set(CMAKE_USE_WIN32_THREADS_INIT 0)
set(CMAKE_USE_PTHREADS_INIT 1)
set(THREADS_PREFER_PTHREAD_FLAG ON)

find_package(Threads)
find_package(HailoRT REQUIRED)
find_package(OpenCV REQUIRED)
message(STATUS "Found OpenCV: ${OpenCV_INCLUDE_DIRS}")

file(GLOB SOURCES
    double_buffer.cpp
    yolo_output.cpp
    yolo_postprocess.cpp
    yolov5_yolov7_inference.cpp
)

link_libraries(stdc++fs)
add_executable(${PROJECT_NAME} ${SOURCES})
include_directories(${OpenCV_INCLUDE_DIRS})
include_directories(${ONNXRUNTIME_INCLUDE_DIR})
include_directories(rapidjson/include)
link_directories(/opt/glibc-2.29/lib)
target_compile_options(${PROJECT_NAME} PRIVATE ${COMPILE_OPTIONS})
target_link_libraries(${PROJECT_NAME} HailoRT::libhailort ${CMAKE_THREAD_LIBS_INIT} ${OpenCV_LIBS})

# Specify the dynamic linker path explicitly
set(CMAKE_EXE_LINKER_FLAGS "-Wl,--dynamic-linker=/opt/glibc-2.29/lib/ld-linux-x86-64.so.2")

# Manually specify linker flags to include the path to GLIBC libraries
set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -Wl,-rpath,/opt/glibc-2.29/lib")
set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -Wl,-rpath-link,/opt/glibc-2.29/lib")

# Add GLIBC library explicitly
set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -L/opt/glibc-2.29/lib")

# Ensure that the rpath is used during the linking process
#set(CMAKE_BUILD_WITH_INSTALL_RPATH TRUE)
#set(CMAKE_INSTALL_RPATH "/opt/glibc-2.29/lib:/usr/lib:/usr/lib/x86_64-linux-gnu:/lib/x86_64-linux-gnu")
#LD_LIBRARY_PATH=/opt/glibc-2.29/lib:/usr/lib:/usr/lib/x86_64-linux-gnu:/lib/x86_64-linux-gnu