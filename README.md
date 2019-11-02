# Demo-1
将library打成jar包

Android studio打包生成Jar包并混淆
开发过程中往往会引用到第三方的jar包或者library或者so文件，那么怎样将自己的一些文件打成jar包供别人使用或者自己使用呢，还有那些文件中的jar中可能会涉及到一些重要的接口和方法，又怎样混淆呢？今天小冷总结一下如何将library打包成jar包并做混淆处理

准备好自己将要打包的library

图一中的library是我即将打包的library里面含有两个工具类文件


图一
在mylibrary.gradle文件中做混淆打包的配置

因为IDE环境问题 classes.jar生成的位置也不同否则构建工程时候会报错，这边小冷得批评下谷歌

* What went wrong
Cannot expand ZIP 'mylibrary\build\intermediates\bundles\release\classes.jar
Android Studio3.1.2之前的配置

def SDK_BASENAME = "myjar";
def SDK_VERSION = "_v1.0.0";
def sdkDestinationPath = "build/outputs/jar/";
def zipFile = file('build/intermediates/bundles/release/classes.jar')
task deleteBuild(type: Delete) {
    delete sdkDestinationPath + SDK\_BASENAME + SDK\_VERSION + ".jar"
}
task makeJar(type: Jar) {
    from zipTree(zipFile)
 from fileTree(dir: 'src/main', includes: \['assets/**'\]) // 打包assets目录下的所有文件

 baseName = SDK\_BASENAME + SDK\_VERSION
    destinationDir = file(sdkDestinationPath)
}
makeJar.dependsOn(deleteBuild, build)

Android Studio3.1.2之后的配置

def SDK_BASENAME = "myjar";
def SDK_VERSION = "_v1.0.0";
def sdkDestinationPath = "build/outputs/jar/";
def zipFile = file('build/intermediates/packaged-classes/release/classes.jar')
task deleteBuild(type: Delete) {
    delete sdkDestinationPath + SDK\_BASENAME + SDK\_VERSION + ".jar"
}
task makeJar(type: Jar) {
    from zipTree(zipFile)
 from fileTree(dir: 'src/main', includes: \['assets/**'\]) // 打包assets目录下的所有文件

 baseName = SDK\_BASENAME + SDK\_VERSION
    destinationDir = file(sdkDestinationPath)
}
makeJar.dependsOn(deleteBuild, build)

如下图：


图二
在proguard-rules.pro文件中配置混淆文件

proguard-rules.pro混淆文件地址点我查看

在Terminal命令窗口下输入打包命令：

$ gradlew makeJar
直到下面生成BUILD SUCCESSFUL的提示就打包成功了。我刚开始在输入这个gradlew makeJar命令时候多次报 找不到 Tool.jar的错误，没办法我只能重装了一遍jdk（泪奔）然后顺利解决。最后就神奇在output文件夹下发现一个jar文件。如下图


图三
引用自己的jar测试能不能用

引用后发现系统已经将里面的参数做了混淆 如下图


图四
小冷在打jar包时候，发现多次打了个空包。应该是全部都被混淆忽略掉了，此时需要留一个入口class文件不可混略。在生成Jar时可能会出现各种奇葩的错误，只要根据错误提示来修改就没啥问题。
