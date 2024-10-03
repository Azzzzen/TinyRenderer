cmake_minimum_required(VERSION 3.10)

#在CMake中使用vcpkg
set(CMAKE_TOOLCHAIN_FILE "C:/Users/Admin/vcpkg/scripts/buildsystems/vcpkg.cmake" CACHE STRING "Vcpkg toolchain file")

# 设置项目名称和语言
project(TinyRenderer LANGUAGES CXX)

# 添加可执行文件
add_executable(7
    gl.cpp  # 源文件
    main.cpp
    model.cpp
    tgaimage.cpp
)

# 如果你使用 vcpkg 安装了 OpenGL 和其他依赖库，可以链接它们
find_package(Eigen3 REQUIRED)
target_link_libraries(7 Eigen3::Eigen)

# 添加头文件搜索路径
target_include_directories(7 PRIVATE
    ${CMAKE_CURRENT_SOURCE_DIR}
)
