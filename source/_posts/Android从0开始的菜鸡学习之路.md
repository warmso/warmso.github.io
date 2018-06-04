title: Android从0开始的菜鸡学习之路
author: howard
tags:
  - 0基础
  - 客户端
  - Android
categories:
  - Android
  - 客户端
date: 2018-06-04 16:05:00
---
开头先说几句：

* 我很久没有更新这个博客了，好不容易搭建的差不多，还没有来的及修改一些细节部分，不过还好，不影响使用，之后有空了再改吧。后面会把一些学习的历程、学习笔记或者一些乱七八糟的感想什么的等等东西更新上来，应该也没有什么人来看吧~
* 这个Android的学习历程其实并不是我学客户端开发的开头——虽然博客里之前也并没有写关于其他的学习历程。。。我其实之前学的是iOS开发，OC写了挺久，最近也在接触Swift，本来是做好了做一个正经的iOS开发实习生（虽然我从来没有过iPhone），但是呢，今天实习公司的导师加了微信，我才得知我去了之后是要做Android的。一开始有点懵，我没写过这个东西呀，Java也掌握的很一般很一般，这意味着我有很多东西需要学习。那，如果我实习留不下呢？我写了一个月的Android然后回来匆匆忙忙复习iOS再投秋招的iOS岗位么？有点纠结，也很难抉择。
* 后来一想，反正实习还是要去的，那我现在开始了解一些开发Android相关的东西，去了以后能调岗位就最好，不能调的话就尽量争取留下来吧。也问了一些学长的看法作为参考。这不过就是一次挑战而已，总不可能事事如意，这个就当是一个学习能力的挑战吧，能不能在固定的一段时间里学到尽量多的东西。反正iOS的学习基本上也到了瓶颈期，不如，换换口味？
* 再者说，我也算个安卓机的爱好者吧，因为穷我用的从来都是安卓机，也很早开始折腾安卓系统，root、刷系统、玩xposed……算是从安卓2.x一直到8.1的体验者了。
* 塞翁失马，焉知非福
* 那就这么开始学习安卓吧

# 开始前的准备
## 教材
我用的是学长推荐的《第一行代码Android》
## 运行环境
用了很久的黑苹果10.12.6，AS最新版，不想用模拟器，就用了我刷了8.1的一加2

# HelloWorld
新建了一个只有一个Activity的项目，它自己就写了“hello，world” 所以其实不算是我自己写的。
但是比起新建一个EmptyActivity什么都没有一脸懵逼来说好很多了
## 项目文件夹结构
### .gradle 和 .idea
自动生成的文件，不用管
### app
主要文件都在这里
#### build 
也是编译生成的文件，更多更杂，不用管
#### libs
第三方包的文件夹，会自动加到构建的路径里
#### src
这个文件里的东西是编写的时候需要关注的文件夹，需要自己写的内容基本上都在这里面
##### androidTest
可以在这里写测试用例，可以自动化测试
##### main
基本上java文件就都在这里了
###### java
java文件就在这个文件夹下，比如刚才创建的Activity.java
###### res 
项目里要用到的所有图片（drawable）、布局（layout）、字符串（values）等
###### AndroidManifest.xml
这个是整个项目的配置文件，在程序里定义的所有的四大组件都需要在这里注册，另外还可以写权限声明，这个要经常用到
##### test
又是一个测试，先不管
##### .gitignore
git忽略的文件
##### app.imi
不用管
##### build.gradle
app这个模块的构建脚本
##### proguard-rules.pro
用于指定混淆规则，厉害了，用于防止代码被破解
### build
编译时自动生成的文件，不用管
### gradle
包含了gradle wrapper的配置文件，默认不启用这个方式
### gitignore
git的忽略文件
### build.gradle
全局gradle构建脚本，gradle是一个自动化构建脚本，一般不用改
### gradle.properties
全局gradle配置文件，会影响整个项目的编译
### gradlew和gradlew.bat
是用来执行gradle的，不用管
### XXX.imi
是这个IDE的存在感的标识，不用管
### local.properties
指定本机的Android SDK的路径，是自动生成的一般不用改
### setting.gradle
用来指定项目中所有引入的模块，目前为止一共就用了app这么一个模块，所以现在还就只写了
```
include ':app'
```

** 好了，关于文件目录就是这些，总结起来就是外层的文件夹什么的基本上先不用管，要注意的就是app这个文件夹里的 **

## 如何运行起来的
先看Android-Manifest.xml里做了什么
### Android-Manifest.xml
里面有一部分代码长这样：
```
<activity android:name=".MainActivity">
	<intent-filter>
    	<action android:name="android.intent.action.MAIN" />
        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>
</activity>
```
指定了点击应用之后首先启动哪个Activity，这里当然是MainActivity这个类
那MainActivity这个类里面做了什么操作呢？
### MainActivity.java
```
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
    }
}
```
这个类是一个继承自AppCompatActivity的类，这个类类似于iOS里的ViewController
这个类的onCreate方法是一个Activity被创建的时候一定会执行的方法（类似于iOS里的ViewDidLoad）
这里并没有看到“hello world”的字样。
Android编程讲究逻辑和视图分离，所以setContentView这个方法引入了一个布局文件（res/layout）

### activity_main.xml
这个文件里面有一部分是这样的：
```
<TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Hello World!"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toTopOf="parent" />
```
表示布局里添加了一个TextView的控件，设置了ndroid:text="Hello World!"所以会显示出Hello World。这个控件类似于iOS里的UILable

** 这样就大致搞清楚了HelloWorld是怎么运行起来的，并且各种文件是怎么组织起来的。其实和iOS类似的地方很多，AndroidManifest.xml有些类似APPDelegate，Activity类似于ViewController，activity_main.xml类似于xib，但是之前都是直接用代码写的布局类，所以这种的还需要适应一下 **
