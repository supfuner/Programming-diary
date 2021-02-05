👍推荐 在线阅读 (Github 访问速度比较慢可能会导致部分图片无法刷新出来)
## 目录
- [CMakeLists带注释的demo]()
- [CMake 指令详解]()

## CMake 指令详解
- CMAKE_MINIMUN_REQUIRED(VERSION 2.6)  
cmake最小版本控制要求


## CMakeLists带注释的demo

```CMakelists.txt
#cmake最小版本需求
CMAKE_MINIMUN_REQUIRED(VERSION 2.6)

#设置此项目的名称,并且赋值给系统变量${PROJECT_NAME}
PROJECT(TestDemo) 

#设置选项开关，例如cmake .. -TEST_DEBUG=ON,那么就需要里面设置好开关
OPTION(TEST_DEBUG "option for debug" OFF)
if(TEST_DEBUG)
  xxx
endif()

#生成可执行文件target ，后面填写的是生成此可执行文件所依赖的源文件列表。
add_executable(target target_source_codes) 

# 设置一个名字var_name 的变量，同时给此变量赋值为var_value
SET(var_name var_value)

# 指定编译器
# CMAKE_C_FLAGS_DEBUG          ----  C 编译器
# CMAKE_CXX_FLAGS_DEBUG        ----  C++ 编译器
# -std=c++11  使用 C++11
# -g：只是编译器，在编译的时候，产生调试信息。
# -Wall：生成所有警告信息。一下是具体的选项，可以单独使用
set (CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11   -g  -wall  ")

#指定编译类型，debug 或者为 release
# debug 会生成相关调试信息，可以使用 GDB 进行
# release 不会生成调试信息。当无法进行调试时查看此处是否设置为 debug.
set(CMAKE_BUILD_TYPE Debug)

# 打印消息
MESSAGE("MSG") 

#给变量var_name赋值为var_value，comment是此变量的注释，和SET 有类似的功效，用于给某变量设置默认值
option(var_name "comment" var_value) 

# 添加include路径，也就是头文件路径
include_directories(xxx) 

# 调用xxx子目录的CMakeLists.txt执行
add_subdirectory(xxx) 

# 给编译器添加xxx参数
add_compile_options(xxx)

# 给编译器添加库目录，
link_directories(xxx)

# 生成库文件，SHARED代表动态库，STATIC代表静态库， 最后一个参数代表此库的源文件列表
add_library(lib_name SHARED or STATIC lib_source_code) 

# 给目标添加依赖库
target_link_libraries(target_name lib_name ...)
```

