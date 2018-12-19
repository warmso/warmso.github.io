title: 《Android第一行代码》读书笔记（上）
author: LeoChang
tags:
  - 0基础
  - 客户端
  - Android
categories:
  - Android
  - 客户端
  - 读书笔记
date: 2018-06-04 16:05:00
---
很久没有更新这个博客了，好不容易搭建的差不多，还没有来的及修改一些细节部分，不过还好，不影响使用，之后有空了再改吧。后面会把一些学习的历程、学习笔记或者一些乱七八糟的感想什么的等等东西更新上来，应该也没有什么人来看吧~

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

** 大致过程大概就是，先在AndroidManifest.xml配置Activity，然后在布局文件里面写界面相关的代码，然后在Activity.java里面写对布局里面的控件的操控，也是类似MVC的经典的逻辑分离的设计方法。 **

# 从0开始自己构建一个APP
前面的HelloWorld是人机自动生成的，这一次开始自己从0开始写一个，选择Empty，这时候没有Acitvity，所以不会有显示的。
## 新建一个Activity
在app/src/main/java/com.example.ifan.activitytest右键从new里面选择一个EmptyActivity。这里新建的Activity是会自动在AndroidManifest.xml里面自动添加注册的代码的，但是不会自动设置为最先启动后展示的第一个页面，所以还需要在activity的标签里手动添加：
```
<intent-filter>
	<action android:name="android.intent.action.MAIN"/>
    <category android:name="android.intent.category.LAUNCHER"/>
</intent-filter>
```
这样，新添加的acticity就是启动页了。
## 新建页面布局文件
虽然已经有了一个空的Activity，但是还没有东西可以显示。
在res文件夹立新建一个layout文件夹，用来存放布局文件，然后新建一个Layout resources file，是一个xml格式的文件，在这里写布局：
```
<Button
        android:id="@+id/button_1"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Button 1"
        />
```
添加了一个button，然后在标签里写的id是这个空间的唯一标示，layout_width和layout_height是控件的大小（match_parent是屏幕宽度，wrap_content是刚好能容纳控件内容的高度），text就是button上面显示的文字

## 在Activity里操作布局里面的控件
刚才已经建好了一个button，我们可以在Activity里面操作这个控件：
```
setContentView(R.layout.first_layout);
Button button1 = (Button) findViewById(R.id.button_1);
button1.setOnClickListener(new View.OnClickListener() {
	@Override
    public void onClick(View view) {
    Toast.makeText(FirstActivity.this,"You Clicked Me!",Toast.LENGTH_SHORT).show();
    }
});
```
* 第一行的setContentView是把布局文件添加到当前Activity里面，R.layout.xxx是在引用res里面的layout文件夹里面的资源文件。
* 第二行从布局文件里面获取一个View，然后显式转换为button类型赋值给一个button类的实例变量。
* 第三行setOnClickListener是对button实例变量调用的方法（监听器），传入的一个参数为新建的View.onClickListener，然后对这个setOnClickListener方法进行覆写。
* 后面的onClick是button监听器里的默认会执行的单击方法，所以在这个方法里写Toast
* Toast.makeText（）是一个静态方法，传入（上下文，文本内容，显示时长），然后调用show（）方法显示出来

### Activity的基本使用
刚才已经使用了Toast，还有一个常用的控件Menu
#### Menu
* 首先在res文件夹下新建文件夹menu文件夹，再在menu文件夹下新建Menu resource file
* 在这个文件里添加：
```
 	<item
        android:id="@+id/add_item"
        android:title="Add"
        />
    <item
        android:id="@+id/remove_item"
        android:title="Remove"
        />
```
在Menu的布局里添加了两个item，分别设置它们的id和title

* 回到Activity.java的文件里，重写onCreateOptionsMenu（）方法，先getMenuInflater（）获取到一个菜单展开器，然后调用inflate把资源文件里的menu展开
```
	@Override
    public boolean onCreateOptionsMenu(Menu menu) {
        getMenuInflater().inflate(R.menu.main,menu);
        return true;
    }
```
* 返回值表示是否允许创建的菜单显示出来
上面的代码能够将menu显示出来，如何响应按钮的item?

* 覆写：
```
@Override
public boolean onOptionsItemSelected(MenuItem item) {
    switch (item.getItemId())
    {
        case R.id.add_item:
        	Toast.makeText(FirstActivity.this,"You clicked item named add",Toast.LENGTH_SHORT).show();
            break;
        case R.id.remove_item:
            Toast.makeText(FirstActivity.this,"You clicked item named remove",Toast.LENGTH_SHORT).show();
            break;
    }
    return true;
}
```
通过switch判断不同的item的id去做不同的动作响应

#### Intent
Intent不仅是各组件中交互的重要方式，可以指明当前组件想要执行的动作，还可以在组件之间传递数据。
** Intent 一般可以用于启动活动、启动服务、发送广播等场景 **
##### 显式Intent
* 在某一个触发事件里可以创建一个Intent的实例：
```
Intent intent = new Intent(FirstActivity.this , SecondActivity.class);
```
第一个参数提供启动活动的上下文，第二个参数是目标活动
* Activity类提供了一个方法，专门用于启动活动，接受一个intent参数
```
startActivity(intent);
```
至于如何返回（即销毁当前的Activity），可以手动绑定方法去执行finish（），也可以是手机上的返回键

##### 隐式Intent
隐式的用法是通过AndroidManifest.xml中的Activity的配置相关信息，然后在Activity.java中调用符合配置的Activity，而不是直接指定某个Activity的类。
下面举例：
```
<activity android:name=".SecondActivity">
	<intent-filter>
    	<action android:name="com.example.activitytest.ACTION_START"/>
        <category android:name="android.intent.category.DEFAULT"/>
    </intent-filter>
</activity>
```
然后在FirstAcitvity.java里面写的和显式的不同：
```
Intent intent1 = new Intent("com.example.activitytest.ACTION_START");
startActivity(intent1);
```
直接写为Activity支持的action名。
** 另外，每个intent只可以对应一个action，但是可以对应多个category，只有当category和action同时满足范围的时候才可以正常调用，否则会崩溃并抛出异常 **

其实还有别的隐式intent的用法，比如跨应用唤起某个页面：
```
Intent intent1 = new Intent(Intent.ACTION_VIEW);
intent1.setData(Uri.parse("http://google.com"));
startActivity(intent1);
```
这样就可以唤起浏览器并打开http://google.com
ACTION_VIEW是安卓系统内置的动作，然后Uri.parse（）是将字符串解析为Uri对象，最后用setDdata（）把Uri对象传入。

除此之外，还可以在<intent-filter>中配置 data 标签，实现更精确地响应：
```
<activity android:name=".SecondActivity">
	<intent-filter>
        <action android:name="android.intent.action.VIEW"/>
        <category android:name="android.intent.category.DEFAULT"/>
        <data android:scheme=""/>
        <data android:host=""/>
        <data android:port=""/>
        ...
    </intent-filter>
</activity>
```
这里的action的值配置为；鹅Intent.ACTION_VIEW的常量值，同时data字段也配置了可以响应的协议类型等字段，假如我们设置的scheme字段是http，那么用户在打开一个http网页的时候，会提示用户选择我们的这个Activity打开。

安卓系统内部其实有很多中intent.ACTION的类型，有唤起地理位置界面的、拨号的等等。

#### 页面间传值
Intent不仅可以用来启动Activity，还可以在页面跳转同时传值

Intent提供了一系列对putExtra（）的重载，可以在调度Activity的同时把数据暂存在Intent中，到达了另外一个Activity的时候，再由目的Activity把数据从Intent中取出就可以了。
```
intent2.putExtra("string_from_firstActivity", "hello second");
```
上面的这是设值
```
Intent intent = getIntent();
Log.v("SecondActivty",intent.getStringExtra("string_from_firstActivity"));
```
这是取值

这种适合用来做单向正向传值。除了这种之外还有反向传值：
```
startActivityForResult(intent2,1);
```
这个方法能够期望SecondActivity销毁时能够返回结果给FirstActivity。
然后需要在FirstActivity中覆写onActivityResult方法：
```
@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    switch (requestCode){
        case 1:
            if (resultCode==RESULT_OK){
                Log.v("FirstActivity",data.getStringExtra("back_to_first"));
            }
    }
```
通过对Intent data调用getStringExtra方法得到返回回来的数据。、
requestCode是前面写的数字。用来标示从FirstACtivity跳转到的不同Activity
resultCode是SecondActivity返回数据时传入的参数。用来标示是否处理成功。
data就是携带的数据。

对于SecondActivity来说，只要设值，然后调用setResult（）就可以了
```
intent.putExtra("back_to_first","hello,first");
setResult(RESULT_OK,intent);
```

除此之外，还可以直接监听到返回键的事件来处理响应的事件：
```
@Override
public void onBackPressed() {
Toast.makeText(SecondActivity.this,"onBackPassed",Toast.LENGTH_SHORT).show();
}
```
### Activity的生命周期

#### 返回栈
Android也是用栈的方式去管理Activity，用Task去管理，返回栈是BackStack
#### 活动的四个状态
每个Activity有四种状态：
##### 运行状态
最栈顶的Activity就是处于运行状态的，系统最不喜欢回收这种状态的Activity
##### 暂停状态
因为并不是所有的Activity都是全屏状态，所以会存在一个Activity是可见的，但是上面有一个小的Activity部分遮挡，这样一来，后面的Activity既不在栈顶（非运行态），又是可见的。这种状态就是暂停状态。一般系统也是不考虑去回收这种状态的Activity，只有在内存极低的时候才有可能被系统回收。
##### 停止状态
如果一个活动在栈中，并且完全不可见的时候，就处于停止状态，系统会保存Activity的状态和成员变量。这个时候它并不是完全可靠的，当内存不足的时候就可能被系统回收。
##### 销毁状态
如果一个Activity从返回栈中被移除掉的话，它就会成为销毁状态，系统最优先回收这种状态的Activity。

#### 活动在三种生存期的七个回调方法

##### 完整生存期
从onCreate（）开始到onDestory（）结束
###### onCreate（）
每个Activity中都重写这个方法，它会在每个Activity创建的时候调用，所以这个方法中应该完成一些例如加载布局文件、绑定事件等初始化操作。
###### onDestory（）
这个方法在Activity被销毁之前调用，调用完成之后Activity就进入销毁状态

##### 可见生存期
Activity从onStart（）到onStop（）之间的部分就是可见生存期。可见生存期的可见二字就意味着Activity是显示的，就算不能交互，也要算在内。
开发者就可以通过在onStart（）方法中加载资源，在onStop（）方法中释放资源，来保证停止状态的Activity不会占用过多的资源。
###### onStart（）
这个方法在Activity由不可见变成可见的时候调用
###### onStop（）
相反的，当Activity变得**完全**不可见的时候调用

##### 前台生存期
在onResume（）方法和onPause（）方法之间的Activity就是前台生存期。这时候Activity子那个是处于运行状态，可以与用户交互
###### onResume（）
这个方法是Activity已经准备好和用户交互的时候调用。这个时候的Activity在栈顶并且一定是运行状态。
###### onPause（）
这个方法是系统准备去启动某个Activity或者恢复到某个Activity的时候调用，这个方法中一般用于把一些消耗系统资源的释放掉，或者保存一些关键数据，但是这个方法里执行的不能是一些耗时操作。
**如果是一个对话框出现在某个Activity上面的时候，如果没有完全遮挡，那么会调用onPause（）而不是onStop（）**

除此之外，还有一个独立在这三种生存期之外的回调方法：
###### onRestart（）
这个方法是Activity在由停止状态重新变成运行状态的时候调用的。

下面附一张Activity的生命周期图（书上原图）：
![Activity的生命周期](https://s1.ax1x.com/2018/06/07/CH124K.jpg)

下面做一个测试用于FirstActivity到SecondActivity然后再跳转回来的一个状态跟踪打印：
```
06-07 14:45:16.457 16551-16551/com.example.ifan.activitytest V/life: onCreate is called
06-07 14:45:16.736 16551-16551/com.example.ifan.activitytest V/life: onStart is called
06-07 14:45:16.742 16551-16551/com.example.ifan.activitytest V/life: onResume is called
06-07 14:45:29.731 16551-16551/com.example.ifan.activitytest V/life: onPause is called
06-07 14:45:30.536 16551-16551/com.example.ifan.activitytest V/life: onStop is called
06-07 14:46:24.588 16551-16551/com.example.ifan.activitytest V/life: onRestart is called
06-07 14:46:24.590 16551-16551/com.example.ifan.activitytest V/life: onStart is called
06-07 14:46:24.591 16551-16551/com.example.ifan.activitytest V/life: onResume is called
06-07 14:46:41.625 16551-16551/com.example.ifan.activitytest V/life: onPause is called
06-07 14:46:42.003 16551-16551/com.example.ifan.activitytest V/life: onStop is called
06-07 14:46:42.004 16551-16551/com.example.ifan.activitytest V/life: onDestory is called
```
可以看出来从FirstActivity到SecondActivity的时候，经过了onPause（）和onStop（）的方法的调用；从SecondActivity回到FirstActivity的时候，经过了onRestart（）、onStart（）和onResume（）的调用


再做一个部分遮挡的例子：SecondActivity是一个对话框式的Activity时候，从FirstActivity到SecondActivity打印出来的是：
```
06-07 14:56:10.743 17244-17244/com.example.ifan.activitytest V/life: onCreate is called
06-07 14:56:11.011 17244-17244/com.example.ifan.activitytest V/life: onStart is called
06-07 14:56:11.019 17244-17244/com.example.ifan.activitytest V/life: onResume is called
06-07 14:56:14.760 17244-17244/com.example.ifan.activitytest V/life: onPause is called
06-07 14:56:24.734 17244-17244/com.example.ifan.activitytest V/life: onResume is called
```
可以看到这种情况少了onStop（）、onRestart（）和onStart（）的调用过程。

另外还可以看到在按返回键的时候的调用过程如下：
```
06-07 15:22:38.832 18596-18596/com.example.ifan.activitytest V/life: onPause is called
06-07 15:22:39.261 18596-18596/com.example.ifan.activitytest V/life: onStop is called
06-07 15:22:39.262 18596-18596/com.example.ifan.activitytest V/life: onDestory is called
```
看到有博客说按home、返回、在后台杀进程的调用过程会有差异。
另外在onStop（）的覆写中如果不写对父类方法的调用会导致Activity直接被回收，下一次还需要重新create。也很好理解，因为不调用父类方法的话其实就少了状态、变量的保存过程，所以会出现这样的结果。

#### Activity在停止状态被回收的情况
Activity在活动状态是可能被回收的，也就是可能存在以下场景：
A跳转到B，然后A进入停止状态，但是A被销毁了，这时候从B回来就不会调用onRestart（）方法，而是调用onCreate（）方法。这时候因为A在onStop（）方法里写入的数据也随着之前停止状态的Activity被销毁了，所以这时候从B回去之后看到的A就是一个新创建的A了。
怎么解决呢？
Activity提供了一个onSaveInstanceState（）的回调方法，这个方法能够保证在Activity被Destory之前一定会被执行。所以我们可以在在这里保存A的Stop数据。

这个onSaveInstanceState（）携带一个Bundle类型的参数，Bundle提供了一系列用于保存数据的方法。
```
@Override
protected void onSaveInstanceState(Bundle outState) {
	outState.putString("FirstActivityDataOut","help~");
    super.onSaveInstanceState(outState);
}
```
然后在onCreate（）方法中携带的Bundle类型的参数里获取这个Bundle中的数据就可以了。
```
if(savedInstanceState.getString("FirstActivityDataOut")!=null)
	Log.v("FirstActivityDataOut",savedInstanceState.getString("FirstActivityDataOut"));
```
除此之外，在Intent的使用中也可以使用Bundle来传值。

### Activity的四种启动模式
四种启动模式是在AndroidManifest.xml里面的Activity对应的标签里面指定的。
#### standard
标准模式，也就是默认模式，在一个Activity建好的初始设置就是这个，每次启动一个新的Activity的时候，它就在返回栈中入栈，处于栈顶位置。这样的话，当此Activity递归地跳转到本类的Activity，栈中也会不停地新建、入栈。
#### singleTop
栈顶单例，顾名思义，处于栈顶的Activity在进行上面的操作的时候，会被直接使用本activity，而不是不断新建、入栈。但是前提条件必须是：** 当前的Activity必须处于栈顶。**

**一个有意思的现象是使用startActivityForResult（）去启动这个singleTop的Activity的时候，是无视它的singleTop模式的，google这样设计的原因可能是考虑到回调的内容没有接受者**
#### singleTask
上一种的singleTop是只检查栈顶，避免了单个Activity的递归启动。除此之外还可以让某个Activity在整个返回栈中成为唯一的一个Activity。
例如：

A->B->C->D->E->B

如果B的启动类型为singleTask，那么在从E到B的时候实际上是回到了最前面B，而且最前面的B之后的都会被从栈里弹出。

#### singleInstance
这个是不同于上面的三种的一种特殊的启动方式——这种启动模式的Activity会启用一个另外的返回栈去管理这个活动。应用场景就是当别的程序要共享访问这个Activity，其他的三种模式就不能完成了。（每个程序都有自己的返回栈，如果访问了这个Activity，那必定是在当前的程序和别的那个程序里面都会分别加入一个新建的这个Activity，那就是不同的实例了，达不到共享的目的。）

而这个singleInstance就是解决这种情况的——**它会连同Activity一块返回一个单独的返回栈，然后任何一个应用来访问这个Activity，都公用同一个返回栈。**
例如：

A -> B -> C

如果B的启动模式为singleInstance的话，实际的栈是这样的：
A — C
B
第一个栈是本程序自己的栈，因为A、C都是普通的模式，所以都默认入栈到程序自己的默认栈里了。而第二个栈就是随着B一同创建的新栈，这个栈是所有程序调用时公用的。

所以返回键连续按下之后，先是C所在的栈先出栈C，然后是A，因为是同一个栈，最后才是B，因为程序自己所在的栈已经空了，就轮到B所在的栈出栈了。

### 一些实践
了解了Activity的基础知识后，这里还有一些关于Activity的应用技巧。
#### 获知当前所处Activity
当拿到别人的代码时候，可能就不方便知道当前的界面是处于哪一个Activity了，一个个对应也很麻烦，所以有一个办法去获知当前所处的Activity：
使用间接继承的原理，新建一个中间类BaseActivity继承自AppCompatActivity，在onCreate（）的方法中扩展一个打印当前类的语句：
```
Log.v("BaseActivity",getClass().getSimpleName());
```
然后让所有的Activity都继承自这个BaseActivity，然后就可以显示出当前类的类名了。
**但是同时也有一个让人没有办法接受的缺点：难道我拿到别人的代码要把所有的Activity的继承类都修改一遍么？！**

#### 退回到任意Activity
用一个List去手动维护一个返回栈，从而能够对这个返回栈的Activity手动操控，就可以返回到任意Activity：
```
public static boolean backToIndexOfStack(int index){
        if (index>=activityList.size()){
            return false;
        }
        for (int i = activityList.size()-1;i > index;i--){
            activityList.get(i).finish();
            activityList.remove(activityList.size()-1);
        }
	return true;
}
```
index从0开始，这里依次从栈里finsh（）掉Activity，并且从手动维护的栈中remove掉。
#### 启动Activity的最佳写法
在多人协作开发的时候，协定传参总是一个要考虑的点。可以在启动Activity的时候封装一个activityStart（）方法，专门用来构建Intent，这样一来参数就在这个activityStart（）方法的参数列表里体现出来了。
```
	public void activityStart(Context context,String firstName,String lastName){
        Intent intent = new Intent(context,SecondActivity.class);
        intent.putExtra("param1","hello");
        intent.putExtra("param2","there");
        startActivity(intent);
    }
```
# Android UI的开发
前面的一节是从探究Activity的角度去了解Activity的用法和特点。接下来就是学习如何编写UI，结合Activity去编写完整的UI已经交互逻辑。

## 常用控件的使用
### TextView
这个控件类似于iOS里面的UILabel，用于展示文字信息。
```
    <TextView
        android:id="@+id/text_view"
        android:text="This is a TextView"
        android:gravity="center"
        android:layout_width="match_parent"
        android:layout_height="wrap_content" />
```
android:layout_width、android:layout_height这两个属性是所有控件里面都有的，就像iOS里面的Frame一样用来设置大小，但是这个只是表示了大小并不明位置。
后面的值可选三种值：
* match_parent：表示让当前的控件和父布局的大小一样大。
* fill_parent：和match_parent的意义一样，现在官方推荐使用match_parent。
* wrap_content：表示让控件的大小刚好能包含控件中展示的内容，控件大小由控件内容决定。

当然了，也可以手动指定固定值作为width、height的值，但是这样会导致适配出现问题。
 
**gravity**：指定的是对齐方式，和iOS里TextAlignment类似。可选的有top、bottom、left、right、center。（center是默认指定"center_vertical|center_horizontal"的，表示垂直和水平方向都居中）

**textSize**：指定文字大小。在Android里字体大小是使用sp作单位的。其实在打出24这个将要指定大小的数字的时候，自动补全了几个选项：dp、in、mm、pt、px、sp。好奇这几个有什么区别。之后我会另外开一个文去写这个的。
**textColor**：#+6位16进制数表示颜色。

更多的使用细节去查文档就好。

### Button
它在布局文件里的写法大同小异，它有一个textAllCaps属性，如果不设置为false的话默认为true就会把所有出现的字母都变为大写的。
然后就可以在Activity的类里面去获取这个控件了：
```
		Button button1 = (Button) findViewById(R.id.button_1);
        button1.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                //点击之后的代码
            }
        });
```
这是一种类似闭包的方式，其实还有一种通过实现接口的方式，需要在本类的声明部分写明实现哪一个接口，然后在设置button的监听者为本类，然后覆写onClick（）方法，方法内部通过判断id来分别实现不同button的点击事件。

### EditText
这个控件实际上就是iOS里的TextView，支持多行输入和排版。
特别的属性：

**hint**：这一个是iOS里面的TextView里面没有的类似于placeHolder的设置。

**maxLines**：EditText如果高度不是定值，就会随着输入内容的增多而拉长。如果指定了这个属性，则EditText不会被拉长，但是内容视图的长度还是会被拉长。而且，它只是限制了EditText这个控件的最大显示行数，并没有限制内容的行数。

另外，还可以在Activity里面获取到EditText里面的文字信息：
```
EditText editText = findViewById(R.id.edit_text);
editText.getText().toString()
```
### ImageView
这个倒是和iOS里的UIImageView差别不大,不过现在资源大多都同意放在res目录下的drawable目录下，但是这个目录没有制定具体的分辨率，所以书上的建议是新建一个drawable-xhdpi文件夹，然后把图片放进去。

对于图片的设置有两种方法，一种是直接写在布局文件里，一种是在Activity类里去修改。这两种可以同时使用。

**有意思的是，Android的图片文件并不能带大写字母，不然会报错说只支持小写字母、数字和下划线。**

同时对文件名里带“.”的文件也不能支持，会报错。


**另外，在一开始设置的height为wrap_content其实也没有起作用。**
### ProgressBar
显示一个进度条（默认样式是一个不断循环转动的圈圈）。布局文件里写的是默认可见的（当然也可以手动指定为其他值），就可以在Activity里去判断它当前是否是可见的，如果是可见的，可以设为不可见。

在布局文件里设置:
```
        android:visibility="invisible"
```
在Activity里设置：
```
		if (findViewById(R.id.progress_bar).getVisibility() ==View.VISIBLE){
            findViewById(R.id.progress_bar).setVisibility(View.INVISIBLE);
        }

```
对于ProgressBar来说有三种可见选项：visible、invisible、gone

**visible**：可见的。

**invisible**：不可见，但是还占据着原来的位置和大小，可以理解为透明状态。

**gone**：不仅是不可见的，而且完全从屏幕上消失。

除了上面的那种圈圈的样式，还有一种样式是条状的。可以通过在布局文件里设置style为：
```
style="?android:progressBarStyleHorizontal"
```
对于这种样式，可以设置一个最大值，以便在Activity中动态的改变进度条的状态。

布局文件：
```

android:max="100"
```
Activity：
```
ProgressBar progressBar = findViewById(R.id.progress_bar);
progressBar.setProgress(progressBar.getProgress()+10);
```
### AlertDialog
弹出一个对话框，一般用于警告用户，下方有三个按钮可以响应用户的点击事件
```
AlertDialog.Builder dialog = new AlertDialog.Builder(FirstActivity.this);
dialog.setTitle("this will change image");
dialog.setMessage("this is very importent");
dialog.setCancelable(true);

dialog.setNeutralButton("NeutralButton", new DialogInterface.OnClickListener() {
    @Override
    public void onClick(DialogInterface dialogInterface, int i) {}
});
dialog.setNegativeButton("Cancle", new DialogInterface.OnClickListener() {
    @Override
    public void onClick(DialogInterface dialogInterface, int i) {}
});
dialog.setPositiveButton("OK", new DialogInterface.OnClickListener() {
	@Override
    public void onClick(DialogInterface dialogInterface, int i) {}
});

dialog.show();
```
可以在示例代码中看到，使用一般是三个步骤，先用Builder新建一个AlertDialog实例出来（指定Activity），然后设置标题、信息内容、可否在点击别的空白处取消这个AlertDialog。

然后分别设置三个按钮（从左到右依次为NeutralButton、NegativeButton、PositiveButton）的点击回调事件。

### ProgressDialog
和上面的那个AlertDialog很类似，也可选两种样式——转圈圈或者一个条状的进度条。
```
ProgressDialog progressDialog = new ProgressDialog(FirstActivity.this);
progressDialog.setTitle("progressDialog");
progressDialog.setMessage("this is a progressDialog");
progressDialog.setCancelable(true);
progressDialog.setProgressStyle(ProgressDialog.STYLE_HORIZONTAL);
progressDialog.setMax(256);
progressDialog.show();
```
这种的是和前面的ProgressBar是一样的，可以设置最大的长度、动态改变进度状态。
默认是转圈的样式。

**需要注意的是，如果这个也设置为不允许失焦取消，那不知道何时会结束的progressDialog就会导致当前应用卡死，无法继续交互。如果设置为允许失焦消失，那就要处理好消失的逻辑，是否是用户选择取消当前进度？如果误操作怎么处理？如果是取消应该处理当前进度的任务？**

## 四种基本布局
布局是一种可用于放置很多控件的容器。布局是可以嵌套的。可以通过布局的嵌套来完成一些复杂的布局效果。这一部分将用一个新的项目去学习和练习。
### 线性布局-LinearLayout
是一种非常常用的布局，这种布局会将内部包含的控件在线性方向上依次排列。
还记得最开始接触布局文件么？那个就是默认的线性布局（因为是根元素），有横向和竖向两种。

可以通过orientation来指定线性到底是竖向还是横向。
```
android:orientation="horizontal"
android:orientation="vertical"
```
**需要注意的是，这时候控件的size就要设置合理。比如水平线性布局的时候，控件的宽度就不能match_parent了**

layout的几个重要属性：
#### layout_gravity
和gravity属性有点相似，但是其实完全没有关系。

**gravity**属性是指定某个控件内部的内容（如文字）在控件内是靠向哪一边的。

**gravity_layout**是指定当前控件在父布局中的位置是在如何对齐的。
同样的，在父布局已经处于某种方式的时候要做合理的设置，例如：父布局是水平布局（horizontal），只有设置控件在竖向位置的对齐（top、center、bottom等）才会生效。

#### layout_weight
这个属性是用比例的方式去指定控件的大小的，有一些自动布局的感觉。
需要区别的是控件的width属性，虽然可能长的像，但是二者是完全不同的：

**width**：用数值或者一些定义过的常量或者宏指定宽度。

**weight**：用比例去指定当前控件在水平方向上占用多少比例。

当weight存在的时候，width是不生效的，也就是随便写多少都没用，但是比较规范的写法是写为0dp。（dp是Android里用于指定控件大小、间距的单位）

然后weight里写的是比例，如果控件1里面写的是1，另一个控件里写的2，那么就表示两个控件的宽度比例是1:2。

有的控件里没有写weight，只写了width为wrap，这时候就还是保证控件的宽度刚够包含控件自己的内容。

如果控件1写了weight的值为1，控件2没有写weight但是写了width为wrap，那就是控件1尽量宽，然后控件2的宽度刚够包含自己的内容。

### 相对布局：RelativeLayout
这种布局也是非常常用的布局，这种布局更随意一些，它可以用相对定位的方式让控件出现在布局的任意位置。

正因如此，相对布局的属性就多了一些，但是好在都是有规律的。
```
	<Button
        android:text="hello3"
        android:layout_height="wrap_content"
        android:layout_width="wrap_content"
        android:layout_alignParentEnd="true"
        android:layout_alignParentTop="true"
        />
```
可以看到，里面的位置相关的代码里面有Parent字段，也就是说，这些控件是相对于父布局来定位的。当然了也可以不依据父布局进行定位：
```
	<Button
        android:layout_height="wrap_content"
        android:layout_width="wrap_content"
        android:layout_above="@+id/text_view"
        android:layout_toLeftOf="@+id/text_view"
        android:text="hello1"
        />
```
可以通过id去选定相对的参考控件，去选择相对位置。
**但是只能去参考前面出现过的控件，不然就找不到id了。**

另外还有layout_alignLeft表示和目标控件左对齐，其他的其他方向都是一样的。

### 帧布局 FrameLayout
这种布局非常简单，相比前面的两种来说，使用场景也较少。

这种布局没有方便的定位方式，所有的控件都会默认摆放在布局的左上角。多个控件按照默认方式放置会重叠在一起。

当然，也可以用layout_gravity属性去指定控件的对齐方式，但是比起前面的几种来说还是太简单了，导致实际的应用场景也不是那么多。
### 百分比布局
安卓引用了一种全新的布局方式来解决线性布局不够强大的比例式布局的缺点，这种布局中，可以不再用wrap_content、match_parent等方式去指定控件的大小，而是直接指定控件在布局中所占的百分比，这样就可以轻松实现任意比例分割布局了。

百分比布局对FrameLayout和RelativeLayout进行了功能扩展，提供了PercentFrameLayout和PercentRelativeLayout的全新布局。

为了让这种布局方式能够兼容就的安卓版本上使用，这种布局定义在support库中，只需要在项目的build.gradle中添加百分比布局的依赖保证兼容性。
```
<android.support.percent.PercentFrameLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">


    <TextView
        android:id="@+id/text_view"
        android:layout_gravity="start"
        app:layout_widthPercent="50%"
        app:layout_heightPercent="50%"
        android:text="hello~"
        />
    

</android.support.percent.PercentFrameLayout>
```
这里的app并没有补全，想来应该是因为这个并不在内置在系统的SDK中的，所以没有自动补全。

**这种布局有一个大坑：写50%的时候总是先写50再写%，写这里的时候我写了50然后手抖后面多跟了个0，成了500，然后就直接卡死，告诉我Out of Memory。其实也很好解释，因为AndroidStudio是可以自动预览布局的，所以这里还来不及修改的时候，已经自动按照500去算百分比了，1是100%，那么500就是50000%，所以就内存不够了。**

对于另一种PercentRelativeLayout，它继承了RelativeLayout的所有属性并且加入了app:layout_widthPercent和app:layout_heightPercent来指定控件的宽高。

### 约束布局——ConstraintLayout
因为书上的几种布局方式比较老，其实在现在的AndroidStudio里面新建一个project的时候，已经默认设置为这种约束布局了，所以换句话说，这个应该重点学习，它被默认支持一定是有它的优点的。

之前一直是用代码去编写布局，是因为前面的几种布局对于用代码的表达更好一些。但是归根结底来说，布局这种的用可视化去编辑是最直观最合适的，所以这个就是为了解决这种问题的一种新的布局方式。（这个书的作者也说了，当时在写书的时候这个布局方式刚出来，就没有加入到书里。）

这种布局方式是使用控件的相对位置去布局的，有点类似相对布局，但是比相对布局还要强大。

这种布局同时还解决了传统的几种布局的嵌套问题。传统布局要实现复杂的布局往往需要多重嵌套，但是多重嵌套会降低性能，所以这种布局应对复杂布局的时候可能会更好。

这种布局暂时看到的教程都说适合用Design界面去拖拽完成布局操作，所以这里就先不写了。

## 自定义控件
和iOS一样，Android里的控件也都是直接或者间接的继承自View，所有的布局都是直接或间接继承自ViewGroup。View是Android里的最基本的UI组件。ViewGroup是一种特殊的View，可以包含很多子View和ViewGroup，是用于防止控件和布局的容器。

需要接触几个前面没有接触到的属性：

**background**：为布局或者控件指定一个背景，可以使颜色或者图片。

**margin**：指定控件在四个方向的偏移量，也可以用margin_left等指定单一某个方向上的偏移量。

先写好某一个自定义布局，然后在另一个布局里面引用这个布局就可以了。

title.xml：
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content">

    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:layout_margin="5dp"
        android:text="Back"
        />
    <TextView
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:layout_weight="1"
        android:gravity="center"
        android:text="title"
        android:textSize="24sp"
        />
    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:layout_margin="5dp"
        android:text="edit"
        />

</LinearLayout>
```

activity_miain.xml：
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <include layout="@layout/title"/>

</LinearLayout>
```

这个的实现效果就是自定义了个ActionBar并且加载到主布局中。

## 最常用、最难用的控件——ListView
这里的ListView就类似于iOS里面的UITableView，重要性不用多说。

先从最简单的用法开始：
### ListView的简单用法
先在布局里面写一个ListView进去，这里先写好大小和id就可以。

然后在Activity里面写一个和这个listView相关的实例，把它的适配器指定为某个适配器的实例就好了：
```
ListView listView = findViewById(R.id.list_view);
ArrayAdapter<String> adapter = new ArrayAdapter<String>(
	MainActivity.this,
    android.R.layout.simple_list_item_1,
    data
    );
listView.setAdapter(adapter);
```
这个适配器只是众多适配器中的一种，这个据说是最好用的，可以通过泛型来指定钥匙胚的数据类型，然后在构造函数里传参进去。

ArrayAdapter有很多重载，这里是因为用到的数据都是String类型，所以这里写 了String。然后依次传入当前的上下文、listView的id，以及具体的数据数组。

这里的android.R.layout.simple_list_item_1相当于是iOS里面UITableView的style，可以有很多种style，根据具体的使用场景自行切换。

### 自定义ListView的界面
自定义的过程和iOS的自定义cell的基本上相似，都是新建一个布局，然后让ListView去加载这个布局作为item，然后把构造好的数据展示到界面上就好了。

* 先新建一个布局：
```
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content">

    <ImageView
        android:id="@+id/fruit_image"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:background="@drawable/ic_launcher_background"/>

    <TextView
        android:id="@+id/fruit_textview"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center_vertical"
        android:layout_margin="10dp"/>
</LinearLayout>
```
这个就是cell（也就是item）的展示布局。

* 新建一个容器类
这个容器类就是作为ListView的适配器类型：
```
public class Fruit {
    private String name;
    private int imageId;

    public Fruit(String name,int imageId){
        this.name = name;
        this.imageId = imageId;
    }

    public String getName() {
        return name;
    }

    public int getImageId() {
        return imageId;
    }
}
```
这个部分就相当于model类的部分，用来存储ListView将来要展示的数据。

* 新建一个适配器类
```
public class FruitAdapter extends ArrayAdapter<Fruit> {
    private int resourceId;
    public FruitAdapter(Context context, int textViewResourceId, List<Fruit>object){
        super(context,textViewResourceId,object);
        resourceId = textViewResourceId;
    }

    @NonNull
    @Override
    public View getView(int position, @Nullable View convertView, @NonNull ViewGroup parent) {
        Fruit fruit = getItem(position);
        View view = LayoutInflater.from(getContext()).inflate(resourceId,parent,false);
        ImageView fruitImage = (ImageView)view.findViewById(R.id.fruit_image);
        TextView fruitName=(TextView)view.findViewById(R.id.fruit_textview);
        fruitImage.setImageResource(fruit.getImageId());
        fruitName.setText(fruit.getName());
        return view;
    }
}
```
可以看到这个适配器类是继承自ArrayAdapter的，声明了一个私有属性resourceId，然后在这个类的构造函数里传入三个参数：上下文、textViewResourceId、和一个model类的List对象。调用父类的方法，对私有属性赋值。

然后需要重写一个父类的方法getView（），这个方法就相当于iOS里面的cellForRowAtIndex，在每次item要出现在屏幕上的时候就会调用。


这个getView传入了三个参数：position相当于是index，因为前面在构造函数里面已经设置过了List对象，所以这个方法是把数组中当前要处理的cell的对应元素取出来。

然后要新建一个View对象，这个view是：先获取到Activity的布局渲染器，然后对这个布局渲染器LayoutInflater调用inflate方法。inflate方法需要传三个参数：resourceId（具体的item的那个布局的xml文件）、parent（父布局，和缓存有关）、false（表示这个resourceId所引用的布局是不带父布局的，因为如果带了父布局，就不能添加到目标要添加的父布局了）。

然后从这个view中通过findViewById的方法去找到具体的控件，接着分别从对应顺序的model的元素（第一行的getItem方法已经取出来了的那个）里面取出需要显示在item上面的值，赋值给对应的控件，然后返回这个view作为ListView的item。

* 在Activity中：

```
private List<Fruit> fruits = new ArrayList<>();

@Override
protected void onCreate(Bundle savedInstanceState) {
	super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);
    ListView listView = findViewById(R.id.list_view);

    initFruit();
    FruitAdapter adapter = new FruitAdapter(MainActivity.this,R.layout.fruit_item,fruits);

    listView.setAdapter(adapter);
}

private void initFruit(){
	for (int i =0;i<100;i++){
    	Fruit fruit = new Fruit("apple"+i,R.drawable.ic_launcher_background);
        fruits.add(fruit);
    }
}
```

前面的其实都是准备的工作，要生效还是在Activity里面建立数据数组，然后对数据数组进行初始化，然后新建一个自定义的适配器，最后把listview的适配器设置为刚才建立好的自定义适配器实例。

要实现更复杂的item，只要修改model和item的布局文件就可以了。

### 提升ListView的效率

ListView之所以难用就是因为它的使用初看比较简单，但是实际上有很多可以优化的点，比如提升效率。

前面写的代码的运行效率就很低，因为在适配器的代码中，每次item出现的时候，都会调用的getView方法中都会加载一遍布局。当ListView快速滑动的时候，就会带来不小的负担。

其实前面没有注意到的是，getView还有一个参数：convertView。

这个参数是将之前加载好的布局缓存起来，以便之后再次使用。

所以其实可以这样用：
```
View view;
if (convertView == null)
	LayoutInflater.from(getContext()).inflate(resourceId,parent,false);
else
    view = convertView;
```
这样一来，就不会每次都不分青红皂白地去加载布局了，而是先判断缓存的布局是不是为空，如果为空，再去加载布局，如果不为空，就直接变动要显示的数据就好了。

**纵使这样，代码的性能还能继续优化：**
虽然已经可以不用重复地去加载布局了，但是对于getView方法，还是需要每次从view里面通过findViewById去找到具体的控件。所以这里就可通过新建一个ViewHolder类去缓存每个控件了：
```
@NonNull
@Override
public View getView(int position, @Nullable View convertView, @NonNull ViewGroup parent) {
	Fruit fruit = getItem(position);

    ViewHolder viewHolder;
    View view;
    if (convertView == null){
    	view = LayoutInflater.from(getContext()).inflate(resourceId,parent,false);
    	viewHolder = new ViewHolder();
        viewHolder.fruitImage = (ImageView)view.findViewById(R.id.fruit_image);
        viewHolder.fruitName = (TextView)view.findViewById(R.id.fruit_textview);
        view.setTag(viewHolder);
    }
    else{
    	view = convertView;
        viewHolder = (ViewHolder)view.getTag();
    }

    assert fruit != null;
    viewHolder.fruitImage.setImageResource(fruit.getImageId());
    viewHolder.fruitName.setText(fruit.getName());
    return view;
}

public class ViewHolder{
	ImageView fruitImage;
    TextView fruitName;
}
```
可以看到这里的ViewHolder类也很简单，和Fruit类一一对应，只不过Fruit里面是数据，这个里面是需要改变数据的item的控件。

在原来优化的基础上，对于新加载布局的情况：把从view里面寻找对应的控件，然后存到viewHolder里面，最后把viewHolder实例设置为view的tag；

对于不用重新加载布局的view：直接通过getTag方法获取viewHolder，然后取出viewHolder里面的控件进行操作。

### ListView的点击事件
也很好理解，就和iOS里面的cell监听方法一样的。

对于Android的ListView，就是实现ListView的setOnItemClickListener方法参数中的闭包。

前面的优化都是在自定义适配器里写的，这里就要回到Activity里面了：
```
listView.setOnItemClickListener(new AdapterView.OnItemClickListener() {
	@Override
    public void onItemClick(AdapterView<?> parent, View view, int position, long id) {
    	Fruit fruit = fruits.get(position);
        Toast.makeText(MainActivity.this,"this is "+fruit.getName(),Toast.LENGTH_SHORT).show();
    }
});
```
这个方法是在参数中传入了一个闭包，覆写了onItemClick的方法，传入了三个参数：一个适配器类型的变量、一个当前的item的view，一个当前的index，一个id。

然后如果要让点击之后发生点什么，就在这个onItemClick方法里写就好。
## RecyclerView

终于过了那个ListView大户，然后又来了一个传说中更强大的控件——RecycleView。

ListView其实有着很多不够强大的地方——一是需要用一些技巧去优化它的性能，二是它只能实现竖向的滚动，不能实现横向的滑动。

所以，Android就提供了一个更强大的控件，这个可以看做一个增强版的ListView，可以轻松实现ListView的效果，还优化了ListView里面的各种不足之处。目前就连Android官方都更加推荐RecycleView。

### RecyclerView的简单用法
因为这个也是官方SDK里没有默认提供的控件，所以需要在app/build.gradle里面写明依赖库。

然后就是在布局文件里面写这个控件了。
```
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <android.support.v7.widget.RecyclerView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:id="@+id/recyclerview">
    </android.support.v7.widget.RecyclerView>

</LinearLayout>
```
也是很简单的写法。

因为想要用这个RecylerView实现ListView，所以依然需要一个适配器类。然后之前的item和model类是通用的，直接拿过来用就好。

新建一个FruitAdapter类：
```
public class FruitAdapter extends RecyclerView.Adapter<FruitAdapter.ViewHolder> {
    private List<Fruit> mFruitList;

    @NonNull
    @Override
    public ViewHolder onCreateViewHolder(@NonNull ViewGroup viewGroup, int i) {
        View view = LayoutInflater.from(viewGroup.getContext()).inflate(R.layout.item_layout,viewGroup,false);
        ViewHolder holder = new ViewHolder(view);
        return holder;
    }

    @Override
    public void onBindViewHolder(@NonNull ViewHolder viewHolder, int i) {
        Fruit fruit = mFruitList.get(i);
        viewHolder.fruitImage.setImageResource(fruit.getImageId());
        viewHolder.fruitName.setText(fruit.getName());
    }

    @Override
    public int getItemCount() {
        return mFruitList.size();
    }

    public FruitAdapter(List<Fruit> fruitList){
        mFruitList = fruitList;
    }


    static class ViewHolder extends RecyclerView.ViewHolder{
        ImageView fruitImage;
        TextView fruitName;

        public ViewHolder(View view){
            super(view);
            fruitImage = (ImageView)view.findViewById(R.id.fruit_image);
            fruitName = (TextView)view.findViewById(R.id.fruit_textview);
        }
    }
}
```
这段代码虽然比较长，但是其实理解起来不难：

* 首先是这个适配器类本身是继承自RecyclerView.Adapter

* 这个适配器的实例拥有一个List的实例变量，由构造函数传入参数来确定。

*  这个适配的内部还有一个内部类ViewHolder，ViewHolder继承自RecyclerView.ViewHolder，它的作用和前面的ListView的ViewHolder的作用类似，也是为了提高性能的复用机制。

* 适配器的类需要重写三个方法：

1.onCreateViewHolder（），是用来创建ViewHolder的实例。

2.onBindViewHolder（），用于将RecyclerView子项进行赋值，每个item出现在屏幕上的时候就会执行这个方法，相当于CellForRowAtIndex的iOS代理方法。 

3.getItemCount（）相当于iOS里面的numberOfRow，返回行数。

* 然后在Activity 中写一个线性布局的管理器，用于将item线性排列，这样就能实现listView 的样式。

细心的我还发现，在ListView中，可如果可滑动超过了屏幕范围，就会有一个滑动条指示当前的位置，如果是RecyclerView实现的ListView，就没有这个滑动条了。

### RecyclerView实现横向滚动和瀑布流
#### 横向滚动的实现
前面实现了类似ListView的纵向滚动，这次来在前面的基础上实现横向滚动。

由于数据内容并没有改变，所以这个UI的变化只需要更改布局相关的代码就可以实现了：
```
	android:orientation="vertical"

```
首先是在item的布局中，把每个item的布局改为合适的排列方式。（可选，这个看情况，并不影响功能的实现）。

```      
	layoutManager.setOrientation(LinearLayoutManager.HORIZONTAL);

```
然后就是在Activity的onCreate（）里让这个布局管理器的布局方式写为水平线性布局。
再次运行，就已经看到现在是水平滚动的RecyclerView了。

由此可见，RecyclerView的布局方式是由Activity自身去管理的，可以自由配置LayoutManager的属性。

除了上面用到的线性布局之外，RecyclerView还提供了GridLayoutManager（网格布局）、StaggeredGridLayoutManager（瀑布流布局）。

#### 瀑布流的实现
这一次就用StaggeredGridLayoutManager去实现瀑布流的布局：
```
StaggeredGridLayoutManager layoutManager = new StaggeredGridLayoutManager(3,StaggeredGridLayoutManager.VERTICAL);

```
要改动的其实就只有布局管理器一处，前面说过，数据不变的时候就是布局与布局管理器的改动了。

这个StaggeredGridLayoutManager的构造方法里传入的两个参数分别表示：列数、布局的方向。

### RecyclerView的点击事件
这个RecyclerView的点击事件并没有提供具体的监听方法。所有的点击操作需要用户为每个item手动注册点击事件。

看似RecyclerView不及ListView设计的好，但是实际上RecyclerView有自己的考虑：如果用户点击到的是item里面的某一个说具体的子控件呢？虽然ListView也还是有办法可以处理，但是就不是那么的人性化了。

主要就是在adapter类里面进行设置：

* 在ViewHolder里面添加View，用来响应item的项点击事件（如果需要的话）。
* 在onCreateViewHolder（）方法里面在每次创建ViewHolder（也就是每次新建item的时候），对item中的控件注册点击监听器：
```
	public ViewHolder onCreateViewHolder(@NonNull ViewGroup viewGroup, int i) {
        View view = LayoutInflater.from(viewGroup.getContext()).inflate(R.layout.item_layout,viewGroup,false);

        final ViewHolder holder = new ViewHolder(view);
        holder.fruitImage.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                int position = holder.getAdapterPosition();
                Fruit fruit = mFruitList.get(position);
                Toast.makeText(view.getContext(),"clicked __ "+fruit.getName(),Toast.LENGTH_SHORT).show();
            }
        });
        return holder;
    }
```
这样就实现了具体的控件的点击事件的监听。前面对于item的view的点击注册会在没有注册过的控件的点击事件进行捕获并响应。

## 总结
基本的UI控件就到此为止了，安卓和iOS的设计还是有很多的相似之处，就是细节和思路的不同之处还需要多加练习以便熟练掌握，难点主要集中在ListView和RecyclerView上面，这个ListView其实比iOS的UITableView的功能差了很多，而RecyclerView和UIScrollView有一些相似，只不过iOS中的UITableView是继承自UISCrollView，但是安卓中的ListView并不是继承自RecyclerView的，后者反而是非标准的支持库的内容。

# 碎片
## 碎片是什么

经常能见到平板上对于手机的UI的不合理拉伸效果，这个碎片可以用来重新设计HD版本的APP，可以充分利用平板的大屏幕空间。

碎片（Fragment）是一种可以嵌入到活动中的UI片段。它和活动很像，都能包含布局，也都有自己的生命周期。可以在一个活动中引入多个碎片，然后在碎片里实现不同的布局和业务逻辑。

## 碎片的简单使用
* 新建fragment的布局文集散，和别的布局没有什么不同。
* 然后就是新建一个继承自Fragment的java类，这个就类似于activity的控制器。这里需要注意可作为父类的Fragment有两个，一个是系统内置的android.app.Fragment，一个是support-v4库中的Fragment类。这里用后者的原因是前者会导致4.2版本之前的系统无法支持这个控件而程序崩溃。
```
public class LeftFragment extends Fragment {
    @Nullable
    @Override
    public View onCreateView(@NonNull LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
        View view = inflater.inflate(R.layout.left_fragment,container, false);
        return view;
    }
}
```
类似菜单和alert的方式，都是用一个铺开器去加载布局然后展开。
* 在根Activity的布局文件里写对前面的fragment的布局文件的引用：
```
    <fragment
        android:id = "@+id/left_fragment"
        android:name="com.example.ifan.fragmenttest.LeftFragment"
        android:layout_width="0dp"
        android:layout_weight="1"
        android:layout_height="match_parent">
    </fragment>
```
可以看到这个比一般的控件多了一个name的字段声明，这个是因为需要指定当前需要引用哪一个布局文件，也是布局文件内嵌使用的必要操作。（包名也要写全。）

## 动态添加碎片
### 动态添加碎片的五个步骤
动态添加碎片分为五个步骤：

* 先创建待添加的fragment实例：

```
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical" android:layout_width="match_parent"
    android:layout_height="match_parent">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center_horizontal"
        android:textSize="20sp"
        android:text="this is another right fragment"
        />
</LinearLayout>
```
上面是新建的一个布局，然后新建一个继承自Fragment的java类加载这个布局作为这个新的Fragment类。
```
public class AnotherRightFragment extends Fragment {
    @Nullable
    @Override
    public View onCreateView(@NonNull LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
        View view = inflater.inflate(R.layout.another_right_fragment,container,false);
        return view;
    }
}
```

*  获取FragmentManager。在Activity里面可以直接调用方法去获取：
```
    private void replaceFragment(Fragment fragment){
        FragmentManager fragmentManager = getSupportFragmentManager();
        FragmentTransaction transaction = fragmentManager.beginTransaction();
        transaction.replace(R.id.right_layout,fragment);
        transaction.commit();
    }
```
* 开启一个事务（transaction）。对FragmentManager的实例调用beginTransaction的方法。
* 向容器内添加或者替换碎片，一般用的是replace（）方法，传入容器的id和待添加的Fragment实例。
**需要注意的是**：在activity的布局文件中，其实还做了一个操作是把上一个例子的另一个Fragment的布局控件改为了一个layout，这个layout控件就相当于一个容器，用来容纳新加入或者替换的Fragment。
* 提交这个事务（transaction），对transaction调用commit（）方法。

### 让Fragment参与到返回栈中

前面的Activity有一个返回栈，在点击back的按钮的时候就会弹出栈顶，Fragment默认是不参与到返回栈的。

如果想让Fragment也参与到返回栈的操作逻辑里的，对FragmentTransaction调用addToBackStack（）方法就可以了。
```
transaction.addToBackStack(null);

```
如果每一次Fragment出现的时候都添加到返回栈的话，就是类似于activity的standard模式，会一直加到栈里。
### Fragment和Activity之间的通信
FragmentManager提供了类似于findViewById（）的方法：
```
RightFragment rightFragment = (RightFragment)getSupportFragmentManager().findFragmentById(R.id.right_layout);

```
然后这个Fragment的实例就可以调用这个Fragment里的方法了。

反之：也可以在Fragment里面获取Activity的实例，来执行Activity的方法。
```
MainActivity mainActivity = (MainActivity)getActivity();
```
再者，还可以不同的Fragment之间通信，可以先获取到Activity的实例，再通过Activity去获取Activity相关联的Fragment。

## 碎片的生命周期
碎片和活动很类似，所以这个生命周期也是很相似的，有一些细节不太相同：
### 四个状态
#### 运行状态
碎片可见、并且相关的活动正处于运行态
#### 暂停状态
活动进入暂停的状态的时候，碎片也会进入暂停状态。
#### 停止状态
活动进入停止状态的时候、或者调用FragmentTransaction的remove（）或者replace（）方法的时候，碎片会被从活动中移除掉。

但是如果碎片是通过addToBackStack（）方法加到返回栈中的，那碎片也会进入停止状态。这时碎片是完全不可见的。
#### 销毁状态
碎片是依附着活动存在的，当活动被销毁的时候，相关联的碎片就会进入到销毁状态。

或者在没有addToBackStack的情况下直接调用FragmentTransaction的remove（）、replace（）方法移除掉碎片，都会导致碎片进入销毁状态。

### 五个回调方法

#### onAttach（）
这个方法时为了让碎片和活动建立关联的。会在Activity的onCreate（）方法之前调用。
#### onCreateView（）
为碎片加载布局文件的时候调用的。在Activity的onCreate（）方法之后调用。
#### onActivityCreated（）
确保碎片依存的Activity一定已经创建完毕之后调用的。判断Activity是否已经成功创建。
#### onDestroyView（）
当碎片依存的视图被进入停止状态之后调用，在onStop（）之后调用。

如果是从返回栈中回到上一个碎片，下一个要出现的碎片进入onCreateView（）的回调
#### onDetach（）
当碎片和Activity接触关联的时候调用，这时候的Activity进入销毁状态。在Activity的onDestroy（）方法之后调用。在onDetach（）方法之后，碎片就被销毁了

## 一些使用技巧
### 使用限定符
在运行时判断程序是使用双页模式还是单页。

限定符是在res的资源文件里不同的标识文件夹下写不同的布局文件、资源文件。然后app会根据不同的设备自动加载不同的布局和资源。

限定符一般分为三类：

#### 大小
从小到大依次为：small、normal、large、xlarge

#### 分辨率
* 120dpi以下： ldpi
* 120dpi~160dpi：mdpi
* 160dpi~240dpi：hdpi
* 240dpi~320dpi：xhdpi
* 320dpi~480dpi：xxhdpi

#### 方向
* land：横屏
* port：竖屏

### 使用最小宽度限定符
前面提到了限定符的概念，但是比如large缺少一些具体的信息，到底多大屏幕的设备会被认定为large呢？这就需要更灵活的限定了。
使用最小宽度限定符就是指定一个最小的宽度值（以dp为单位），然后比这个最小宽度小的就会加载一种布局，大于这个值的就会加载另外一种布局。

具体的做法是在res文件夹下新建一个layout-sw600dp的文件夹，然后在该文件夹下写一套布局文件，这样这个文件夹中的600就是一个最小宽度的限定，大于600dp宽度的设备会自动加载这个文件夹中的布局，如果没有大于600，就会加载原来的layout文件夹中的布局文件。