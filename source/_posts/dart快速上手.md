title: dart快速上手
author: Leo Chang
tags:
  - dart
categories:
  - 快速上手
  - 踩坑
date: 2019-01-25 23:38:00
---
dart语言用于flutter开发，所以最近在dart的语法学习的过程中总结一点从其他语言到dart的异同点，以便少踩坑。（语法参考的是官方文档）

<!--more-->

### 1. 字符串中引用变量或者表达式的值：

C语言的printf函数中是使用%符号作为变量或者表达式的引用：
```c
printf('a`s value is %d ', a);
```
在dart中是这样的：
```dart
print('a`s value is $a');
//dart使用的是$符号。
```

### 2.final和const
const也是final类型的，不过const是编译时常量，final是在第一次使用的时候初始化的，第一次赋值的时候就确定下来了，之后再不能修改了。所以实例变量可以是final但是不可以是const修饰。

const变量如果声明在类中，最好直接定义为static const。
除此之外，const还用来声明一些不变的值（不可变对象、数组等）。

### 3.bool类型很重要
在其他的语言中，有时候是很习惯用一些空值or非空值变量本身作为判断条件：
```c
if(a){
	printf("a != 0")
}
```
这样看也没什么问题，如果a的值是0，条件判断会为假。

但是在dart中就不一样了，dart的判断条件只认正经bool类型，所以对于如上代码，dart总会判断为假，因为a变量是一个int类型，压根就不是bool类型，更不可能为true对象。

所以在dart中，需要显式判断数据，然后返回一个布尔值作为if、while等的判断条件。

### 4.方法定义
诚然在类中还是不可以再定义一个类，不过dart的方法定义中是可以再定义一个方法的。

### 5.默认参数语法的版本变动
1.21之前的dartSDK版本是用 ：冒号指定可选参数的默认值，后来版本更新了以后，需要用 = 等号来指定。如果遇到了升级版本后出现的语法错误，需要注意一下这里。

### 6.级联操作符
dart也有支持类似链式调用的语法，区别于其他常见语言的 “.”点号，dart使用的是“..”，叫级联操作符（然而实际上并不是一个操作符，而是一个语法）。
```dart
querySelector('#button') // Get an object.
  ..text = 'Confirm'   // Use its members.
  ..classes.add('important')
  ..onClick.listen((e) => window.alert('Confirmed!'));
```
（偷官方那个例子用嘻嘻）
第一个方法返回一个对象，然后后面的操作语句的对象都是第一个返回的那个对象（的成员变量），并且后续操作的返回值都忽略掉了。

级联操作符可以嵌套使用。

### 7.类的构造方法
dart的面向对象是单继承。dart提供了一个语法糖可以简化构造方法的一些比较枯燥单一的赋值操作：
```dart
class Point {
  num x;
  num y;

  Point(num x, num y) {
    // There's a better way to do this, stay tuned.
    this.x = x;
    this.y = y;
  }
}
```
语法糖如下：
```dart
class Point {
  num x;
  num y;
  
  // Syntactic sugar for setting x and y
  // before the constructor body runs.
  Point(this.x, this.y);
}
```
并且子类并不继承父类的构造方法。

**在构造函数调用其他函数的时候，在 ：之后调用其他的方法：**
```dart
Point.alongXAxis(num x) : this(x, 0);
```


**需要调用父类的初始化方法，并且父类只有一个命名初始化方法时，则必须** ：
```dart
Son.fromJson(Map data) : super.fromJson(data) {
    print('in Son Class');
  }
```

**dart提供了初始化列表的语法：**
```dart
Son.fromJson(Map jsonMap)
      : x = jsonMap['x'],
        y = jsonMap['y'] {
    print('In Son.fromJson(): ($x, $y)'); //用:开始参数列表的赋值，用逗号分隔参数，右边不能使用this引用成员变量
```


**几种构造方法的调用顺序如下：**

->
initializer list（初始化参数列表） 

->
superclass’s no-arg constructor（超类的无名构造函数）

->
main class’s no-arg constructor（主类的无名构造函数）

**如果该对象的实例是一个不可变的对象，使用常量构造函数**
在构造函数之前声明const关键字，并且所有成员变量为final。

**如果要使用工厂模式的构造方法，可以使用factory关键字**
工厂模式的构造方法并不是每次都直接生成一个实例。有时候是需要从缓存中找到一个实例并返回，有的时候是需要返回某一个子类的实例（比如iOS开发中的UIButton，工厂方法返回的是UIButton的子类的实例）

### 7.类的访问控制
dart并没有关键字来表示私有还是开放访问，所以只是比较简单使用“ _ ” 来标识私有成员

### mixins 混合
要重复使用类中的代码但是又不用走继承方式，那么就可以使用mixins方式去组合两个类：
```dart
// 使用with关键字，表示类C是由类A和类B混合而构成
class C = A with B;
```

### 类方法
类方法需要用static关键字来实现为静态函数，当然类方法中是不可以访问this的。

### 包的引入支持懒加载
可以用deferred as 来标识要懒加载的包。然后在使用的时候用loadLibrary（）函数来加载库（可以指定是同步方式还是异步方式）。

### 注释
当然双斜线还是可以用的啦～


>(*之后有空还会更新哦～*)