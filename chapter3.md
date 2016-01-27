# 第三步

##安装和测试
下一步我们将为我们的项目增加安装规则和测试支持，安装规则相当简单，针对MathFunctions库，我们通过在MathFunctions的CMakeLists文件中增加下面两行来安装这个库和头文件：
```
install (TARGETS MathFunctions DESTINATION bin)
install (FILES MathFunctions.h DESTINATION include)
```
对于应用来说，下面几行要加到最顶层的CMakeLists文件中来安装可执行文件和配置的头文件：
```
# add the install targets
install (TARGETS Tutorial DESTINATION bin)
install (FILES "${PROJECT_BINARY_DIR}/TutorialConfig.h"        
         DESTINATION include)
```
这就是全部内容了，现在你应该可以构建这个教程了，然后输入make install（或者从某个IDE里构建INSTALL的目标），它将会安装合适的头文件，库和可执行文件。CMake变量CMAKE_INSTALL_PREFIX被用来决定文件安装的根目录。增加测试也是一个相当简单的过程，在最顶层的CMakeLists文件中我们可以许多基本的测试来验证程序在正常工作。
```
include(CTest)

# does the application run
add_test (TutorialRuns Tutorial 25)

# does it sqrt of 25
add_test (TutorialComp25 Tutorial 25)

set_tests_properties (TutorialComp25
  PROPERTIES PASS_REGULAR_EXPRESSION "25 is 5")

# does it handle negative numbers
add_test (TutorialNegative Tutorial -25)
set_tests_properties (TutorialNegative
  PROPERTIES PASS_REGULAR_EXPRESSION "-25 is 0")

# does it handle small numbers
add_test (TutorialSmall Tutorial 0.0001)
set_tests_properties (TutorialSmall
  PROPERTIES PASS_REGULAR_EXPRESSION "0.0001 is 0.01")

# does the usage message work?
add_test (TutorialUsage Tutorial)
set_tests_properties (TutorialUsage
  PROPERTIES
  PASS_REGULAR_EXPRESSION "Usage:.*number")

```
构建之后就可以运行`ctest`命令行工具来运行测试。
第一个测试简单的验证了程序是否运行，没有出现段错误或崩溃，并且返回0值。这是一个CTest测试的基本样式。接下来的几个测试都使用了PASS_REGULAR_EXPRESSION的测试属性来验证测试的输出包含特定的字符串。在这个例子里，就是验证计算出来的平方跟的确是应该的那个值，或者当提供了不正确数量的参数后打印使用方法信息。如果你想增加很多的测试来测试不同的输入值，你可能要考虑像下面一样创建一个宏：
```
#define a macro to simplify adding tests, then use it
macro (do_test arg result)
  add_test (TutorialComp${arg} Tutorial ${arg})
  set_tests_properties (TutorialComp${arg}
    PROPERTIES PASS_REGULAR_EXPRESSION ${result})
endmacro (do_test)

# do a bunch of result based tests
do_test (25 "25 is 5")
do_test (-25 "-25 is 0")
```
每次do_test的调用，根据传入的参数，另一个带有名字，输入和结果的测试就会被加入到项目中，
