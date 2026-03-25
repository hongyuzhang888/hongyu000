# set minimum cmake version
cmake_minimum_required(VERSION 3.21 FATAL_ERROR)

# project name and language
project(recipe-07 LANGUAGES CXX)

# require C++11
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_EXTENSIONS OFF)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# example code
add_executable(example example.cpp)

# needs to find the generated header file
target_include_directories(example
  PRIVATE
    ${CMAKE_CURRENT_BINARY_DIR}/generated
  )

#CMakeLists.txt的剩余部分，记录了每次编译代码时的Git Hash:
#示例中，在构建时执行CMake代码。为此，定义了一个自定义命令:
add_custom_command(
  OUTPUT
    ${CMAKE_CURRENT_BINARY_DIR}/generated/version.hpp
    ALL
  COMMAND
    ${CMAKE_COMMAND} -D TARGET_DIR=${CMAKE_CURRENT_BINARY_DIR} -P ${CMAKE_CURRENT_SOURCE_DIR}/git-hash.cmake
  WORKING_DIRECTORY
    ${CMAKE_CURRENT_SOURCE_DIR}
  )

#我们还定义了一个目标:
# rebuild version.hpp every time
add_custom_target(
  get_git_hash
  ALL
  DEPENDS
    ${CMAKE_CURRENT_BINARY_DIR}/generated/version.hpp
  )

# version.hpp has to be generated
# before we start building example
add_dependencies(example get_git_hash)
