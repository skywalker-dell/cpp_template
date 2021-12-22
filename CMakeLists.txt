# 设置debug调试
# set(CMAKE_BUILD_TYPE "Debug")
# set(CMAKE_CXX_FLAGS_DEBUG "-O0 -Wall -g -ggdb")

cmake_minimum_required(VERSION 3.10.1)
project(demo)

if(NOT CMAKE_CXX_STANDARD)
    set(CMAKE_CXX_STANDARD 17)
    set(CMAKE_CXX_STANDARD_REQUIRED ON)
endif()

if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE "Release")
endif()

# set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -O3")

#开启编译缓存
find_program(CCACHE_PROGRAM ccache)
if(CCACHE_PROGRAM)
  message(STATUS "Set up ccache ...")
  set_property(GLOBAL PROPERTY RULE_LAUNCH_COMPILE ccache)
  set_property(GLOBAL PROPERTY RULE_LAUNCH_LINK ccache)
endif()

# 设置第三方库文件夹位置
set(OPENCV_DIR /usr/local/share/OpenCV)

# 搜索库
find_package(OpenCV REQUIRED)


# 添加源文件
file(GLOB_RECURSE sourcefiles "src/*.cpp")
add_executable(${PROJECT_NAME} ${sourcefiles})

# 添加头文件
# 递归添加头文件函数实现
function(include_sub_dir root_dir)
    if (IS_DIRECTORY ${root_dir}) 
        # message("include dir: " ${root_dir})
        include_directories(${root_dir})
    endif()
    file(GLOB ALL_SUB RELATIVE ${root_dir} ${root_dir}/*)
    foreach(sub ${ALL_SUB})
        if (IS_DIRECTORY ${root_dir}/${sub})
            include_sub_dir(${root_dir}/${sub})
        endif()
    endforeach()
endfunction()
#调用递归函数链接自己的头文件
include_sub_dir(${CMAKE_SOURCE_DIR}/src) 
# 链接其他头文件
include_directories( "/usr/include/eigen3" )
include_directories(${OpenCV_INCLUDE_DIRS})


# 添加链接库
target_link_libraries(${PROJECT_NAME}
        ${OpenCV_LIBS}
        -pthread)



