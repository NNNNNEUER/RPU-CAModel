cmake_minimum_required(VERSION 3.14.5)
project(HiCModel)
set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS}")
# 添加子目录
add_subdirectory(src)

# 设置测试可执行文件名称
set(TEST_EXECUTABLE runUnitTest)

# 查找所有的测试源文件
file(GLOB_RECURSE TEST_SOURCES 
 "${CMAKE_SOURCE_DIR}/unit_tests/*.cpp"
 "${CMAKE_SOURCE_DIR}/unit_tests/**/*.cpp"
 "${CMAKE_SOURCE_DIR}/unit_tests/**/**/*.cpp"
)

# 添加测试可执行文件
add_executable(${TEST_EXECUTABLE} ${TEST_SOURCES})

# 设置包含目录
target_include_directories(${TEST_EXECUTABLE} PRIVATE 
 ${CMAKE_SOURCE_DIR}/src/example
 ${CMAKE_SOURCE_DIR}/src/rpu
 ${CMAKE_SOURCE_DIR}/src/rpu/cu
 ${CMAKE_SOURCE_DIR}/src/basic_kernel
)

# 链接必要的库
target_link_libraries(${TEST_EXECUTABLE} PRIVATE 
 example
 basic_kernel 
 rpu
 gtest 
 gtest_main 
 pthread
)

# 添加编译选项以生成调试信息
target_compile_options(${TEST_EXECUTABLE} PRIVATE "-g")
# 添加链接选项以生成调试信息
target_link_options(${TEST_EXECUTABLE} PRIVATE "-g")

set(CMAKE_CXX_FLAGS_DEBUG "{CMAKE_CXX_FLAGS} -o0 -ggbd")  #添加语句
add_compile_options(-g)   #添加语句

# 添加测试
add_test(NAME AllTests COMMAND ${TEST_EXECUTABLE})

# 可选：设置工作目录
set_tests_properties(AllTests PROPERTIES WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR})