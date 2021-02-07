👍推荐 在线阅读 (Github 访问速度比较慢可能会导致部分图片无法刷新出来)  
[参考cmake官网教程](https://cmake.org/cmake/help/latest/guide/tutorial/index.html#)
## 目录
- [CMakeLists带注释的demo](#CMakeLists带注释的demo)
- [CMake 指令详解](#CMake 指令详解)
- [1.语法示例](#1)
## CMake 指令详解  
- PART 1 前期文件准备  
将前期的版本控制，编译选项，源文件等准备好。
    - **CMAKE_MINIMUN_REQUIRED(VERSION 2.6)**  
    cmake最小版本控制要求。  
  
    - **PROJECT(TestDemo)**  
    设置此项目的名称，并且会赋值给系统变量*${PROJECT_NAME}*,后面直接用PROJECT_NAME就可以。  
  
    - **OPTION(TEST_DEBUG "option for debug" OFF)**  
    设置选项开关，在执行cmake命令*(cmake .. TEST_DEBUG=ON)*这个函数有三个参数：  
                              第一个参数：设置选项名称，例如TEST_DEBUG,  
                              第二个参数：解释该开关作用，只做解释，类似注释，  
                              第三个参数：开关的默认值，这里OFF或者ON都是布尔型。  
                              
    - **set(CMAKE_BUILD_TYPE Debug)**  
    指定编译类型，debug或者relaase，这两则区别就不过多介绍。  
  
    - **CMAKE_CXX_FLAGS&&CMAKE_C_FLAGS**  
    指定编译类型，例如*set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11 -g -wall")*设置编译选项。  
  
    - **include_directories()**  
    添加头文件，通过该函数可以加入依赖的头文件  
    *include_directories([AFTER|BEFORE] [SYSTEM] dir1 [dir2 ...])  
    #例如：  
    include_directories(
      "${CMAKE_CURRENT_SOURCE_DIR}/include"
    )*
    
    - **file()**  
    文件操作，包括文件的读写等，  
      - WRITE:file(WRITE filename "message towrite"... ) **WRITE** 将一则信息写入文件’filename’中，如果该文件存在，它会覆盖它，如果不存在，它会创建该文件。  
      - APPEND:file(APPEND filename "message to write"... ) **APPEND** 如同WRITE，区别在于它将信息内容追加到文件末尾。  
      - READ:file(READ filename variable [LIMIT numBytes] [OFFSEToffset] [HEX]) **READ** 会读取文件的内容并将其存入到变量中。它会在给定的偏移量处开始读取最多numBytes个字节。如果指定了HEX参数，二进制数据将会被转换成十进制表示形式并存储到变量variable中。  
      - STRINGS:按照一定的规则读取，目前感觉我是没用到。  
      - GLOB:file(GLOB variable [RELATIVE path] [globbingexpressions]...).**GLOB**主要是用于收集源码树，GLOB 会产生一个由所有匹配globbing表达式的文件组成的列表，并将其保存到变量中。Globbing 表达式与正则表达式类似，但更简单。globbing 表达式包括：  
        *.cxx     - match all files with extension cxx  
        *.vt?      - match all files with extension vta,...,vtz  
        f[3-5].txt - match files f3.txt,f4.txt, f5.txt  
        实例：  
        *file(GLOB TD_SOURCE  
        "${CMAKE_CURRENT_SOURCE_DIR}/src/*"  
        )*  
        <font color=#FF0000 >注：</font>关于file的操作还有很多，例如根据url下载资源，删除/移动资源等等，这里本着快速使用cmake，所以高阶玩法就不介绍，比较这不是手册。

    - **link_directories()**  
    添加依赖库文件，例如：  
   *link_directories(  
        "${CMAKE_CURRENT_SOURCE_DIR}/lib"  
      )*  
      
- **Part 2 生成文件**  
常用的生成文件有两种：一种是可执行文件，一种是库（动/静态库）
  - **生成可执行文件**  
  调用函数*add_executable(${PROJECT_NAME} $TD_src)*,该函数有两个入参：第一个参数直接使用系统变量PROJECT_NAME,这个变量在PRPOJEC的时候就直接被赋值了；第二个参数是编译可执行程序所依赖的源文件，在*file(GLOB ...)*中定义的。  
    
  - **生成库文件**  
  不管是动态库还是静态库，统一用*add_library(<name> <SHARED|STATIC> <源代码>)*函数生成，用法*add_executable()*差不多，只是第二个参数用来区别动态库还是静态库。  
    
  - **添加链接库**  
  最后还需要链接一下所依赖的库，使用函数*target_link_libraries(<name> <lib>)*,第一个参数同样是工程名字，可以用*${PROJECT_NAME}*,第二个参数是库名字。例如*target_link_libraries(${PROJECT_NAME} memleak)*,注意这里链接的是libmemleak.so的库，但是在函数里面只需要写上memleak就行。
  
- **Part 3 安装文件**  
  当文件生成后，需要安装到我们指定目录，不管是安装可执行文件，库文件还是头文件等，都可以通过install来完成。  
  
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

#指定编译类型，debug 或者为 release
# debug 会生成相关调试信息，可以使用 GDB 进行
# release 不会生成调试信息。当无法进行调试时查看此处是否设置为 debug.
set(CMAKE_BUILD_TYPE Debug)

# 指定编译器
# CMAKE_C_FLAGS_DEBUG          ----  C 编译器
# CMAKE_CXX_FLAGS_DEBUG        ----  C++ 编译器
# -std=c++11  使用 C++11
# -g：只是编译器，在编译的时候，产生调试信息。
# -Wall：生成所有警告信息。一下是具体的选项，可以单独使用
set (CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11   -g  -wall  ")

#生成可执行文件target ，后面填写的是生成此可执行文件所依赖的源文件列表。
add_executable(target target_source_codes) 

# 设置一个名字var_name 的变量，同时给此变量赋值为var_value
SET(var_name var_value)

# 打印消息
MESSAGE("MSG") 

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

file dmeo ```
message(STATUS "current dir: ${CMAKE_CURRENT_SOURCE_DIR}")  
file(WRITE test1.txt "Some messages to Write\n" )  
file(APPEND test1.txt "Another message to write\n")  
file(READ test1.txt CONTENTS LIMIT 4 OFFSET 12)  
message(STATUS "contents of test1.txt is: \n ${CONTENTS}")  
file(MD5 ${CMAKE_CURRENT_SOURCE_DIR}/test1.txt HASH_CONTENTS)  
message(STATUS "hash contents of test1.txt is: \n ${HASH_CONTENTS}")  
file(STRINGS test1.txt PARSED_STRINGS)  
message(STATUS "\n strings of test1.txt is: \n ${PARSED_STRINGS}")  
file(GLOB files RELATIVE ${CMAKE_CURRENT_SOURCE_DIR} "*.*")  
message(STATUS  "files: ${files}")  
file(MAKE_DIRECTORY dir1 dir2)  
file(RENAME dir2 dir3)  
file(REMOVE dir3)  
file(REMOVE_RECURSE dir3)  
file(RELATIVE_PATH relative_path ${PROJECT_SOURCE_DIR} ${CMAKE_CURRENT_SOURCE_DIR}/test1.txt)  
message(STATUS "relative path : ${relative_path}")  
file(TO_CMAKE_PATH "$ENV{PATH}" cmake_path)  
message(STATUS "cmake path: ${cmake_path}")  
file(TO_NATIVE_PATH "/usr/local/sbin;/usr/local/bin" native_path)  
message(STATUS "native path: ${native_path}")  
file(DOWNLOAD "http://www.baidu.com" ${CMAKE_CURRENT_SOURCE_DIR}/index.html SHOW_PROGRESS)  
file(COPY test1.txt DESTINATION ${CMAKE_CURRENT_SOURCE_DIR}/dir1)  
file(INSTALL test1.txt DESTINATION ${CMAKE_CURRENT_SOURCE_DIR}/dir1)
```
<h2 id="1">1.语法示例</h2>

