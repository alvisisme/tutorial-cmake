# 第四步

##增加系统自检
接下来让我们考虑加入一些依赖目标平台可能没有的特性的代码到我们的项目中。举个例子，我们会加入一些代码，依赖于目标平台是否拥有log和exp函数。当然几乎每个平台上都有这些函数，但是假设这个教程中他们不是很普遍的。如果平台有log函数那么我们就会在mysqrt函数中计算平方根。我们先要在最顶层的CMakeLists文件中使用CheckFunctionExists.cmake宏来测试一下这些函数的可用性，像下面这样：
```
# does this system provide the log and exp functions?
include (CheckFunctionExists)
check_function_exists (log HAVE_LOG)
check_function_exists (exp HAVE_EXP)

```
然后我们像下面这样修改TutorialConfig.h文件来定义那些值，如果CMake在平台上找到它们的话：
```
// does the platform provide exp and log functions?
#cmakedefine HAVE_LOG
#cmakedefine HAVE_EXP
```
log和exp函数的测试在对TutorialConfig.h文件使用configure_file命令之前完成是很重要的，在CMake中，configure_file命令会马上使用当前的设置配置该文件，最后在mysqrt函数里我们就能使用下面的代码根据log和exp是否在系统中可用提供一个可选的实现。
```
// if we have both log and exp then use them
#if defined (HAVE_LOG) && defined (HAVE_EXP)
  result = exp(log(x)*0.5);
#else // otherwise use an iterative approach
  . . .
```
