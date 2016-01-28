# 第六步

## 构建安装器
接下来假设我们想要将我们的项目发布给别人使用，我们希望在各种平台上同时提供二进制和源码发行版，这和我们之前安装和测试一节（第三步）有点不同，那时我们是安装已经从源码构建好的二进制文件。在这个例子中，我们会构建支持二进制安装的安装包，还支持出现在cygwin，debian，RPMs等中的包管理功能。为了实现这个功能，我们将使用CPack来创建平台相关的安装器，就像使用CPack来打包一章中描述的那样。具体来说，我们在最顶层的CMakeLists文件的底部加入几行代码：
```
# build a CPack driven installer package
include (InstallRequiredSystemLibraries)
set (CPACK_RESOURCE_FILE_LICENSE  
     "${CMAKE_CURRENT_SOURCE_DIR}/License.txt")
set (CPACK_PACKAGE_VERSION_MAJOR "${Tutorial_VERSION_MAJOR}")
set (CPACK_PACKAGE_VERSION_MINOR "${Tutorial_VERSION_MINOR}")
include (CPack)
```
这就是全部的工作了，我们从包含InstallRequiredSystemLibraries开始，这个模块将会引入当前平台下项目所有需要的运行库，接下来我们为CPack设置在哪里存放项目的证书和版本信息的变量。版本信息使用了之前我们在教程中设置的变量，最后我们包含使用这些变量的CPack模块和一些系统属性来安装这个安装器。
下面一步就是用一般的方法构建这个工程然后运行CPack，为了构建一个二进制发行版你需要运行:
```
cpack --config CPackConfig.cmake
```
为了创建一个源码发行版，你要输入：
```
cpack --config CPackSourceConfig.cmake
```
