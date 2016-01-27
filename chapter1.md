# 第一步

## 一个基本的出发点

一个最基本的项目是从源代码文件中构建的可执行的工程。对简单的项目来说，只需要一个两行的CMakeLists文件，这就是这个教程的基本出发点。CMakeLists文件如下：
```
cmake_minimum_required (VERSION 2.6)
project (Tutorial)
add_executable(Tutorial tutorial.cxx)
```

需要注意的是，这个例子在CMakeLists文件中使用了小写命名，大写，小写和大小写混合的命令CMake都是支持的。tutorial.cxx的源代码将计算一个数字的平方根，第一个版本非常简单，如下：
```
// A simple program that computes the square root of a number
#include <stdio.h>
#include <stdlib.h>
#include <math.h>
int main (int argc, char *argv[])
{
  if (argc < 2)
    {
    fprintf(stdout,"Usage: %s number\n",argv[0]);
    return 1;
    }
  double inputValue = atof(argv[1]);
  double outputValue = sqrt(inputValue);
  fprintf(stdout,"The square root of %g is %g\n",
          inputValue, outputValue);
  return 0;
}
```

## 加入版本号和配置好的头文件
我们第一个要加入的功能是给我们的可执行文件和项目一个版本号。尽管你可以在源代码里面专门地这么做，但是在CMakeLists文件中可以更灵活。我们可以像下面这样修改CMakeLists文件加入版本号：
```
cmake_minimum_required (VERSION 2.6)
project (Tutorial)
# The version number.
set (Tutorial_VERSION_MAJOR 1)
set (Tutorial_VERSION_MINOR 0)

# configure a header file to pass some of the CMake settings
# to the source code
configure_file (
  "${PROJECT_SOURCE_DIR}/TutorialConfig.h.in"
  "${PROJECT_BINARY_DIR}/TutorialConfig.h"
  )

# add the binary tree to the search path for include files
# so that we will find TutorialConfig.h
include_directories("${PROJECT_BINARY_DIR}")

# add the executable
add_executable(Tutorial tutorial.cxx)
```
配置的文件会被写入到二进制树中，所以我们必须把它们的目录加入到头文件搜索路径列表。我们可以在源码树中创建一个TutorialConfig.h.in文件，内容如下：
```
// the configured options and settings for Tutorial
#define Tutorial_VERSION_MAJOR @Tutorial_VERSION_MAJOR@
#define Tutorial_VERSION_MINOR @Tutorial_VERSION_MINOR@
```
当CMake配置这个头文件时， @Tutorial_VERSION_MAJOR@ 和 @Tutorial_VERSION_MINOR@ 的值会被CMakeLists文件中的值替换掉。接下来我们修改tutorial.cxx，将配置头文件引入，使用我们的版本号。源代码结果如下：
```
// A simple program that computes the square root of a number
#include <stdio.h>
#include <stdlib.h>
#include <math.h>
#include "TutorialConfig.h"

int main (int argc, char *argv[])
{
  if (argc < 2)
    {
    fprintf(stdout,"%s Version %d.%d\n",
            argv[0],
            Tutorial_VERSION_MAJOR,
            Tutorial_VERSION_MINOR);
    fprintf(stdout,"Usage: %s number\n",argv[0]);
    return 1;
    }
  double inputValue = atof(argv[1]);
  double outputValue = sqrt(inputValue);
  fprintf(stdout,"The square root of %g is %g\n",
          inputValue, outputValue);
  return 0;
}
```
主要的变化是引入了TutorialConfig.h头文件，还有打印了版本号作为使用方法消息的一部分。
