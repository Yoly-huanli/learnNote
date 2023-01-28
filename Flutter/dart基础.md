[toc]

# 变量

+ Dart 也可以使用 var 关键字来声明一个可变的变量，此时编译器会根据变量初始值自动推断类型

+ Dart 可以显示指明类型来声明一个可变的变量。

+ Dart可以指明变量是否可以为空类型

+ Dart 中变量如果是非空类型，那么必须给一个默认值，否则无法编译通过。如果是可空类型，默认值都为 null

+ 延迟初始化，通过late关键字修饰，使用到的时候才会进行初始化

+ Dart 中还可以使用 Object 和 dynamic 关键字来声明一个变量

  ```DART
  var name = 'Bob'; // name 变量的类型被推断为 String
  String name = 'Bob'; //显示指定类型
  int? name; // 可空类型
  late String description = getOrder(); //延迟初始化
  Object str = "erdai666"; // Object声明变量
  dynamic str = "erdai666"; // dynamic声明变量
  ```

# 常量

常量：final以及const的变量不可被重新赋值。

+ const： 声明编译时的常量，必须给一个明确的编译常量值（即编译期间就确定的值），const 表示内容和引用都不可变

+ final： 声明运行时的常量，可以通过计算或者方法获取一个值（即运行期间确定的值）进行赋值，final 表示引用不可变，但内容是可变的。

```dart
final a;//编译通过
a = 10;//编译通过

const b;//编译报错
b = 10;//编译报错

//final 表示引用不可变，但内容是可变的。const 表示内容和引用都不可变
final set = {1,2,3};
set.add(4);

var list = const [1,2,3];
list.add(4);//运行报错，const list 不可新增元素

```

# 类型

## 1.数字类型

num：数字类型，int和double都是它的子类

+ int：整数类型
+ double：浮点数类型

## 2.字符串类型

```dart
//引号包裹
var s1 = 'Hello';
// 模版字符串
var s4 = "转大写：${s2.toUpperCase()}";
```

## 3.布尔类型

```DART
bool status = true;
```

在进行条件判断的时候，必须显式判断布尔类型的值，不能直接通过if转换和判断

```dart
if(desc != null) {
    print(desc);
}
```

## 4.枚举类型

枚举类型其实就是一组常量的集合，都是只读的,枚举常量都有一个从 0 开始数字编号，第一个常量是 0，第二个是 1，以此类推

```dart
// 使用 enum 关键字，定义 Color 枚举类型，Color 包含了3个常量 red、green、blue
enum Color { red, green, blue }

// 读取 Color.blue 枚举常量
var aColor = Color.blue;
```

## 5.List 数组

List 数组定义的元素有序可重复

```dart
//定义非固定长度的数组
List<int> a = []; //等价：var a = <int>[];
//定义固定长度的数组，超出就会报错
//定义一个固定长度为 3 ，类型为 int 的数组
var list = List.filled(3,0);
// 扩展操作符
var list1 = [0, ...list];
 
// 空感知扩展操作符
var arr = null;
var list2 = [0, ...?arr];
 
// 集合中的 if操作
var nav = ['Home', 'Furniture', 'Plants', if (1==1) 'Outlet'];
 
// 集合中的 for 操作
var listOfStrings = ['#0', for (var i in list1) '#$i'];
```

常用API

```dart
//1、首先定义一个 int 类型的可变数组
var a = <int>[];
//2、往数组尾巴添加元素 a:[1,2,3]
a.add(1);
//3、修改第一个元素的值 a:[0,2,3]
a[0] = 0;
//4、在数组 0 位置，插入 100 a:[100,0,2,3]
a.insert(0,100);
//5、删除一个元素 a:[100,0,2]
//根据元素删除
a.remove(3);
//根据下标删除 a:[100,2]
a.removeAt(1);
//6、获取数组大小
print(a.length); //打印：2
//7、数组排序：默认数组从小到大排序 a:[2,100]
a.sort();
//8、判断数组是否包含指定元素
a.contains(2); // true
//9、清空 List，删除所有数据 a:[]
a.clear();
```

## 6.Set 集合

Set 的元素都是唯一的

```dart
//1、方式一：使用 {} 定义一个 String 类型的 Set
var strSet = {"str"};
//2、方式二：定义一个空的 String 类型的 Set
var names = <String>{};
//3、方式三：通过 Set 对象定义一个可以保存 String 类型的 Set
var names = Set<String>();
```

常用API

```dart
//1、首先定义一个 set 集合
var names = <String>{};
//2、添加一个元素 names:{"Dart"}
names.add("Dart");
//3、添加一个 List 数组 names:{"Dart","Flutter"}
var titles = ["Flutter"];
names.addAll(titles);
//4、获取 Set 大小
print(names.length); //打印：2
//5、删除元素
//根据元素进行删除 names:{"Dart"}
names.remove("Flutter");
//6、判断 Set 是否包含指定元素
names.contains("Dart"); //true
//7、清空 Set 所有元素 names:{}
names.clear();
```

## 7.Map 集合

初始化

```dart
//1、方式一：直接以key, value 的方式初始化一个 map 类型变量， key 和 value 都是 String 类型
var map1 = {
  //格式  Key: Value
  'first': 'partridge',
  'second': 'turtledoves',
  'fifth': 'golden rings'
};
//2、方式二：直接通过 Map 类构造一个 map 类型变量, key 类型为 String, value 类型为 int
var map2 = Map<String, int>();
//3、方式三：定义一个 key 类型为 String, value 类型为 int 的空 Map
var map3 = <String,int>{}
//4、方式四：定义一个绕过编译检查的动态 key，value 类型 Map
var map4 = Map(); //等价于：var map4 = {};
```

常用 Api 

```dart
//1、首先定义一个 map
var map = {};
//2、向 map 添加数据 map:{"key1":"value1","key2":"value2"}
//使用 [] 操作符读写 map 数据，语法：map变量[key]
map["key1"] = "value1";
map["key2"] = "value2";
//3、查询 map：使用[]操作符，根据 key 查询对应的 value 值，如果 key 不存在则返回 null
var v1 = map["key1"];
var v2 = map["key2"];
//4、更新 map 数据：根据 key 更新 map 数据和添加 map 数据用法一样
map["key2"] = "erdai";
//5、获取 map 大小
print(map.length); //打印：2
//6、遍历 map
map.forEach((k, v) {
  print('$k $v');
});
//7、删除 map 数据：通过 remove 函数可以删除指定的 key 数据
//map:{"key2":"value2"}
map.remove("key1");
//8、清空所有 map 数据 map:{}
map.clear();
```

## 类型转换

+ 数字和字符串

  ```dart
  // 将 String 类型转换成 int 类型
  var one = int.parse('1');
  // 将 int 类型转换成 String 类型
  String oneAsString = 1.toString();
  ```



# 函数

函数定义 返回值类型  函数名（函数参数）{}

```dart
//1、Dart 写法1：方法的参数使用：var 参数名
String methodName(var name,var age){
  return "erdai666";
}

//2、Dart 写法2：方法的参数使用：类型 参数名
String methodName(String name,int age){
  return "erdai666";
}

//3、Dart 写法3：方法的返回类型可省略，根据方法体最后一行代码进行返回类型推断
methodName(var name,var age){
  return "erdai666";
}

//4、如果没有写返回类型，且方法体最后一行代码没有明确写返回语句，那么默认执行：return null
methodName(var name,var age){ 
}

//5、Dart 写法4：无返回值使用 void 关键字
void methodName(var name,var age){ 
}
//6、Dart 写法5：如果方法体只有一行表达式，可将其改成单行方法样式，方法名和方法体用 => 连接
String methodName(var name,var age) => "erdai666";
```

## 可选参数[]

使用 [] 表示可选的位置参数,可以不传

```dart
void optionFunction(var value1,[var value2 = 2,var value3 = 3]){
  print('$value1 $value2 $value3');
}
```

## 命名参数{}

+ 命名参数默认都为可选参数。如果是必要参数，则需要用 required 关键字，且使用 required 修饰的参数不能提供默认值
+  使用 {} 来指定命名参数

+ 命名参数必须以 key: value 的形式去指定

```dart
void optionFunction(var value1,{required var value2,var value3 = 3}){
  print('$value1 $value2 $value3');
} 
```

## 默认参数 =

```dart
//1、如果可选参数没有提供默认值，那默认值就为 null
void optionFunction(var value1,{var value2 = 2,var value3}){
  print('$value1 $value2 $value3');
}
```

## 匿名方法(又称闭包）

匿名方法顾名思义就是没有名字的方法，语法规则如下：

```dart
//1、方式一：
(var 参数名){
   方法体
}
//2、方式二：
(类型 参数名){
   方法体
}
//3、方式三：如果方法体只有一行代码可以将匿名方法用单行表示
(var 参数名) => 方法体
(类型 参数名) => 方法体
  
//4、方式四：省略 var 或类型
(参数名){
   方法体
}

(参数名) => 方法体
```

## 静态方法

+ 使用 static 关键字修饰的方法即为静态方法，因静态方法不属于类实例，所以也无法访问类成员
+ 静态方法可以使用类名直接调用

```dart
class Test{
  static String staticFunction1(){
    return "";
  }
  static void staticFunction2(){
 
  }
}
void main(){
  Test.staticFunction1();
  Test.staticFunction2();
}
```

# 运算符

+ 算术运算符： \+， -， *， / (除法有小数) ，~/（取整） %， ++

+ 级联调用:连续调用对象的属性和方法

  ```dart
  querySelector('#confirm') // 通过 querySelector 查询获得一个对象
    ..text = 'Confirm' // 设置对象 text 属性
    ..classes.add('important');  // 调用对象的 classes 属性的 add 函数
  
  //等价如下代码
  var button = querySelector('#confirm');
  button.text = 'Confirm';
  button.classes.add('important');
  ```

+ 赋值运算符:  =, *=,+=等

+ 关系运算符：==（是否相等），!=（是否不相等），\>，\>=

+ 类型测试运算符

  ```JS
  as	用于类型转换，将一个对象类型转换成另外一种对象类型，一般用于子类对象转换成父类对象。
  is	用于检测一个变量是否属于某种对象类型
  // 如果 emp 变量是 Person 类型则条件为 true
  if (emp is Person) {
    XXX
  }
  is!	用于检测一个变量不属于某种对象类型
  ```

+ 逻辑运算符： &&（逻辑与），||（逻辑或），!（表达式条件取反）

+ 位运算符：~expr（按位取反），<<

+ 条件运算符: condition ? expr1 : expr2,  expr1 ?? expr2(如果 expr1 不等于 null, 则执行 expr1 并返回 expr1 的值，否则执行并返回 expr2 的值)

# 流程控制

## if-else

```DART
if (isRaining()) {
  // 代码1
} else if (isSnowing()) {
  // 代码2
} else {
  // 代码3
}
```

## for-i 和 for-in 循环

```dart
// 定义 int 数组
var list = [5,1,2,6,3];

//1、for-i 循环遍历数组
for (var i = 0; i < list.length; i++) {
  // 打印数组元素
  print(list[i]); 
}
​
//2、for-in 循环遍历数组变量 list
for (var v in list) {
  // 打印数组元素 v
  print(v); 
}
```

## switch语句

```DART
testSwitch(String command) {
  switch (command) {
    case 'CLOSED':
      print(command);
      break;
    case 'PENDING':
      print(command);
      break;
    default:
      print(command);
  }
}
```

## while 和 do-while 循环语句

```dart
//1、while 循环语句例子
void main() {
  // 定义 int 数组
  var list = [5, 1, 2, 6, 3];
  // 循环遍历数组
  var i = 0;
  while (i < list.length) {
    // 条件为 true 则执行循环体代码
    print(list[i]); // 打印数组元素
    i++; // 数组下标递增
  }
}
//2、do-while 循环语句例子
void main() {
  // 定义 int 数组
  var list = [5, 1, 2, 6, 3];
  // 循环遍历数组
  var i = 0;
  do {
    print(list[i]);
    i++;
  } while (i < list.length); // 先执行循环体代码后再检测循环条件，条件为 true 则继续执行循环
}
```

# 异常处理

## 1.throw 关键字

```
 throw Exception('这是一个异常');
 throw '这是一个异常';
```

## 2.try/on catch

+ on 用来指定异常的类型，catch 则用来捕获对象
+ 当抛出的错误并不是 on 指定的异常类型时，则走最后面的 catch 兜底

```dart
void main() {
  try {
    var s;
    print(s.length);
  } on NoSuchMethodError catch (e) {
    //捕获异常并打印
    print(e);
  } catch (e, s) {
    //兜底处理 e：抛出的异常对象 s：栈信息，此参数可写可不写
    print(e);
    print(s);
  }
}
```

# 类

Dart 是面向对象编程语言，对象都是由类创建的，所有类都是由 Object 类派生出来的子类，除了 Object , 所有类只有一个父类（即只能继承一个父类）

尽管 Dart 语言中一个类只能继承一个父类，但是 Dart 语言提供了 mixin 机制，可以复用多个类，达到类似多继承的效果

## 类和对象

+ Dart 没有 public、protected 和 private 等成员访问限定符。默认情况下属性，方法，类等都是共有的。如果想要表示私有，则以下划线 _ 开头去命名
+ Dart 中实例化对象，new 关键字可写可不写
+ 当我们在类中创建私有属性时，我们应该给私有属性提供 getter 和 setter 方法供外界访问：

```dart
class Person {
  // 定义类成员属性，默认类的成员属性和方法都是共有的
  var name;
  // 以下划线 ( _ ) 开头命名的属性代表私有成员属性
  var _age;
  // 跟类名同名的方法，为构造方法
  // 这里自定义了一个携带参数的构造方法。
  // 如果我们没有自定义构造方法，会自动生成一个不带参数的默认构造方法
  Person(var name, var age) {
    // 因为参数名和类属性名同名，可以使用this引用当前对象
    this.name = name;
    // 可以忽略this关键字，直接引用类成员
    _age = age;
  }
  
  //为 _age 提供 getter 和 setter 方法
  int get age{
    return _age;
  }
  set age(int age){
    _age = age;
  }
  // 定一个 public 的方法
  String greet(String who) => 'Hello, $who. I am $name, my age is $_age !';
}
void main(){
  var person = Person("erdai",18);
  //下面这句就是调用了 age 的 set 方法
  person.age = 20;
  var greet = person.greet("lucy");
  print(greet);
}
//打印结果
Hello, lucy. I am erdai, my age is 20 !
```

## 构造方法

如果我们没有自定义一个构造方法，会自动生成一个不带参数的默认构造方法

```dart
// 这个类会生成默认的构造方法
class Person {
    String name;
}

// 通过默认构造方法实例化对象
var p = Person();
```

+ 自定义构造方法

```dart
class Point{
  var x,y;
  
  Point(var x,var y){
    // 通过this访问成员属性，当然一般除非出现命名冲突，否则可以忽略this
    this.x = x;
    this.y = y;
  }
}
======>简写
class Point{
  var x,y;
  // 直接将构造方法的第一个参数赋值给this.x, 第二个参数赋值给this.y
  Point(this.x,this.y);
}
======> 初始化参数列表
class Point{
  var x,y;
  // 冒号 : 后面的表达式就是参数初始化列表，每个表达式用逗号分隔
  Point(var x,var y): this.x = x,this.y = y{
    // 使用参数初始化列表初始化对象属性，这里如果没有别的初始化工作要做，可以是空的
  }
}
=======>多个初始化方法
  class Point{
  var x,y;
  Point(this.x,this.y);
  // 命名构造方法 namedConstructor
  // 这里使用参数初始化列表，直接通过 this 调用上面的构造方法，传入两个参数 0，初始化对象
  Point.namedConstructor():this(0,0);
}
==========>factory 构造方法
factory 构造方法只能访问静态属性和静态成员方法，因此不能访问 this 引用
  
//1、定义个日志类
class Logger {
  final String name;
  bool mute = false;
  // 定义一个私有的_cache属性，用来保存创建好的Logger对象
  static final Map<String, Logger> _cache = {};
  // 注意这个构造方法，前面使用了factory关键字修饰，这代表这个构造方法是一个工厂构造方法
  // 工厂构造方法不会每次都创建一个新的Logger对象
  factory Logger(String name) {
    // 根据name判断缓存的Logger对象是否存在
    if (_cache.containsKey(name)) {
      // 返回缓存的Logger对象
      return _cache[name]!;
    } else {
      // 如果没有缓存，则调用命名构造方法_internal创建一个Logger对象
      final logger = Logger._internal(name);
      // 根据name缓存logger
      _cache[name] = logger;
      // 返回新的Logger对象
      return logger;
    }
  }
  // 注意这个是一个私有的命名构造方法。
  Logger._internal(this.name);
  void log(String msg) {
    if (!mute) print(msg);
  }
}
//2、测试
void main(){
  var logger = Logger("erdai");
  logger.log(logger.name);
}
//打印结果
erdai
```

## 3.继承

+ Dart 通过 extend 关键字继承一个类
+ 子类会继承父类可见的属性和方法，不会继承构造方法
+ 子类能够复写父类的 getter，setter，以及普通方法，使用 @override 表示覆写

```dart
class Parent{
  String name = "";
  int age = 0;
  //计算属性
  bool get adult => this.age > 18;
  //私有属性，对于子类不可见
  String _address = "";
  void method(){
    print('Parent');
  }
}
class Children extends Parent{
  void specificMethod(){
    print('Children specificMethod');
  }
}
void main(){
  var child = Children();
  //调用子类自己的方法
  child.specificMethod();
  //访问父类的属性
  child.name = "erdai";
  child.age = 18;
  print('${child.name} ${child.age}');
  //调用父类的方法
  child.method();
  //访问父类的计算属性
  print('${child.adult}');
}
//打印结果
Children specificMethod
erdai 18
Parent
false
```

+ Mixin:使用 with 关键字并在其后跟上 Mixin 类的名字来使用 Mixin 模式：

  ```dart
  class Performer {
     
  }
  class Musician extends Performer with Musical {
   
  }
  
  mixin Musical {
   
  }
   
  ```

## 4.多态

+ 简单的理解：多态就是将子类的对象赋值给父类的引用，同一个方法调用会有不同的执行效果

+ 多态的体现：父类定义一个方法，让继承它的子类去实现，每个子类有不同的表现

```dart
class Animal{
  void animalType(){
  }
}
class Dog extends Animal{
  @override
  void animalType() {
    print('I am dog');
  }
}
class Pig extends Animal{
  @override
  void animalType() {
    print('I am pig');
  }
}
void main(){
  //子类的对象赋值给父类的引用
  Animal animal1 = Dog();
  Animal animal2 = Pig();
  //同一个方法调用会有不同的执行效果
  animal1.animalType();
  animal2.animalType();
}
```

## 5.抽象类和抽象方法

### 抽象类

+ 抽象类就是不能实例化的类，通过 abstract 关键字声明，其作用是用于定义接口
+ 继承抽象类，子类必须要实现所有抽象方法，否则会报错

```dart
// 使用 abstract 关键字修饰的类，就是抽象类
abstract class Doer{
  // 抽象类跟普通类一样，可以定义成员变量，成员方法。
  String name = "";
  // 定义个抽象方法，这个方法我们没有实现具体的功能
  void doSomething();
}

// 继承抽象类 Doer
class EffectiveDoer extends Doer{
  // 实现抽象类的抽象方法
  @override
  void doSomething() {
    print('doSomething');
  }
}

void main(){
  var doer = EffectiveDoer();
  doer.doSomething();
  doer.name = "erdai";
  print(doer.name);
}

//打印结果
doSomething
erdai
```

### 抽象方法

+ 抽象方法就是没有实现的方法，Dart 中的抽象方法不能用 abstract 声明，Dart 中没有方法体的方法就称为抽象方法

+ 如果类中有抽象方法 , 必须将该类标为抽象类 ;



## 6.接口

+ Dart 中的接口没有使用 interface 关键字定义，而是普通类和抽象类都可以作为接口被实现。但是一般都是用抽象类来定义接口

+ 子类通过 implements 来实现接口

+ 默认情况每一个类都**隐含**一个包含所有公有成员（属性和方法）的接口定义

```dart
abstract class Fruit{
  // 包含在隐式接口里面
  String name = "";
  
  // 构造方法不包含在隐式接口里面
  Fruit(this.name);
  // 包含在隐式接口里面
  void eat();
}

class Apple implements Fruit{
  @override
  String name = "苹果";
  @override
  void eat() {
    print('吃$name');
  }
}
void main(){
  var fruit = Apple();
  fruit.eat();
}
```

# 泛型

Dart 中泛型主要有以下四种使用：

+ 泛型类

```dart
//1、定义泛型类
class GenericClass<T>{
  T? name;
  GenericClass(this.name);
  void setName(T? value){
    name = value;
  }
  T? getName(){
    return name;
  }
}

//2、泛型类使用
void main() {
  var genericClass = GenericClass<String>("");
  genericClass.setName("erdai");
  print(genericClass.getName());
}
```

+ 泛型接口

```dart
//1、定义泛型接口
abstract class GenericInterface<K,V>{
  void setKeyValue(K key,V value);
}
//2、定义泛型接口实现类
class GenericInterfaceImpl<K,V> implements GenericInterface<K,V>{
  var map = {};
  @override
  void setKeyValue(K key, V value) {
    map[key] = value;
  }
}
//3、泛型接口使用
void main() {
  var impl = GenericInterfaceImpl<String,int>();
  impl.setKeyValue("erdai", 666);
  impl.map.forEach((key, value) {
    print('$key $value');
  });
}
```

+ 泛型方法

```dart
//Dart 中的写法
void genericMethod<T>(T param){
  
}
```

4、限制泛型类型: <泛型参数 extends 父类>

```dart
class BaseClass{
  void baseMethod(){
    print('BaseClass baseMethod...');
  }
}
class Child extends BaseClass{
  @override
  void baseMethod() {
    print('Child baseMethod');
  }
}
//T 类型必须是 BaseClass 或者其子类
class Foo<T extends BaseClass>{
  T? t;
  Foo(this.t);
  void fooTest(){
    t?.baseMethod();
  }
}
void main(){
  //1、使用父类 BaseClass 作为泛型参数是允许的
  var baseClass = BaseClass();
  var foo1 = Foo<BaseClass>(baseClass);
  foo1.fooTest();
  
  //2、使用子类 Child 作为泛型参数
  var childClass = Child();
  var foo2 = Foo<Child>(childClass);
  foo2.fooTest();
  //3、如果不传入任何泛型参数，默认使用父类 BaseClass 作为泛型参数
  var foo3 = Foo(baseClass);
  foo3.fooTest();
}
//打印结果
BaseClass baseMethod...
Child baseMethod
BaseClass baseMethod...
```

# 库和可见性

import 和 library 关键字可以帮助你创建一个模块化和可共享的代码库。代码库不仅只是提供 API 而且还起到了封装的作用：以下划线（_）开头的成员仅在代码库中可见。 每个 Dart 程序都是一个库，即便没有使用关键字 library 指定。

+ 使用库: 使用 import 来指定命名空间以便其它库可以访问。

```dart
// 导入内置包使用 dart: 作为路径前缀
import 'dart:math';;
import 'package:test/test.dart'; // 导入第三方开源包
import 'libs/stack.dart'; // 导入本地模块

// 包的别名
import 'package:lib2/lib2.dart' as lib2;
lib2.Element element2 = lib2.Element(); // Uses Element from lib2.

====> 导入部分库
// Import only foo.
import 'package:lib1/lib1.dart' show foo;
// Import all names EXCEPT foo.
import 'package:lib2/lib2.dart' hide foo;

====》延迟加载库
使用 deferred as 关键字来标识需要延时加载的代码库：
import 'package:greetings/hello.dart' deferred as hello;
```

# 异步处理

## 异步过程

+ Future：代表的是一个异步的计算任务，如果任务还没执行完成，我们是拿不到异步任务的结果

```dart
import 'package:http/http.dart' as http;
void main() {
  var url = "https://www.baidu.com/";
  //调用 get 函数请求 url, 返回一个封装了 http 请求任务的 future 对象
  Future fTask = http.get(Uri.parse(url));
  //打印 future 对象
  print(fTask);
  
  // 向 future 对象注册回调函数，处理请求结果
  fTask.then((response) => {
    print('Response status: ${response.statusCode}')
  });
  // 打印 main 函数结束标记
  print('main end...');
}

======> async和await
void main() async{
  var url = "https://www.baidu.com/";
  //请求 url, 通过 await，等待 future 异步计算任务的结果，执行成功就直接返回结果
  var response = await http.get(Uri.parse(url));
  print('Response status: ${response.statusCode}');
  print('main end...');
}
```

+ Stream:代表一个异步的数据序列，是一种异步读取流式数据的方式，使用格式如下：

```dart
Future<int> sumStream(Stream<int> stream) async {
  var sum = 0;
  await for (final value in stream) {
    sum += value;
  }
  return sum;
}

//async* 表示这是一个需返回 Stream 类型参数的异步函数
Stream<int> countStream(int to) async* {
  for (int i = 1; i <= to; i++) {
    //yield 在这里表示暂时让出资源让其他代码执行
    yield i;
  }
}
void main() async{
  var stream = countStream(10);
  //通过 await 等待 sumStream 计算返回结果
  var sum = await sumStream(stream);
  print(sum);
}

```



学习资料

https://juejin.cn/post/7130647339294785549#heading-13