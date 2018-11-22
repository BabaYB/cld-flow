## CMake 笔记
-------
From [Link](http://www.hahack.com/codes/cmake/)

### 编译流程
- 编写CMakeLists.txt
- 执行cmake PATH生成akefile，PATH为CMakeLists.txt所在路径
- make

### 变量


### 命令
> 命令不区分大小写
> 使用#进行注释

#### cmake_minimun_required (VERSION 2.8)
设置最低版本为2.8

#### project (hello)
项目名称hello

#### set (CMAKE_BUILD_TYPE "Debug")
设置变量，一般变量，缓存变量，环境变量赋值

#### add_executable (hello ${DIR_SRCS})
指定目标以及源文件

#### add_source_directory (<dir> <variable>)
将文件夹下所有源文件添加到指定变量，比如(. DIR_SRCS)

#### add_subdirectory (math)
存在子文件夹math，添加math路径进行访问

#### add_library (MathFunctions ${DIR_LIB_SRCS})
将源文件生成名为MathFunctions的链接库

#### larget_link_libaries (hello MathFunctions)
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
添加cmake文件
