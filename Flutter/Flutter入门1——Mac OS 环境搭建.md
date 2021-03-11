# Flutter SDK 下载地址

进入https://flutter.dev/docs/get-started/install/macos下载 Mac 版本 Flutter SDK并将其解压，例如：`/Users/ooyao/flutter`。



# 配置环境变量

使用`vim ~/.bash_profile`命令编辑配置文件，并输入下面内容。

```
# Android SDK
export ANDROID_HOME=/Users/ooyao/Library/Android/sdk
export PATH=$PATH:$ANDROID_HOME/tools:$ANDROID_HOME/platform-tools
# Flutter 国内镜像
export PUB_HOSTED_URL=https://pub.flutter-io.cn
export FLUTTER_STORAGE_BASE_URL=http://storage.flutter-io.cn
export PATH=/Users/ooyao/flutter/bin:$PATH  
```



`注意，上面的 ANDROID_HOME和最后的 PATH要替换成自己本机的对应目录。`



按`ESC`进入命令模式，输入`:wq`保存退出`vim`。再执行`source $HOME/.bash_profile`使其配置生效。



# flutter doctor

`flutter docto`命令可以检查当前环境，并给出报告，例如：

<img src="/Users/ooyao/Library/Application Support/typora-user-images/image-20210311142248680.png" alt="image-20210311142248680" style="zoom:50%;" />



# 安装 Android Studio 插件





`Android Studio - Preferences - Plugins` 搜索`Flutter和 Dart`两个插件并安装。

<img src="/Users/ooyao/Library/Application Support/typora-user-images/image-20210311142847874.png" alt="image-20210311142847874" style="zoom:50%;" />

<img src="/Users/ooyao/Library/Application Support/typora-user-images/image-20210311142929481.png" alt="image-20210311142929481" style="zoom:50%;" />

安装之后重启`Android Studio`即可创建 `Flutter`工程了。



































