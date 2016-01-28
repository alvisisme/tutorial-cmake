# 第七步

## 支持指示板
支持将我们提交的测试结果加入指示板是非常简单的，我们已经在这教程的前面几步中为我们项目定义了许多的测试，我们只需要运行这些测试，并将它们提交到指示板。为了支持指示板功能，我们在最顶层的CMakeLists文件中引入了CTest模块：
```
# enable dashboard scripting
include (CTest)
```
我们也创建了一个CTestConfig.cmake文件，在这里我们可以指定项目指示板的名字。
```
set (CTEST_PROJECT_NAME "Tutorial")
```
当CTest运行的时候会读取这个文件，你可以在项目中运行CMake来创建一个简单的指示板，改变二进制树的目录，然后运行ctest -D Experimental。指示板的结果会上传到Kitware的公开的指示板上，就在[这里](https://open.cdash.org/index.php?project=PublicDashboard)
