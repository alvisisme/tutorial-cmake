# 第二步

## 加入库
现在我们要在项目中加入一个库，这个库将会包含我们自己实现的计算一个数平方根的方法，之后可执行文件可以使用这个库来代替编译器提供的标准的平方根函数。在这份教程里我们将这个库放在一个叫MathFunctions的子目录中，这个库的CMakeLists文件如下：
```
add_library(MathFunctions mysqrt.cxx)
```
源文件mysqrt.cxx有一个叫mysqrt的函数，提供了编译起的sqrt函数的类似功能。为了使用新的库，我们在最顶层的CMakeLists文件中增加了一个add_subdirectory调用，这样这个库才会被构建。我们还增加了一个头文件目录来让MathFunctions/mysqrt.h头文件能够被作为函数原型查找到。最后一个改变是把新的库加入到可执行文件中。最顶层的CMakeLists文件的最后几行看起来像如下这样：
```
include_directories ("${PROJECT_SOURCE_DIR}/MathFunctions")
add_subdirectory (MathFunctions)

# add the executable
add_executable (Tutorial tutorial.cxx)
target_link_libraries (Tutorial MathFunctions)
```
现在让我们考虑下让MathFunctions库称为可选的。在这篇教程里，没有任何理由需要这样做，但是对于更大的库或者那些依赖第三方库代码的库，你可能希望这么做。第一步就是在最顶层的CMakeLists文件中加入一个选项。
```
# should we use our own math functions?
option (USE_MYMATH
        "Use tutorial provided math implementation" ON)
```
这会在CMake的GUI图形界面中显示默认值打开ON，用户可以根据需要改变这个值。这个设置将会存储在缓存中，这样用户就不需要每次在项目中运行CMake的时候都去设置它。下一个要改变的是要有条件的构建和链接MathFunctions库，为此我们需要改变最顶层的CMakeLists文件,就像下面这样：
```
# add the MathFunctions library?
#
if (USE_MYMATH)
  include_directories ("${PROJECT_SOURCE_DIR}/MathFunctions")
  add_subdirectory (MathFunctions)
  set (EXTRA_LIBS ${EXTRA_LIBS} MathFunctions)
endif (USE_MYMATH)

# add the executable
add_executable (Tutorial tutorial.cxx)
target_link_libraries (Tutorial  ${EXTRA_LIBS})
```
它使用了USE_MYMATH的设置来决定MathFunctions是否被编译和使用，注意一个变量（这里指EXTRA_LIBS）的使用，聚集了所有可选的库以便之后被链接进可执行文件中。这是一种常见的用来保持拥有许多可选组件的更大的工程整洁的方法。源代码相对应的改动相当的直接，像下面这样：
```
// A simple program that computes the square root of a number
#include <stdio.h>
#include <stdlib.h>
#include <math.h>
#include "TutorialConfig.h"
#ifdef USE_MYMATH
#include "MathFunctions.h"
#endif

int main (int argc, char *argv[])
{
  if (argc < 2)
    {
    fprintf(stdout,"%s Version %d.%d\n", argv[0],
            Tutorial_VERSION_MAJOR,
            Tutorial_VERSION_MINOR);
    fprintf(stdout,"Usage: %s number\n",argv[0]);
    return 1;
    }

  double inputValue = atof(argv[1]);

#ifdef USE_MYMATH
  double outputValue = mysqrt(inputValue);
#else
  double outputValue = sqrt(inputValue);
#endif

  fprintf(stdout,"The square root of %g is %g\n",
          inputValue, outputValue);
  return 0;
}
```
在源代码中我们也可以使用USE_MYMATH，这是通过增加如下一行到TutorialConfig.h.in配置文件由CMake提供给源代码的：
```
#cmakedefine USE_MYMATH
```
