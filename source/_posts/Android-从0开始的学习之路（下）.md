title: 《Android第一行代码》读书笔记（下）
author: howard
tags: []
categories: []
date: 2018-07-18 21:42:00
---
为什么要分个上下呢？因为在实际的使用中发现如果整篇文章过长的话，可能导致博客的性能十分低下，无论是admin的管理界面还是整个静态网页。暂时还没有精力去优化一下，就暂时分个上下篇吧。
<!--more-->
# 广播机制

## 简介
Android中的每个app都可以对自己感兴趣的广播进行注册，这样它就可以收到它关心的广播内容。这些广播有的是来自于系统，有的是来自其他app的。

广播分为两种：**标准广播、有序广播**
### 标准广播
是一种完全异步执行的广播，在广播发出之后，所有注册的接收器会在同一时间接收到这条广播，效率较高、无法截断。
### 有序广播
是同步执行的广播，在广播发出之后，同一时间只会有一个接受者去接受这个广播，然后剩下的接受者依次轮流接收到广播。

优先级高的广播接收者就先收到，并且有机会截断正要继续传递的广播，这样后面的接受者就接收不到了。

## 接收系统广播
Android系统内置了很多系统级别的广播，在app中可以监听这些广播来得到系统的状态。比如手机开机完成之后、电池电量发生变化、时间、时区发生变化等等情况都会发出广播。

app在注册接收器的时候有两种方式：在代码中注册，或者在在AndroidManifest中注册。

前者也称为动态注册，后者也称为静态注册。

### 动态注册
动态注册需要一个接收器类，这个新类继承自BroadcaseReceiver，并重写onReceive（）方法，这样如果有广播到来，onReceive（）方法就会被调用。具体的处理逻辑写在这里就会被执行。

```java
public class MainActivity extends AppCompatActivity {
    private IntentFilter   intentFilter;
    private NetworkChangeReceiver networkChangeReceiver;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        intentFilter = new IntentFilter();
        intentFilter.addAction("android.net.conn.CONNECTIVITY_CHANGE");
        networkChangeReceiver = new NetworkChangeReceiver();
        registerReceiver(networkChangeReceiver,intentFilter);

    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        unregisterReceiver(networkChangeReceiver);
    }

    class NetworkChangeReceiver extends BroadcastReceiver{
        @Override
        public void onReceive(Context context, Intent intent) {
            Toast.makeText(context,"I had received a Broadcast",Toast.LENGTH_SHORT).show();
        }
    }
}
```
在Activity类里面自定义了一个内部类NetworkChangeReceiver，继承自BroadcastReceiver，重写了父类的onReceive（）方法，这样一来就有了一个接收器类。

然后Activity加了一个该类的实例，然后加了一个意图过滤器，去捕获系统的某个事件广播。

最后调用registerReceiver的方法注册一个接受者，传入两个参数：接收器、过滤器。

**在Activity被销毁的时候需要手动移除通知接受者。**

运行程序，可以看到在注册完成的时候会调用一遍onReceive方法，之后就是在网络状态发生改变的时候会调用。

**除此之外，我们还可以获取当前网络的可用状态**
```java
ConnectivityManager connectivityManager = (ConnectivityManager)getSystemService(Context.CONNECTIVITY_SERVICE);
NetworkInfo networkInfo = connectivityManager.getActiveNetworkInfo();
if(networkInfo != null&&networkInfo.isConnected()){
	Toast.makeText(context,"network is available",Toast.LENGTH_SHORT).show();
}
else {
	Toast.makeText(context,"network is disable",Toast.LENGTH_SHORT).show();
}
```
先通过getSystemService（）方法得到ConnectivityManager的实例，这个是一个专门管理网络连接的系统服务类，然后对这个实例调用getActiveNetworkInfo（）的大搜系统的网络状态。判断这个状态，显示不同的toast就可以了。

**注意：Android系统为了保护用户安全和隐私，规定了如果程序需要进行一些敏感信息的操作，就必须在配置文件中声明权限，否则程序就崩了。所以就需要在AndroidManifest文件里加入权限声明：**
emmm，新版的AndroidStudio已经自动加了，很好用呀，但是还是需要知道有这个权限的存在，这个机制倒是和iOS开发里面的info.plist文件很相似。

### 静态注册
前面的动态注册方法可以自由控制广播接收器的注册和取消很灵活，但是也有缺点：要开启广播接收器就必须要让app先运行起来。这样就没有办法实现让程序在没有启动的情况下就能接受到广播。

这就需要静态注册的方式了：

AndroidStudio提供了快捷方式去做静态注册：在java文件夹的包名文件夹下右键-New-Other-Broadcast Receiver，在弹出的窗口里选Exported和Enable选项然后Finsh就可以完成创建。

然后会有一个新的java类：
```
public class BootCompleteReceiver extends BroadcastReceiver {

    @Override
    public void onReceive(Context context, Intent intent) {
        // TODO: This method is called when the BroadcastReceiver is receiving
        // an Intent broadcast.
        Toast.makeText(context,"boot complete",Toast.LENGTH_SHORT).show();
    }
}
```
这个java类就是一个静态方式的广播接收器。所以覆写onReceive（）方法就可以实现自己想要的逻辑。

在此之后，还需要在AndroidManifest文件里面去声明权限，前面看到过关于网络的权限AndroidStudio已经自动写了，但是这次并没有写获取开机的通知的代码，就需要我们手动写一个这样的语句：
```
    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED"/>

```
不过AndroidStudio还是为我们自动写了一些东西的：
```
<receiver
	android:name=".BootCompleteReceiver"
	android:enabled="true"
	android:exported="true">
	<intent-filter>
		<action android:name="android.intent.action.BOOT_COMPLETED"/>
	</intent-filter>
</receiver>
```
自动添加了一个receiver的字段，声明了name等信息，不过中间的intent-filter字段是需要自己写的，这个是为了说明让接受者捕获什么样的广播。所以这里写了一个action，声明了要监听的是BOOT_COMPLETED这个特定的消息。

**需要注意的是：接收器的java类里面不要添加过于复杂的逻辑或者一些耗时操作，因为接收器里是不允许开启线程的，当onReceive（）的方法里运行了很长时间都没有结束，程序就会报错的。**

## 发送自定义广播
前面也说过广播有标准和有序两种，接下来就通过发送广播去感受一下两种广播的不同

### 发送标准广播
发送标准广播很简单：
```
Intent intent = new Intent("com.example.broadcasttest.MY_BROADCAST");
sendBroadcast(intent);
```
新建一个意图，然后调用sengBroadcast（）方法把这个意图发送出去就好了。

而且因为是使用intent发送的广播，所以还是可以携带数据等信息给接受者。

### 发送有序广播
如果要发送有序广播其实只需要改动一行代码就可以实现了：
```
sendOrderedBroadcast(intent,null);

```
这个方法的方法名里多了order，表示是有序的。

另外，对于接受者，就需要声明一下自己的接受优先级：
```
android:priority ="100"

```
表示自己的优先级设置为最高，保证可以在AnotherBroadcastReceiver之前就收到广播。

再者，如过接收器的java类的onReceive（）方法的实现中可以调用
abortBroadcast()方法来阻断该条有序广播的继续传播。

## 使用本地广播

前面也说过，广播机制是系统全局的，也就是说，我们app发送的广播其他的app也可以收到，其他app发的广播我们也可以收到。这样就可能带来一些安全性的问题：万一别的app获取了我们的app广播数据，或者他们一直发送大量垃圾广播。

所以Android引入了一套本地广播机制，这个机制发出的广播只能够在app内部传递，接收器也不会接受除了自己app之外的广播了。

还是从发送和接收的两个方面来看一下不同点：
### 发送本地广播
* 首先需要一个LocalBroadcastManager的实例属性
* 和前面不一样的是，这里需要的这个管理器是需要用get的方法去获取实例的。

```
localBroadcastManager = LocalBroadcastManager.getInstance(this);      
localBroadcastManager.sendBroadcast(intent);
```
* 然后就是对这个管理器调用sendBroadcast（）方法去发送意图。
可以看到其实基本上和标准广播的发送没有什么不同，只不过多了LocalBroadcastManager类的使用。

### 接收本地广播
这个其实和前面的标准广播的套路也很类似：
* Activity要有一个接收器类的实例
* 写好intent的监听条件
* 通过对localBroadcastManager调用registerReceiver（）方法去注册本地广播接受者。
* 在Activity的onDestroy（）方法里对localBroadcastManager调用unregisterReceiver（）注销接受者。

### 总结
本地的广播是没有办法注册为静态方式的，因为它的存在周期也就是在app的生存周期之内，所以也没有必要用静态的方式。

本地广播对比一般广播的优势：

* 可以明确地知道这类广播不会作用在app之外，比较安全。
* 其他的程序无法发送广播进来，不用担心恶意广播的安全隐患。
* 因为只作用在app内部，所以效率也会更高。


# 持久化存储

## 简介
持久化存储其实就是把内存中的数据存到外外存上。

Android主要提供了三种方式去做持久化：文件、SharedPreference、数据库存储。其实还是可以把数据存在SD卡里但是后来的手机都没有SD卡的插槽了，而且也比较不安全。

## 文件存储

文件存储是一种最基本的数据存储方式，它不对内容进行处理，直接原封不动地存储，比较适合用来存储一些简单的文本数据或者二进制数据。如果需要实现复杂的文本存储，就需要自定义格式规范。

### 存储到文件

Context类有一个openFileOutput（）的方法，用于将数据存储到指定的文件中：
```
public void save(){
	String data = "Data to be save";
	FileOutputStream out = null;
	BufferedWriter writer = null;
	try{
		out = openFileOutput("data",Context.MODE_PRIVATE);
		writer = new BufferedWriter(new OutputStreamWriter(out));
		writer.write(data);
	}
	catch (IOException e){
		e.printStackTrace();
	}
	finally {
		try{
			if (writer != null){
				writer.close();
			}
		}
		catch (IOException e){
  	  	e.printStackTrace();
		}
	}
}
```
首先有一个待存储的data字符串。然后新一个文件输出流的对象FileOutputStream类的实例。和一个BufferedWriter的实例。


openFileOutput（）方法的两个参数分别是文件名（不可包含路径）和文件操作模式（MODE_PRIVATE和MODE_APPEND），然后返回一个FileOutputStream对象，这个对象可以用java流的方式写入文件。

借助这个流对象构建一个BufferedWriter对象，这个对象就可以把某个字符串写入到文件里面了。

所以写入文件的流程其实就是三步：
* 先打开一个到文件的输出流（文件输出流）
* 然后用文件输出流创建一个“输出流写入器”
* 再用输出流写入器创建一个“缓冲写入器”将数据写入数据流。

**之所以不用写文件路径，是因为默认都会放到“data/data/package name/files”文件夹下** 

### 从文件中读取数据
从文件中读数据也是需要一个openFileInput（）方法。只接受一个参数——文件名，同样也是从data/data/package name/files文件夹下去找的。然后返回一个FileInputStream对象。

然后还是通过java流的方式读入。

```
    public String load(){
        FileInputStream fileInputStream = null;
        BufferedReader bufferedReader = null;
        StringBuilder content = new StringBuilder();
        
        try {
            fileInputStream = openFileInput("data");
            bufferedReader = new BufferedReader(new InputStreamReader(fileInputStream));
            String line = "";
            while ((line  = bufferedReader.readLine()) !=null){
                content.append(line);
            }
        }
        catch (IOException e){
            e.printStackTrace();
        }
        finally {
            if(bufferedReader != null){
                try{
                    bufferedReader.close();
                }
                catch (IOException e){
                    e.printStackTrace();
                }
            }
        }
        return content.toString();
    }
```
读入文件其实也是三步：
* 创建文件输入流
* 用文件输入流创建输入流读取器 
* 用输入流读取器创建一个缓冲读取器

然后当读入的字符不为空，就循环读取就可以了。可以用一个StringBuilder去拼接字符串。

（PS：可以用TextUtils.isEmpty（）方法来判断字符串的空值情况，这个方法可以同时判断两种情况——当字符串对象为null或者是一个空串，都可以返回true。）

## ShardPreferences存储

这种存储是用键值的方式去存储数据(xml格式)，支持多种不同的数据类型，比文件的存储要方便很多。

### 数据存入
要使用SharedPreferences存储，就需要先获取到SharePreferences对象，Android提供了三种方法去获取：Context类中的get方法、Activity类中的get方法、PreferenceManger类中的getDefaultShared方法。
* 获取SharedPreferences

 ```
getSharedPreferences("data",MODE_PRIVATE);
//这个方法是Context类中的，接受两个参数，第一个是文件名，存放在data/data/package name/shared_prefs目录下，如果不存在就会新建一个文件，第二个参数是操作模式，这里用的是private模式，表示其他的app无权操作，其他模式已经废弃。
 ```
 ```
getPreferences(MODE_PRIVATE);
//这个方法时Activity中的，只不过只接收一个参数，它会自动将当前活动的类名作为SharedPreferences的文件名。
 ```
 ```
PreferenceManager.getDefaultSharedPreferences(this);
//这个是PreferenceManager类的方法，接收一个Context参数，并自动使用当前app的包名作为前缀作为文件名
 ```
 
上面的三个方法都是为了得到SharedPreferences的，得到之后就可以进行下一步了：
 
* 对SharedPreferences对象调用edit（）方法来获取一个SharedPreferences.Editor的对象
 ```
SharedPreferences.Editor editor = getSharedPreferences("data",MODE_PRIVATE).edit();

 ```
 
* 向editor中添加数据：

 ```
editor.putInt("age",21);
editor.putString("name","paopao");
editor.putBoolean("single",false);
 ```
* 对editor调用apply（）方法将前面添加的数据提交，就完成了存储操作。
 ```
editor.apply();

 ```
 
### 数据取出
取数据的步骤更为简单：
* 先获取到SharedPreferences对象，和前面的方法一样。

* 对SharedPreferences对象调用getXXX方法，get方法传入两个参数，第一个参数为键名，第二个参数为默认值——如果对应的键没有取到值，就返回默认值。

### 总结
其实这个SharedPreferences和iOS中的NSUserDefault非常相似，只不过后者用单例模式实现。但是Android中的用PreferenceManager的方法获取到的SharedPreferences其实也可以当做一个单例来使用，因为同一个app的包名是一样的。

## SQLite数据库存储
Android内置了SQLite数据库，这是一种轻量级的关系型数据库，运算速度快，占用资源少（一般几百KB内存就够了），支持标准的SQL语法，遵循ACID事务（即原子性、一致性、隔离性、持久性）。
### 创建数据库
Android为了能够更方便地使用数据库，专门提供了一个SQLiteOpenHelper的帮助类。它是一个抽象类。所以需要我们自己构建子类去实现具体的功能。

```
    class MyDatabaseHelper extends SQLiteOpenHelper{
        private static final String CREATE_BOOK = "create table Book ("
                +"id integer primary key autoincrement, "
                +"author text, "
                +"price real,"
                +"pages integer,"
                +"name text)";
        private Context mContext;
        public MyDatabaseHelper(Context context, String name, SQLiteDatabase.CursorFactory factory,int version){
            super(context , name , factory , version);
            mContext = context;
        }

        @Override
        public void onCreate(SQLiteDatabase sqLiteDatabase) {
            sqLiteDatabase.execSQL(CREATE_BOOK);
            Toast.makeText(MainActivity.this,"create database succeded",Toast.LENGTH_SHORT).show();
        }

        @Override
        public void onUpgrade(SQLiteDatabase sqLiteDatabase, int i, int i1) {
        }
    }
```
* 首先需要新建一个类，继承自SQLiteOpenHelper，类中需要有一个用于创建数据库的SQL语句。还要有一个context的实例变量来暂存构造方法中传入的context。

* 重写该类的构造方法，将参数中的context传给成员变量。四个参数的意义分别是：上下文、数据库名、自定义光标（一般为null）、数据库版本号。

* 重写onCreate（）方法，对SQLiteDatabase的实例调用execSQL（）方法去执行前面写好的SQL语句。

* 重写onUpgrade（）方法，加入想要执行的逻辑代码。

这个数据库帮助类就基本完成了，要使用这个类去创建数据库，就要在Activity的java类中去创建一个该帮助类的实例，
* 创建该类的实例：
 ```
MyDatabaseHelper myDatabaseHelper = new MyDatabaseHelper(this,"BookStore.db",null,1);

 ```
* 对实例调用创建方法：
 ```
myDatabaseHelper.getWritableDatabase();
//方法一：当数据库不可写入的时候，会报异常
myDatabaseHelper.getReadableDatabase();
//方法二：当数据库不可写入的时候，会以只读方式打开
//两个方法都可以打开一个已存在数据库或者新建一个数据库并返回一个可读写数据库的对象。
 ```
 
### 升级数据库
 
 前面覆写了onUpgrade（）的方法，但是没有加入逻辑代码。如果需要在原来的onCreate（）中加入别的SQL语句的操作，就会发现，已经存在了数据库之后，原来的onCreate（）不能正确执行，因为数据库已经存在。
 
 在onUpgrade（）方法中，先把数据表删除了，然后再调用更新了代码的onCreate（）方法。
 
 而且，onUpgrade（）不是手动调用的，它的触发机制是在自定义帮助类的初始化的时候，在版本参数中传入一个大于上一个版本数的数字就好了。
 
 
### 添加数据
既然要操作数据库，当然可以用SQL语句去完成，但是为了照顾开发者的SQL水平，Android提供了不用SQl语句操作数据库的方法。

前面用getWritableDatabase（）或getReadableDatabase（）返回的是一个SQliteDatabase对象，对这个对象调用方法就可以对数据库操作。

```
SQLiteDatabase db = myDatabaseHelper.getReadableDatabase();

ContentValues values = new ContentValues();
values.put("name","Android first code");
values.put("author","paopao");
values.put("pages",454);
values.put("price",16.96);
db.insert("Book",null,values);
values.clear();
```
新建一个ContentValue来组装单条数据项，然后对SQLiteDatabase实例调用insert（）方法，接收三个参数：表名、未指定数据给某些可为空的列自动赋值为NULL、单条数据项。

后面如果需要组装多次，可以重复使用ContentValues的实例，每次用clear方法清空数据就可以了。

### 更新数据

SQLiteDatabase还提供了update（）方法用于更新数据。
```
values.put("price",10.99);
db.update("Book",values,"name  = ?",new String[]{"Android first code"});
```
先构造需要修改的数据项，然后对SQLiteDatabase调用update（）方法去更新数据。

update（）方法接收四个参数：表名、数据项、where语句部分（这里的？是一个占位符，可以指定第四个参数去替换这里的？）、用于替换占位符的字符串。

### 删除数据
```
db.delete("Book","page > ?",new String[]{"500"});

```
删除数据就更简单，接收三个参数：表名、where语句、用于替换占位符的String对象。

### 查询数据
查询是这几种数据库操作里面最复杂的操作了。

同样的，SQLiteDatabase提供了query（）方法用于查询数据，这个方法很复杂——需要传入七个参数。(拍照渣像素，见谅)
![P9iCh6.jpg](https://s1.ax1x.com/2018/06/23/P9iCh6.jpg)

然后这个函数会返回Cursor对象，包含着若干行数据。
可以对cursor实例对象调用moveToFirst（）和moveToNext（）方法来切换行的跳转。

对cursor对象调用getColumnIndex（）传入列名可以得到某一列在表中对应位置的索引，然后再对cursor调用getInt等方法从得到的索引中取出当前行的具体类型的数据。
```
Cursor cursor  = db.query("Book",null,null,null,null,null,null,);
cursor.getString(cursor.getColumnIndex("name"));
```
(这个只是展示方法调用的，实际使用还需要加入一些判空等操作和行的操作)。


### 直接使用SQL语句操作数据库
Android充分考虑不同人的编程习惯，也支持开发者直接使用SQL操作数据库：

查询数据库用SQLiteDatabase的rawQuery（）方法，其他的操作都是用调用execSQL（）方法，接受两个参数，一个由语句和占位符构成的公式，一个字符串数组去逐个替换占位符。
```
db.execSQL("insert into Book(name,author,pages,price)values(?,?,?,?)",new String[]{"a book","me","454","16.61"});
db.rawQuery("select * from Book",null);
```

## 使用LitePal操作数据库
这个是一个开源库，采用了对象关系映射ORM的模式，不用编写SQL语句就可以完成建表和各种增删改查等骚操作。

鉴于这个也是第一次在Adroid开发中使用开源库，所以顺带熟悉一下怎么集成第三方框架

### 集成第三方框架
很多的开源框架会提交到jcenter上面，所以只需要在app/build.gradle里面声明引用该开源库就可以了，这一点和cocoapods很相似。当然了，也是可以下载别人的开源库的源代码然后导入包的，只不过这种方式方便很多。
```JAVA
implementation 'org.litepal.android:core:1.4.1'
```
* 在dependencies字段中添加上面的语句，然后同步gradle就可以集成该库了。

* 接下来使用的时候，先在app/src/main目录下新建一个asset目录，然后在asset目录下新建一个litepal.xml文件，写入如下内容：
```Xml
<?xml version="1.0" encoding="utf-8" ?>
<litepal>
    <dbname value = "BookStore"></dbname>
    <version value = "1"></version>
    <list>
    </list>
</litepal>
```
其中的dbname是用于指定数据库名，version用于指定数据库的版本号，list用来指定映射模型，这个一会再写。

* 最后在AndroidManifest.xml中配置：
```java
android:name="org.litepal.LitePalApplication"
```
为什么要这样配置呢？再后面一些会说的。

到此为止就完成了，看起来其实好像要比cocoapods使用还是要麻烦一些的。

## 创建和升级数据库
前面提到过这个litepal是用了ORM，那和前面的数据库操作有什么不同呢？

这个对象关系映射模式，使得开发者可以用面向对象的思维来操作数据库。

试一下：
* 先新建一个类Book，
```JAVA
public class Book{
	private int id ;
	private String author;
	private double price;
	private int pages;
	private String name;
}
```
然后按Alt+Insert键，可以自动生成getter和setter方法（这么好用？！），这样这个Book类就对应数据库中的一张Book表，而每一个成员变量就对应一列
* 接下来还需要将Book类添加到映射表中——就是前面没写完的list部分：
```XML
<litepal>
    <dbname value = "BookStore"></dbname>
    <version value = "1"></version>
    <list>
        <mapping class="com.example.ifan.databasetest.MainActivity.Book"></mapping>
    </list>
</litepal>
```
这里一定要使用完整的类名。如果有多个类需要映射，就都添加到list里面就可以了。
* 调用LitePal.getDatabase（）方法就可以创建数据库了。
* 要升级数据库也很简单，不用像之前一样去drop掉表然后再建新表了，直接在相关的类中做列的修改，然后在litepal.xml中修改版本号就可以了。不用担心，原来的数据还是保留下来的，不用担心数据的丢失。

## 添加数据
之前要添加数据的时候，需要先新建一个ContentValues对象作为新的一行的数据项，然后向其中添加数据......

现在，只需要新建一个模板类的实例，对成员变量赋值，然后对对象调用save（）方法就可以了。

之前的模板类没有写所继承的父类，因为光进行表的管理操作本来就不需要继承别的类就可以实现。但是如果要进行CRUD操作，还是要继承DataSupport类。
```JAVA
Book book = new Book();
book.setName("hi you");
book.setPrice(12.655);
book.save();
```
最后这个save（）方法就是DataSupport类提供的，除此之外，它还提供了丰富的CRDU方法。

### 更新数据
更新数据有几种方法：
* 最简单的一种是**对已经存储的对象重新设值**
（PS：首先要搞清楚的是什么才算是已经存在的数据？这里的判定是通过对模板类实例调用isSaved（）方法，如果存在，就返回true。返回true有两种情况：一种是已经调用过save（）添加过数据；一种是LitePal提供了API去查询数据库，查到了就表明已经存在。）
所以可以对前面存储过的某条数据的部分成员变量赋值修改后的值。
试过之后，发现这个必须是连续操作的，重启app之后会把这一条本来表示更新的的数据重复添加。这样一来，就有了很大的局限性。

那么就要用到更灵活的方法了：
* updateAll（）方法
```java
book.updateAll("name = ? and author = ?","hi me","me");
```
还是新建一个模板类，对成员变量赋值，然后用这个updataAll（）方法更新数据库，参数也比较简单，第一个参数是公式，后面若干个字符串是用于替代占位符的串。

另外，LitePal还提供了设置为默认值的方法，而不是手动设置为默认值：setToDefault（），参数中接受若干个字符串作为要重置为默认值的列名。

### 删除数据
对于比较简单的delete（）方法没有太大的意义，这里主要看一下另一种删除的方法：

DataSupport类提供了deleteAll（）方法来删除数据，和上面的updateAll（）用法类似，很灵活。

### 查询数据
前面的query（）方法里面要传入7个参数，颇为不便，LitePal做了很多优化：
```java
List<Book> books = DataSupport.select("name")
                              .where("pages >30")
                              .order("price")
                              .limit(10)
                              .offset(10)
                              .find(Book.class);
```


* 首先，LitePal的查询结果返回了一个模板类构成的List，这样就自动完成了赋值，后面的操作都围绕着List中的元素进行就可以了。

* 而且把众多的查询条件都以方法的方式封装，可以根据实际情况自由连缀使用。


# 内容提供器——跨程序共享数据
之前的持久化存储部分提到过，文件的存储都开始使用本app独占的操作模式，就是为了保证数据安全，

但是有的时候需要和别的app共享一些数据——当然不是账号密码这种敏感数据！比如电话簿、短信等程序，需要开放一些数据以便二次开发，那就需要跨程序共享数据，这就需要用到内容提供器了（而且，这个Android实现跨程序共享数据的标准方式。）


## 简介
内容提供器（Content Provider）提供了一套完整的机制，允许一个程序访问另一个程序的数据，同时还能保证被访问数据的安全。

emmm，书上说，要先了解一下运行时权限，好吧，歪一下。
## 运行时权限
Android中其实一直有权限机制，但是都比较鸡肋。后来到了6.0时代，引用了运行时权限，为了更好地保护用户的安全和隐私。

### 权限详解
前面在用广播的时候就写过关于权限的东西——在AndroidManifest.xml中写过uses-permission的字段，有了这个字段之后，对于低于6.0系统的设备上安装时候就会提醒用户一些权限获取的信息，用户可以选择安装与否。

但是越来越多的app就是先都一股脑申请了权限，不管你用得到用不到，这样就容易被app滥用权限，但是对于一些常用、必用的app，又不得不同意。

6.0加入的运行时权限，使得用户不用一次性授权所有权限，而是可以在运行过程中，对具体的某一次申请进行授权。

Android中的权限分为两类：**普通权限、危险权限**。（其实还有一种特殊权限，但是使用的太少了就先不说了）

* 普通权限：一些不会直接威胁到用户的安全和隐私的权限。这种权限系统会自动授权。
* 危险权限：一些可能会触及到用户隐私或者会对设备安全性造成影响的权限，如联系人、地理位置等，这类权限需要用户手动授权。

危险权限实际上很少，大部分是普通权限，这里有张表列出了9组24个危险权限，除此之外，都是普通权限：
![P9MLIU.jpg](https://s1.ax1x.com/2018/06/23/P9MLIU.jpg)

需要注意的是，虽然请求的是某一个具体的权限，但是最后一旦授权，那么这个权限所属的权限组都会获得权限。

### 在运行时申请权限
因为运行时动态授权的都是危险权限，所以这里只写获取危险权限的例子：

* 第一步就是先判断用户是否已经授权：
```java
ContextCompat.checkSelfPermission(MainActivity.this, Manifest.permission.CALL_PHONE);
```
这个方法接受两个参数：上下文、具体的权限名。
然后将返回值和PackageManager.PERMISSION_GRANTED作比较，如果相等，那就是已经授权了，反之则没有授权。
* 如果已经被授予权限，就新建一个intent并设置所携带的数据，然后用StartActivity（）方法启动这个intent，就会跳转到目标的页面。
* 如果没有被授权，ActivityCompat.requestPermissions（）方法来申请权限。接受三个参数，第一个参数是Activity的实例变量、第二个是String数组，用来放要申请的权限名、第三个参数是请求码，传入一个整型唯一值就可以了。
* 前面的请求权限后面会唤起一个回调方法——onRequestPermissionResult，这个回调方法会携带garantResults参数，只要判断一下最后的授权结果就知道下一步是放弃操作还是用刚请求到的权限搞一搞事情。
（PS：这个回调方法每次请求授权的时候都会唤起，所以在方法里需要用请求码去区分到底是哪种类型的请求。然后判断grantReaults数组是否包含元素——长度>0，然后判断grantResults的第0个元素是否和PackageMangeer.PERMISSION_GRANTED的值一致）

## 访问其他app的数据
呼...终于搞完了权限相关的基础内容，下面就是本章的正文：访问别的app中的数据。

内容提供器一般有两种用法：
* 一种是使用现有的内容提供器来读取和操作别的app中的数据。
* 另一种是创建自己的内容提供器为我们的app提供接口供别的app来访问我们的数据。

### ContentResolver的基本用法
每个app如果想要访问内容提供器，就必须要借助ContentResolver类，这个类的实例可以通过Context中的getContentResolver（）方法获取到。

ContentResolver中提供了一系列的方法用于对数据进行CRUD操作，比如insert（）、updata（）、query（）等。

ContentResolver中的增删改查并不接受表名作为参数，而是使用一个Uri参数，这个参数被称为**内容URI**，这个内容URI为内容提供器的数据建立唯一标识符。

URI由两部分组成：authority和path。

authority是用于对不同的app做区分的，一般是app的包名+.provider；

path是对于同一app中provider的不同的表做区分的，一般作为authority的后缀。

举个例子：com.example.app.provider/table1，这个URI中的“com.example.app.provider”是authority部分，后面的“/table1”部分是path。

最后，为了辨认这个字符串是内容URI，还需要在首部加上协议声明，最终就变成了：

content://com.example.app.provider/table1

然后还需要把这个字符串解析成Uri对象才可以作为参数使用：
```java
Uri uri = Uri.parse("content://com.example.app.provider/table1");
```
现在就可以使用query方法查询表了：
```java
Cursor cursor = getContentResolver().query();
```
这个query（）方法接受五个参数：
![P9DeN4.jpg](https://s1.ax1x.com/2018/06/24/P9DeN4.jpg)

query（）方法返回的依然是一个Cursor对象，前面已经使用过这个了，不再赘述。需要注意的是，使用完cursor对象之后要记得对对象调用close（）方法。

另外，对于其他的操作比如插入更新等等，也是构建一个ContentValues的对象作为行数据，然后调用insert（）、update（）方法去操作就可以了。

## 创建自己的内容提供器
需要自己新建一个类，继承自ContentProvider。ContentProvider类中有六个抽象方法，因此在实现子类的时候，需要全部实现这些方法：onCreate（）、query（）、insert（）、update（）、delete（）、getType（）。

onCreate（）一般做的就是一些创建和升级数据库的操作，返回true或者false表示内容提供器初始化成功与否。（只有ContentResolver要访问数据的时候才会调用初始化方法）

query（）、insert（）、update（）、delete（）都是用uri来确定要操作哪一张表，然后传入参数进行类似数据库的操作。

不同的是getType（）方法，这个方法返回一个Uri对象对应的MIME类型。MIME有三部分构成：以vnd开头+android.cursor.dir/或者android.cursor.item（选哪一个取决于Uri的结尾是路径还是id）+vnd.*authority*.*path*

**内容提供器一定要在AndroidManifest.xml中注册才能使用，不过使用Android Studio的快捷方式去新建的话，会自动完成。**

# 多媒体开发
P 282，暂时先不看，后面会补。

# 网络相关的开发

## WebView的使用
WebView也是控件之一，所以也可以在布局文件里正常书写。不过还需要做一些设置才能正常加载网页：
```java
WebView webView= (WebView) findViewById(R.id.webview);    webView.getSettings().setJavaScriptEnabled(true);
webView.setWebViewClient(new WebViewClient());
webView.loadUrl("https://www.baidu.com");
```
getSetting()方法可以设置一些浏览器的属性。
SetJavaScriptEnabled（）是让WebView支持JS脚本。

调用WebView的setWebViewClien

t（）方法并传入一个WebViewClient的实例变量是表示当一个网页要跳转到另一个网页的时候，还是在这个WebView中，而不是打开系统的浏览器。

调用loadUrl方法并传入一个用作URL的字符串，就可以让这个WebView加载指定的网页。

**因为访问网页是需要网络权限的，所以还需要AndroidManifest.xml中添加INTERNET权限**

## 使用HTTP访问网络
### 使用HttpURLConnection
```java
URL url = new URL("http://www.baidu.com");
HttpURLConnection connection = (HttpURLConnection)url.openConnection();
connection.setRequestMethod("GET");
connection.setConnectTimeout(8000);
connection.setReadTimeout(8000);
InputStream in= connection.getInputStream();
connection.disconnect();
```
* 先通过一个字符串创建一个URL对象。
* 然后对url调用openConnection（）方法开启一个http连接并赋值给一个HttpUrlConnection对象。
* 对这个HttpUrlConnection对象设置各种属性。
* 从HttpUrlConnection对象获取一个输入流。
* 新建一个输入流读取器。
* 从上一步的输入流读取器新建一个BufferedReader对象
* 对BufferedReader实例循环调用readLine（）来读入字符串，需要的话用StringBuilder变量拼接起来，作为http请求的response体。
* 完成请求之后调用disconnect（）关闭连接。

上面的步骤如果直接写，就是同步请求，如果要写异步请求后面多线程的部分会说到。

**用http请求去访问网络同样需要网络的权限，需要和前面一样去AndroidManifest.xml文件里声明权限。**

### 使用OKHttp进行网络请求
这个又是一个第三方框架，不仅在接口封装上做的简单易用，而且底层实现上也自成一派。

* 第一步还是先配置第三方库：在gradle里面添加“com.squareup.okhttp3:okhttp:3.4.1”的依赖
* 首先要创建一个OKHttpClient实例。
* 创建一个Request对象，Request的Builder（）之后可以连缀很多个方法来丰富这个请求体。
* 对OKHttpClient对象调用newCall（）方法传入前面=创建好的请求体，并调用execute（）方法来发送这个请求，execute（）的返回值用一个Response类的对象接收。
* 可以对Response对象调用body（）方法获取响应体，然后调用string（）方法来返回一个字符串。

以上就是一个GET请求的过程，说着感觉过程很多，但是代码很简洁：
```java
OkHttpClient okHttpClient = new OkHttpClient();
Request request = new Request.Builder().url("http://www.google.com").build();
Response response = okHttpClient.newCall(request).execute();
String responseData = response.body().string();
```

如果需要写POST请求，就需要构建一个RequestBody对象来存放请求体的参数：
```java
RequestBody requestBody = new FormBody.Builder().add("username","admin")
				  .add("password","123456")
				  .build();
```
然后在requset的连缀方法里多加一个post（），传入请求体，之后就和和GET的操作一样了。

### 解析XML
P.321 暂时先不看。
### 解析Json 
* 新建一个JSONArry或者JSONObject（取决于具体的json数据的结构）
* 对于JSONArray，可以遍历其中的数据，每个元素都是JSONObject
* 对于JSONObject，直接调用getString（）方法取出字符串就可以了。

### 用GSON解析JSON
GSON是一个google提供的开源库，好处是可以直接映射赋值到某个类的成员变量。
* 先配置第三方库，在gradle里添加‘com.google.code.gson:x.x’
* 新建一个Gson对象
* 对Gson对象调用fromJson（）方法，传入两个参数：jsondata原字符串、模板类.class。
* 如果结构里面有数组结构，用TypeToken将目标类型传入fromJson（）方法中：
```java
List<Person> people = gson.fromJson(jsonData,new TypeToken<List<Person>>(){}.getType());
```

### 回调机制
回调机制普遍存在在各种语言的设计中，总的来说就是A调用B的方法，B在耗时操作这个方法，然后方法执行完了以后B再调用A的某个方法，这样的机制就是回调。

java中的实现是依靠interface的特性实现的。
* 新建接口类CallBackListener：
```java
public interface CallBackListener {
    public void OnFinished(String callBackData);
    public void onError(IOException e);
}
```
* 让A类实现接口类的接口：
```java
public class A extends AppCompatActivity implements CallBackListener
```
然后重写接口中的方法，作为收到回调之后的处理：
```java
@Override
public void OnFinished(String callBackData) {
	Log.d("A","callback succeed");
}
@Override
public void onError(IOException e) {
}
```
* 在B类中写耗时操作，并对传入的接口类实例调用接口定义中的方法“；
```java
public class B {
    public void verySlow(CallBackListener callback,String question) {

        for (int i = 0; i < 100000; i++);//模拟耗时操作

        String string = "i am very slow , but i had finished it " + question;
        callback.OnFinished(string);
    }
}
```
如果要使用回调，就在A类里持有一个B类的实例，然后新开一个线程，在A类中调用B类的方法。
```java
new Thread(new Runnable() {
	@Override
	public void run() {
		justDoIt.verySlow(MainActivity.this,"1+1");
		Log.d("MainActivity","call finished");
	}
}).start();
```

# 后台服务
服务是Android中能够让程序后台运行的办法，这种方法很适合去做那种不需要用户交互的、长期运行的任务。

服务不是一个独立运行的进程是，而是依赖于创建服务的那个进程。所以如果创建服务的进程被杀死之后，这一票依赖于这个进程的服务都会挂掉。

服务不会自己开启线程，所有的代码默认在主线程里运行。所以就需要手动去创建子线程。

## 多线程编程
### 线程的基本用法
定义一个新的线程只需要新建一个类继承自Thread，然后重写run（）方法，并在里面编写耗时操作的逻辑代码就好。

新建完线程类之后，如果想让这个线程启动，对线程对象调用start（）方法就可以。

但是这样写会导致较高的耦合性，更多的时候其实是通过实现Runnable（）接口的方式来自定义线程的：
```java
public class Mythread extends Thread implements Runnable {
    @Override
    public void run() {
        Log.v("MyThread"," run myThread with interface Runnable");
    }
}
```
或者如果不想另外新建一个类去实现接口的方法，也可以直接使用匿名类（而且使用场景更多）：
```java
new Thread(new Runnable() {
	@Override
	public void run() {
		//do something
	}
});
```
### 在子线程中更新UI
** Android的UI线程是不安全的，如果想要更新UI元素，就必须在主线程中进行，否则会出现异常**

但是有的时候，我们会开启一个用于处理耗时操作的子线程，然后在子线程处理完之后改变UI的内容。对于这种情况，Android提供了一套异步消息处理的机制。

先看看怎么使用：
```java
public static final int UPDATE_TEXT = 1;
private TextView textView;
private Handler handler = new Handler(){
	public void handleMessage(Message msg){
		switch (msg.what){
			case UPDATE_TEXT:
				textView.setText("changed");
				break;
			default:
				break;
            }
        }
    };
```
先在Activity类中新增三个成员变量：一个是用来标识“更新某个控件”这个动作的标志变量、一个是对目标控件的引用、一个是Handler变量，在新建的时候就可以创建一个代码部分，里面实现了handleMessage（）方法，这个代码就是先判断传入的Message实例是哪一个操作，然后写UI的更新逻辑代码。
```java
new Thread(new Runnable() {
	@Override
	public void run() {
        Message message = new Message();
		message.what = UPDATE_TEXT;
		handler.sendMessage(message);
	}
}).start();
```
这个部分就是异步开启一个线程，然后新建一个Message对象，指定Message对象的what属性为前面用来标识更新某个UI控件的标志变量，然后对Handler的成员变量实例调用sendMessage（）方法，传入刚才新建好的message对象，就可以了。

### 异步消息机制的原理简析
前面了解了Handler的用法，现在再来看一下异步消息处理的机制是怎么一回事，为什么这种机制能够处理UI线程。

这种机制由四部分构成：Message、Handler、MessageQueue、Looper。
#### Message
Message是线程之间传递的**消息**，可以携带少量数据。上面在构建Message对象的时候，设置过一个what的属性，除此之外还可以使用arg1、arg2来携带整型数据、用obj属性携带一个Object对象。

#### Handler
用于发送和处理消息，发送消息一般是使用Handler的sendMessage（）方法，之后消息兜兜转转，最终会回到HandleMessage（）方法。所以上面在声明Handler对象的时候就自己实现了handleMessage（）方法，因为这个是最终要执行的方法。

#### MessageQueue
消息队列，用于存放所有通过Handler发送的消息，这些消息们会一直存在在这个队列中，等待着被一个接一个的处理掉。

**每个线程只有一个MessageQueue对象**
#### Looper
Looper是每个线程中的MessageQueue的管家，调用Looper的loop（）方法，就会让Looper把MessageQueue中的message循环取出并传递到Handler的handleMessage（）方法中。

**每个线程也只有一个Looper**

### 使用AsyncTask
Android还基于异步消息处理机制封装了AsyncTask以便从子线程切换到主线程，AsyncTask是一个抽象类，如果需要使用，就需要子类去继承它。

三个泛型参数：

* Params：在执行AsysncTask时传入的参数，可以在后台任务中使用。
* Progress：如果需要 在界面上显示当前进度，这个泛型参数就是进度的单位。
* Result：指定返回值的类型

```java
class DownloadTask extends AsyncTask<Void,Integer,Boolean>{
	@Override
	protected void onPreExecute() {
		super.onPreExecute();
	}
	@Override
	protected Boolean doInBackground(Void... voids) {
		return null;
	}
    @Override
    protected void onProgressUpdate(Integer... values) {
		super.onProgressUpdate(values);
	}

	@Override
	protected void onPostExecute(Boolean aBoolean) {
		super.onPostExecute(aBoolean);
	}
}
```
重写了四个方法:

- onPreExecute：在后台任务开始之前调用，做一些初始化操作，弹出显示进度条、对话框等。
- doInBackground：这个方法中的代码都会在子线程中，可以用来处理耗时任务，完成之后就可以通过return预计来返回结果。因为在子线程中，所以这里不能更新UI，如果要更新UI元素，可以调用publishProgress（）方法来实现。
- onProgressUpdate：该方法中携带的参数是在后台的publishProgress（）方法发送过来的，这个方法中可以对UI进行操作。
- onPostExcute：这个方法也是出于主线程的，当后台任务执行完return回来的时候就会调用这个方法，这个方法可以提示后台任务的执行结果或者关闭进度条等等。

## 服务的基本用法

作为Android四大组件之一，服务也是很重要的一部分。

### 定义一个服务

可以使用Android Studio的快捷方法去新建一个服务类：

```java
public class MyService extends Service {
    public MyService() {
    }

    @Override
    public int onStartCommand(Intent intent, int flags, int startId) {
        return super.onStartCommand(intent, flags, startId);
    }

    @Override
    public void onDestroy() {
        super.onDestroy();
    }

    @Override
    public void onCreate() {
        super.onCreate();
    }

    @Override
    public IBinder onBind(Intent intent) {
        // TODO: Return the communication channel to the service.
        throw new UnsupportedOperationException("Not yet implemented");
    }
}
```

新建的服务类还需要重写一些方法去完成功能，这里重写了onCreate（）、onStartCommand（）、onDestroy（）方法，也很好理解，就不细说了。

服务也需要在AndroidManifest.xml文件中注册，不过因为用的是IDE的快捷方法，它已经自己写好了：

```xml
<service
	android:name=".MyService"
	android:enabled="true"
	android:exported="true">
</service>
```

### 启动和停止服务

前面已经创建好了一个服务，现在来启动一下这个服务：

```java
Intent startServiceIntent = new Intent(this,MyService.class);
startService(startServiceIntent);
```

代码也很简单，就是新建一个Intent，参数中传入自定义service的类，然后调用startService（）方法传入这个intent就可以启动了。



停止服务也很简单：

```java
Intent startServiceIntent = new Intent(this,MyService.class);
stopService(startServiceIntent);
```

和上面一样，可以这样手动停止。

另外，可以在MyService类中的任意位置调用stopSelf（）方法就可以让服务自己停下来。

### 活动和服务的通信

前面的启动和停止都是Activity调用方法去控制服务的开始和结束，但是中间的具体执行细节、进度等等信息Activity并没有得知。如果需要Activity和服务有更紧密的联系，就需要使用onBind（）方法了。

```java
class DownloadBinder extends Binder{
        public void startDownload(){
            Log.v("MyService","startDownload");
        }
        public int getProgress(){
            Log.v("Myservice","getProgress");
            return 10;
        }
    }
```

使用的具体方法时新建一个内部类，然后在service类的内部新建一个Binder子类的成员变量，在onBind（）方法中返回这个成员变量。

这样就完成了service类的部分，如果需要实现活动和服务的通信还需要在Activity类中写一些的代码：

```java
    private MyService.DownloadBinder downloadBinder;
    private ServiceConnection connection = new ServiceConnection() {
        @Override
        public void onServiceConnected(ComponentName componentName, IBinder iBinder) {
            downloadBinder = (MyService.DownloadBinder) iBinder;
            downloadBinder.startDownload();
            downloadBinder.getProgress();
        }

        @Override
        public void onServiceDisconnected(ComponentName componentName) {

        }
    };
```

在Activity类中添加一个成员变量：一个service内部类Binder的实例。和一个ServiceConnection的匿名类，在匿名类里面重写了onServiceConnected（）方法和onServiceDissconnected（）方法。这两个方法会在 活动和服务绑定和解绑时候调用。

这样，就可以通过在Activity中对Binder实例这个成员变量调用方法，实现在服务里执行某个方法。

最后就是将服务和Activity绑定起来了：

```java
Intent bindIntent = new Intent(this,MyService.class);
bindService(bindIntent,connection,BIND_AUTO_CREATE);
```

依照惯例，先新建一个intent对象，然后用bindService（）方法将intent、ServiceConnection的实例绑定起来，第三个参数是表示Activity和通知绑定起来之后会自动新建服务，这时候Service类中的onCreate（）方法会被回调执行，但是onStartCommand（）方法不会执行。

要解绑也很简单，直接调用unbindService（）传入ServiceConnection的实例就可以解绑了。

**任何服务在整个APP的范围内都是通用的，一个服务可以和任意活动绑定，绑定后获取到的Service的bind子类实例是相同的。

### 服务的生命周期

- 服务开始于create，回调onCreate（）方法
- 然后在app的任何位置调用了startService（）方法，该服务就会启动起来，并并回调onStartCommand（）方法。
- 启动之后服务会一直运行，直到stopService（）方法或者service类内部的stopSelf（）方法调用。
- 当startService（）调用后，再调用stopService（）方法，就会回调onDestory（）方法，表示服务被销毁。或者在bindService（）之后再调用unbindService（）方法也会被销毁。如果同时startService（）和bindService（）都被调用过，那销毁服务就需要stopService（）和unbindService（）方法都调用。

**每个服务只会存在一个实例，所以无论对同一个服务启动了多少次，实际上只有一个，调用一次stopService（）或者stopSelf（）就停止运行了。**

### 服务的使用技巧

#### 前台服务

服务几乎都是在后台工作，它的系统优先度其实比较低，如果系统内存不足的时候，可能就会回收掉正在运行的服务。

如果希望服务可以一直保持运行，就可以使用**前台服务**

前台服务和后台服务的一个区别在于，前台服务会有一个正在运行的图标在系统的状态栏显示，很类似通知的效果。

前台服务的使用并不仅仅是为了避免被回收，比如天气类的app，它就需要这样一种的效果——在后台更新天气数据的时候，还可以在状态栏一直显示当前的天气信息。

- 在Service类中加入如下代码：

```java
intent = new Intent(this,MainActivity.class);
NotificationChannel  channel = new NotificationChannel("5555","foreground",
NotificationManager.IMPORTANCE_HIGH);
PendingIntent pi = PendingIntent.getActivity(this,0,intent,0);
Notification notification = new Notification.Builder(this,"5555")
                .setContentTitle("this is content title")
                .setContentText("this is content text")
                .setWhen(System.currentTimeMillis())
                .setSmallIcon(R.mipmap.ic_launcher)
                .setLargeIcon(BitmapFactory.decodeResource(getResources(),R.mipmap.ic_launcher))
                .setContentIntent(pi)
                .build();
startForeground(1,notification);
```

书上的方法有的部分已经被废弃了，这个是新一些的方法：新键了一个intent，然后新建了一个Notification的Channel并，为后面的通知实例指定这个channel 的id，然后调用service的startForeground（）方法，传入这个notification的实例，就可以启动一个前台服务。

#### 使用IntentService

因为服务使用的还是在主进程的主线程，所以如果用服务去执行耗时操作，就会出现ANR（Application Not Responding，未响应）情况。

所以如果需要使用后台服务去执行耗时操作，就需要在service的类中开启新的线程去执行耗时任务。这时候新的线程开了以后就没有办法去控制了，除非直接停止这个服务。

所以就可以在耗时操作后调用stopSelf（）方法。

实际上，Android提供了一个专门用于开启带线程的后台任务的类：IntentService

新建一个类继承自InetntService，然后重写其onHandleintent方法，这个里面就可以写一些耗时操作，然后构造方法和onDestory（）方法就执行父类的方法就可以（看具体情况）。

**这个类可以自动开辟新的线程去执行任务，而且最后的onDestory（）方法也可以自动执行。**

# Material Design 风格的UI

2014年，google推出了一套全新的UI设计语言，包括视觉设计、运动、互动效果等等。想让各个app的设计都采用这套设计语言（可惜的很多公司还是没有接受这个...）

简而言之，它实际上也是包括了一套UI，这套控件体现了Material Design 的UI风格。

## Toolbar

之前的所有默认的Activity都会有一个ActionBar的控件，就是最上面的一个条。官方已经不再推荐使用ActionBar了，转而推荐ToolBar。ToolBar继承了ActionBar的所有功能，而且灵活性很高，可以配合其他空间去完成Material Design的效果。

### ToolBar 的简单体验

Activity默认带的ActionBar是由AndroidManifest.xml中的android:theme属性中指定的AppTheme主题而来的。这个AppTheme在res/values/styles.xml文件中定义的，这个文件长这样：

```xml
<resources>
    <!-- Base application theme. -->
    <style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">
        <!-- Customize your theme here. -->
        <item name="colorPrimary">@color/colorPrimary</item>
        <item name="colorPrimaryDark">@color/colorPrimaryDark</item>
        <item name="colorAccent">@color/colorAccent</item>
    </style>
</resources>
```

可以看到，定义的AppTheme的parent主题是Theme.AppCompat.Light.DarkActionBar，所以之前的Activity带有ActionBar是因为指定了这个主题。

这里如果想要使用ToolBar代替ActionBar，就需要设置这个AppTheme为NoActionBar。

然后就需要在activity的布局代码中写一个toolbar的控件：

```xml
<android.support.v7.widget.Toolbar
        android:id="@+id/toolbar"
        android:layout_width="match_parent"
        android:layout_height="?attr/actionBarSize"
        android:background="?attr/colorPrimary"
        android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
        android:popupTheme= "@style/Theme.AppCompat.Light"
        >
</android.support.v7.widget.Toolbar>
```

可以看到和一般的控件有一些不太一样的地方，不过都大同小异。

再然后就是在activity的代码中写：

```java
Toolbar toolbar = (Toolbar)findViewById(R.id.toolbar);
setSupportActionBar(toolbar);
```

可能会有报错的提示，只需要把包含的库改为import android.support.v7.widget.Toolbar;就可以了，因为setSupportActionBar是个库提供的，而自动包含可能写成另一个ToolBar的库。

目前为止，实现了一个ToolBar，但是看上去的实现效果和ActionBar并无二致，这是因为暂时还没有应用到ToolBar的一些效果，但是实际上就已经有可以实现Material Design的能力。

如果想要实现更丰富的ToolBar就需要自定义ToolBar了：

### 自定义ToolBar

先新建一个toolbar.xml布局文件作为自定义ToolBar的布局：

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">
    <item
        android:id="@+id/backup"
        android:icon="@drawable/ic_launcher_background"
        android:title="Backup"
        app:showAsAction = "always"
        >
    </item>
    <item
        android:id="@+id/delete"
        android:icon="@drawable/ic_launcher_background"
        android:title="Delete"
        app:showAsAction="ifRoom"
        ></item>
    <item
        android:id="@+id/settings"
        android:icon="@drawable/ic_launcher_background"
        android:title="Settings"
        app:showAsAction="never"
        ></item>
</menu>
```

和前面的控件的布局不同的地方在于：多了一个app:showAsAction来指定按钮的显示位置，因为这里也用到了app的字段，所以在开头需要用xmlns去指定这个命名空间。

showAsAction主要有几种值可选：

- always：表示永远显示在ToolBar中，如果屏幕空间不够就不显示了。
- ifRoom：如果屏幕空间足够，就显示在ToolBar中，如果不够的话就显示在菜单中。
- nerver：永远显示在菜单中。

**ToolBar中的action按钮只显示图标，菜单中的action只显示文字**

## 滑动菜单

滑动菜单其实就是侧滑栏，iOS中并没有提供原生的侧滑栏，但是安卓提供了一个Drawerlayout控件，就可以借助这个控件来实现侧滑栏。

### Drawerlayout

这个DrawerLayout是一个布局，在布局中允许防止两个直接子控件：主屏幕中显示的内容、滑动菜单中显示的内容。

```xml
<android.support.v4.widget.DrawerLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id = "@+id/drawer_layout"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <FrameLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent">
        <android.support.v7.widget.Toolbar
            android:layout_width="match_parent"
            android:layout_height="?attr/actionBarSize"
            android:background="?attr/colorPrimary"
            android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
            app:popupTheme="@style/ThemeOverlay.AppCompat.Light"
            >
        </android.support.v7.widget.Toolbar>
    </FrameLayout>

    <TextView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_gravity="start"
        android:text="123456"
        android:textSize="30sp"
        android:background="#FFF"   />
</android.support.v4.widget.DrawerLayout>
```

现在的activity的布局文件变成了这样，最外层是DrawerLayout，然后在里面写了一个ToolBar作为主屏幕的显示内容，在侧滑栏里写了一个TextView。

**对于第二个控件的layout_gravity的属性是必须指定的，表示了侧滑栏的滑出位置。

但是如果用户没有划出来就不知道是否有侧滑栏的存在。所以建一个做法是在ToolBar的最左边加一个导航按钮，这样点击导航按钮也可以打开侧滑栏。

```java
drawerLayout = (DrawerLayout)findViewById(R.id.drawer_layout);
ActionBar actionBar = getSupportActionBar();
if (actionBar!=null){
	actionBar.setDisplayHomeAsUpEnabled(true);
	actionBar.setHomeAsUpIndicator(R.drawable.ic_launcher_background);
}
```

首先，先获取到这个DrawerLayout的实例，再获取到可用的ActionBar，因为现在用的ToolBar去实现ActionBar，所以获取到的实际上就是ToolBar。然后让ActionBar实例把HomeAsUp按钮显示出来。

现在就设置好了ToolBar上的显示，最后让点击到这个按钮时候显示出DrawerLayout就可以了：

```java
    @Override
    public boolean onOptionsItemSelected(MenuItem item) {
        switch (item.getItemId()){
            case android.R.id.home:
                drawerLayout.openDrawer(GravityCompat.START);
                break;
        }
        return super.onOptionsItemSelected(item);
    }
```

可以看到，对DrawerLayout实例调用openDrawer（）方法就可以打开侧滑栏了，传入的参数表示的依旧是打开侧滑栏的方向。

### NavigationView

NavigationView是DesignSupport库中提供的一个控件，可以更为简单的实现滑动菜单的页面。

暂时先不看，等到具体需要使用的时候回回来补笔记。因为目前看到的国内的很多app也并没有使用这套设计语言，所以使用场景暂时还是很受限的，当然了，如果要去制作个人的app还是很不错的一种app的设计风格，可能会和原生系统UI统一而高效。

## 悬浮按钮和可交互提示

### FloatingActionButton

默认使用colorAccent作为按钮的颜色。可以在布局文件中设置其阴影效果（借此来表示悬浮高度）。其他的也没有什么特性，和普通的按钮使用效果是一样的。暂时不做样例代码的学习。

### Snackbar

这个是在屏幕下方显示一个条状视图和一个按钮，用于用户交互（比如相机删除照片后会有一个Snackbar提示要不要撤销。

### Coordinatorlayout

这个可以说是一个加强版的Framelayout，也是有Design Support库提供的，可以监听所有子控件的各种事件，然后做出合理的响应，比如避免控件遮挡的自动偏移操作等等。并且替换原来的FrameLayout也很简单，能够向下兼容，直接换掉关键字字段皆可以了。

## 卡片式布局

卡片式布局的意思就是，让页面中的元素看起来就像在卡片中一样，并且还有圆角和投影。

### CardView

由appcompat库提供的一个重要控件，实际上它也是一个FrameLayout，知识额外提供了圆角和阴影效果，看上去有立体的感觉。

### AppBarLayout

上一个CardView的RecyclerView会遮挡住ToolBar，为了不遮挡，还有另外一种解决办法——AppBarLayout，它是一个垂直方向上的LinearLayout，内部做了很多滚动事件的封装，也应用了一些Material Design的设计理念。

具体使用其实就是两步：

- 将ToolBar嵌套到AppBarLayout中。
- 给RecyclerView指定一个布局行为app:layout_behavior=“@string/appbar_scrolling_view_behavior”

这样就不会遮挡了。

（PS:在TollBar中可以指定一个app:layout_scrollFlags=“scroll|enterAlways|snap”的字段属性，这样就可以实现上划时候自动隐藏ToolBar，在下划的时候又自动显示。）



## 下拉刷新

Google提供了现成的刷新控件，用就好了。

SwipeRefreshLayout是用来实现这个效果的核心类，由support-v4库提供。只要把想要实现下拉刷新的控件放到SwipeRefreshLayout中就可以了。

要实现这个控件的效果的第一步就是在想要下拉刷新的RecyclerView布局代码的部分的外层包裹一层SwipeRefreshLayout的布局代码。

然后在Activity的内部持有一个SwipeRefreshLayout成员变量，然后通过findViewById（）方法和布局文件中的控件对应起来。

再然后对这个SwipeRefreshLayout的成员变量调用setOnRefreshListener（）方法来处理下拉动作触发的回调事件（注意不要直接把耗时操作写在这个里面，可能会导致ANR的！）。

## 可折叠式标题栏

### CollapsingToolbarLayout

这是一个可以极大扩充ToolBar的使用效果的布局，不过它无法直接存在，而是要作为AppBarLayout的直接子布局来使用。进一步地，AppBarLayout又必须作为CoordinatorLayout的子布局。

这个布局实现的效果就是微信朋友圈的那种效果。

### 充分利用状态栏的空间

这个是要实现将系统的状态栏做成全透明的，和前面的CollapsingToolbarLayout实现效果融为一体。



# 一些其他的技巧

## 全局获取Context的技巧

Android开发中几乎处处要用到context，在目前的学习中，似乎获取到context不算多难，因为activity类就是context对象。

但是后面的开发会逐渐脱离activity类，如果这时候需要context，就有点不那么容易从activity获取context了。

Android提供了一个类——Application类，每当程序启动的时候，系统就会自动将这个类初始化，所以我们可以定制一个自己的Application类，以便于管理程序内部的全局状态信息。比如全局Context。

- 新建一个类继承自Application
- 重写onCreate（）方法，并提供get方法。
- 在AndroidManifest.xml中的application字段指定我们刚刚新建新建的自定义Application子类。
- 之后需要使用的时候，就对新建的Application类调用get方法就可以了。
- 如果和其他的Application类冲突的话，在自定义Application类的初始化方法里调用别的Application的初始化方法就可以了。**一个App只能配置一个Application**。

## 使用Intent传递对象

### Serializable方式
序列化一个对象，将其转化为可存储或者可传输的状态。

要实现序列化，只要让这个需要被传递的类去实现Serializable接口就可以了。然后还是按照常规的put方法传递这个对象，但是在接收的时候取值就不同了：

```java
Person person = (Person) getIntent().getSerializableExtra("Person_data");
```

### Parcelable方式

这种方式比上一种要复杂一些，但是上一种要全部对象都序列化，这个是只对需要的

首先还是要实现Parcelable的接口，必须重写两个方法：describeContents（）、writeToParcel（）。

第一个方法返回0就可以了。第二个需要调用Parcel的writeXXX（）方法，将本类的成员变量一个个写入进去。

然后还需必须提供一个CREATOR的常量，创建Parcelable.Creator的接口实现，指定泛型为Person。

实现接口就需要重写两个方法：createFromParcle（）、newArray（）。

在createFromParcle（）方法中需要确定格式：读取刚才写的成员变量字段，并创建一个本类对象返回。（调用read的顺序一定要和前面的write（）方法顺序一致）。

在newArray（）方法的实现：只需要new 出一个Person数组就可以了。

## 定制自己的日志工具

前面的学习中，为方便调试，在代码中插入了很多日志，但是如果发布包的时候还带有这些日志信息的话，可能会导致信息泄露或者APP的效率下降。

所以就需要有一个工具，在开发调试阶段有日志的打印信息，但是在发布的时候就把日志去掉。

实现方法是自定义一个类，去实现一些Log的逻辑，用一个level变量去控制打印信息，默认设置为verbose，到时候如果需要发布，就修改这个level值，这样就会避免打印log。

## 调试Android APP

从IDE的运行选项届可以选择Debug按钮，然后在IDE里面就能看到各种信息，这个在后面的使用中会慢慢熟悉。

## 创建定时任务

定时任务的实现一般有两种方式：Java里的Timer、Android里面的Alarm机制。

Timer有一个明显的短板，不太适合那些长期在后台运行的任务，容易被休眠策略影响。

而Alarm有唤醒CPU的功能 ，保证大部分情况下，可以正常工作。

### Alarm机制

借助AlarmMangager类实现：

```java
AlarmManager manager = (AlarmManager)getSystemService(Context.ALARM_SERVICE);
long triggerAtTime = SystemClock.elapsedRealtime() + 10*1000;
Intent intent = new Intent(this,LongRunningServices.class);
PendingIntent pendingIntent = PendingIntent.getService(this,0,i,0);
manager.set(AlarmManager.ELAPSED_REALTIME_WAKEUP,triggerAtTime,pendingIntent);
```

（上面的代码仅仅是一个演示，参数需要根据实际情况去写）

因为时间的单位是毫秒，所以这里的秒数需要乘1000。然后在manger的set方法的三个参数分别传入：

- 用于指定AlarmManger的工作方式。可选ELAPSED_REALTIME（触发时间从系统开机算起，不会唤醒CUP）、ELAPSED_REALTIME_WAKEUP（可以唤醒CPU）、RTC（触发时间从1970年1月1日0点开始算起，不唤醒）、RTC_WAKEUP（可以唤醒）。
- SystemClock.elapseRealtime（）方法可以获取到系统开机到现在为止经过的毫秒数。（同样的，调用System.currentTimeMillis（）方法可以获取到从1970年1月1日0点至今所经历的毫秒数）
- 获取到一个广播或者服务，这样在定时任务执行的时候，对应的服务的onStartCommand（）或者服务的onReceive（）方法就能得到执行。



**在Android4.4开始，这个Alarm方式的触发开始变得不准确，不过这个不是bug，而是系统的省电优化，把附近几个时段的Alarm一块执行。如果仍然需要准确执行，可以用setExact来代替set方法。而且考虑到逻辑代码的顺序执行带来的时间误差，可以用多线程的方式保证逻辑代码能够在触发后及时地被执行。**



### Doze模式

Android一直在优化耗电控制，但是还是挡不住后台服务的滥用，所以在Android6.0加入了Doze模式，可以大幅度优化耗电量过大的问题。

简单来说，就是系统在检测到手机没有充电，而且关闭了屏幕一段时间，就会进入Doze模式，系统这时候会对CPU、网络、Alarm等活动进行限制。

系统也不会一直处于Doze状态，而是会隔段时间唤醒一次，但是间隔会越来越长。

在Doze模式下：

- 网络不能访问
- 忽略唤醒CPU或者屏幕操作
- 系统停止扫描WIFI
- 停止同步服务
- Alarm任务会在下次推出Doze模式的时候执行

## 多窗口模式编程

多窗口其实就是分屏，其实默认都是支持分屏的，只不过在分屏模式下，很多控件的尺寸会发生变化，所以这时候就要考虑在写布局的时候，多使用match_parent属性、RecyclerView等空控件，尽量避免出现屏幕尺寸变化过大带来的程序异常显示。

### 多窗口模式下的生命周期

其实App的生命周期并没有发生大的改变，只是：同时出现的两个APP，当用户和其中的一个交互的时候，另外一个虽然也是处于完全可见的状态，但是还是进入到了onPause（）的回调模式中。所以在一些视频播放的APP开发，就不要在onPause（）的方法中暂停掉播放。

另外，APP进入多窗口模式的时候Activity会被重新创建。要是想改变这种默认的机制，可以在AndroidManifest.xml中对Activity的配置加入configChanges字段进行配置。这样一来，屏幕发生变化的事件会回调Activity的onConfigurationChanged（）方法中，可以根据具体情况去重写这个方法。

### 禁用多窗口

有的时候我们不希望APP在多窗口状态下运行，就可以在AndroidManifest.xml文件中配置application或者Activity的resizeableActivity字段为false，表示不支持。

上述方法只在targetSdkVersion24以及更高的版本中生效，在低版本的系统中，会提示可能不能正常工作，但是还是会进入多窗口模式。不过，在低版本的规定中，如果不允许横竖屏切换就不能进入多窗口模式。所以低版本的设置可以在AndroidManifest.xml中声明screenOrientation字段为portrait（只支持竖屏）或者landscape（只支持横屏）就可以了。

## Lambda表达式

这个是java8新引入的特色功能。而且，这个功能最低支持到Android2.3呢。而其他的stream API等却只能支持7.0之上的系统版本，兼容性就很不好，所以项目的开发中能够使用的也就是这个Lambda表达式了。

来一个具体的使用场景感受一下

```java
button.setOnClickListener(v -> {
	//do something 
});
```

而原来的写法是：

```java
button.setOnClickListener(new View.OnClickListener() {
	@Override
	public void onClick(View view) {
		//do something
	}
});
```

只要是只有一个待实现的方法的接口，都可以使用Lambda表达式去写：

```java
Runnable runnable = new Runnable(){
    @Override
    public void run(){
        //do something 
    }
};

//Lambda:
Runnable runnable = () ->{
    //do something 
}
```



# 总结

啊… …

终于告一段落了，这本《第一行代码》我本来是打算尽快过一遍的，但是不断的学习中，发现还是有很多地方和iOS有很大的不同，当然也有很多联系，但是在这个过程中，我喜欢一边学习Android的设计，一边和iOS开发中的一些设计对应起来，所以进度慢了很多。

而且其实有一部分的内容没有涉及到，比如多媒体的部分和定位的部分。这一块我打算后面如果有时间的话，会补上这部分的内容，因为这些其实偏向于模块，不会影响到正常的逻辑开发等等。

不过好的一点是，现在在看完这本书，能够上手去做一些简单的事情——比如创建项目，跟着示例去完成一些功能和效果等等。还有一个重要的收获就是，慢慢熟悉了Android的开发环境和开发思维，虽然目前接触到的开发的知识还很少，也还没有真正地开始写项目，不可避免地手生。但是在能够上手之后，就是快速积累实际经验的时候，希望这部分的学习能对之后的工作和学习有所增益，就算没有增益也没啥关系，学的越多，眼界就越开阔。

总而言之，过了一遍课本，积累了一点点零散的开发知识，后面就是去上手写一个简单的APP，然后在不断的踩坑中进步。加油呀自己~~~