## CMake 笔记
-------
From [Link](http://www.hahack.com/codes/cmake/)   
From [PDF](https://github.com/yaozhongxiao/BuildSystem/blob/master/cmake/docs/CMake%20Practice.pdf)

### 编译流程
- 编写CMakeLists.txt，如果多个目录，则每个目录下创建一个
- 执行cmake PATH生成akefile，PATH为CMakeLists.txt所在路径
- make

### 变量
> 对变量的引用一般使用${}，但在IF等语句中则是直接使用变量名
- PROJECT_BINARY_DIR 编译发生的位置
- <projectname>_BINARY_DIR
- PROJECT_SOURCE_DIR 工程代码的位置，工程顶层目录
- <projectname>_SOURCE_DIR
- CMAKE_CURRENT_SOURCE_DIR 当前处理的子文件夹路径
- CMAKE_CURRENT_BINARY_DIR 如果是in-source，则与CMAKE_CURRENT_SOURCE_DIR相同，如果是out-of-source则是target编译目录
- CMAKE_CURRENT_LIST_FILE 输出调用这个变量的CMakeLists.txt的完整路径
- CMAKE_CURRENT_LIST_LINE 输出这个变量所在的行
- CMAKE_MODULE_PATH cmake模块所在的路径
- EXECUTABLE_OUTPUT_PATH 最终生成的目标存储位置
- LIBRARY_OPUTPUT_PATH 最终生成的共享库存储的位置
- PROJECT_NAME
- ENV{NAME} 调用系统的环境变量
- BUILD_SHARED_LIBS 开关控制默认的库编译方式，不设置时默认编译生成的库都是静态库
- CMAKE_C_FLAGS C编译选项，可以通过add_definitions添加
- CMAKE_CXX_FLAGS C++编译选项，可以通过add_definitions添加

### 内部构建与外部构建
是否直接在当前工程项目（有CMakeLists.txt）的路径下执行cmake，如果新建一个空的文件夹如build并在其中运行cmake则称之为外部构建（out-of-source），所产生的文件都在build中

### 命令
> 命令不区分大小写   
> 使用#进行注释

#### cmake_minimum_required (VERSION 2.8)
设置最低版本为2.8

#### project (hello)
项目名称hello，可指定项目类型

#### set (CMAKE_BUILD_TYPE "Debug")
设置变量，一般变量，缓存变量，环境变量赋值

#### message (STATUS "a messaget" ...)
打印信息，其余类型SEND_ERROR、FATAL_ERROR

#### add_executable (hello ${DIR_SRCS})
指定目标名以及源文件

#### add_source_directory (<dir> <variable>)
将文件夹下所有源文件添加到指定变量，比如(. DIR_SRCS)

#### add_subdirectory (math)
存在子文件夹math，添加math路径进行访问\

#### include_directories (/inc)
向工程添加多个特定的头文件搜索路径

#### add_dependencies (target-name depend-t1 depend-t2)
定义目标的其他依赖项

#### add_library (MathFunctions ${DIR_LIB_SRCS})
将源文件生成名为MathFunctions的链接库，可选SHARED动态库、STATIC静态库、MODULE在使用dyld的系统有效，不支持则视为SHARED，EXCLUDE_FROM_ALL参数表示这个库不会杯默认构建，除非有其他的组件对其有所依赖或者手工构建

#### target_link_libaries (hello MathFunctions)
添加链接库

#### configure_file ("${PROJECT_SOURCE_DIR}/config.h.in")
添加配置头文件，用于cmake对源码的设置

#### option (USE_MYMATH "Use provided math implementation" ON)
添加一个选项USE_MYMATH，默认为ON，根据选项来确定是否使用自己的MathFunctions库
```
	if (USE_MYMATH)
	  include_directories ("${PROJECT_SOURCE_DIR}/math")
	  add_subdirectory (math)
	  set (EXTRA_LIBS ${EXTRA_LIBS} MathFunctions)
	endif (USE_MYMATH)
```

#### add_definitions (-DENABLE_DEBUG)
向编译器添加宏定义

#### install (TARGETS MathFunctions DESTINATION bin)
#### install (FILES MathFunctions.h DESTINATION include)
指定MathFunctions库的安装路径，使生成的obj和head file复制到特定文件夹，如linux环境下，使用make install命令后，两者将分别被复制到`/usr/local/bin`和`/usr/local/include`中

#### enable_test ()
启用测试

#### add_test (test_run hello 5 2)
添加测试，使用make test即可使用CTest工具进行测试

#### add_test (test_5_2 hello 5 2)
#### set_tests_properties (test_5_2 PROPERTIES PASS_REGULAR_EXPRESSION "is 25")
测试是否输出是否包含"is 25"，可以使用宏来简化编写测试用例
```
	macro (do_test arg1 arg2 result)
	  add_test (test_${arg1}_${arg2} hello ${arg1} ${arg2})
	  set_tests_properties (test_${arg1}_${arg2}
	    PROPERTIES PASS_REGULAR_EXPRESSION ${result})
	endmacro (do_test)

	do_test (5 2 "is 25")
	do_test (10 5 "is 100000")
	do_test (2 10 "is 1024")
```

#### include (${CMAKE_ROOT}/Modules/CheckFunctionExists.cmake)
添加cmake文件或者CMakeLists.txt，如果是一个cmake模块则将在CMAKE_MODULE_PATH中所以并载入

#### exec_program (ls SRGS "\*.c" OUTPUT_VARIABLE LS_OUTPUT RETURN_VALUE)
执行ls \*.c
```
	IF (not LS_RVALUE)
	MESSAGE (STATUS "ls result: " ${LS_OUTPUT})
	ENDIF (not LS_RVALUE)
```
#### find_ (<VAR> name1 path1 path2 ...)
查询系列得其全路径
- find_file
- find_library
- find_path
- find_program
- find_package 查询预定义CMKAE_MODULE_PATH下的cmake模块
```
	find_package (CURL)
	if (CURL_FOUND)
		include_directories (${CURL_INCLUDE_DIR})
		target_link_libraries (hello ${CURL_LIBRARY})
	endif (CURL_FOUND)
	# <name>_FOUND
	# <name>_INCLUDE_DIR
	# <name>_LIBRARY 均会自动生成
```

#### if (condition)
if需要与endif配套使用

#### while (confition)
while与endwhile配套使用

#### foreach (loop_var arg1 arg2 ...)
foreach与endforeach配套使用
```
	add_source_directory (. SRC_LIST)
	foreach (f ${SRC_LIST})
		message (${f})
	endforeach (f)
	foreach (v RANGE 10)
		message (${f})
	endforeach (v)
```

### 可自定义模块生成库

### 原理
> [Link](https://blog.csdn.net/ztemt_sw2/article/details/81384538)
