[toc]

# 基本信息

| 语言 | 作用                                                         | 语言                                                         |
| ---- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Js   | Web 开发的核心语言，可用于服务器端（如 Node.js）。移动应用（如 React Native）和桌面应用（如 Electron）。 | **面向对象的：**基于原型（prototype） **弱类型**：变量的类型在运行时确定 **类定义**：class **单继承** **单线程** **编译方式**：JIT 即时编译 |
| Dart | 在Flutter 框架中作为主要语言。也可用于服务器端开发。         | **面向对象的**：基于类（class）和对象（object） **强类型**：变量的类型在编译时确定 **类定义**：class **单继承** **单线程** **编译方式**：同时支持AOT和JIT |

# 基本介绍

+ 入口

  main是入口函数，所有代码通过main才能调用

```dart
//dart执行函数体
void main() {
  print('');
}
```

+ 代码注释: 和js一样

```
1、//
2、///
3、/* */
```

+ 打印输出

```
print('');
```

+ 类型转化： dart不做类型转换，定义变量时，需要确认数据类型
+ 空安全： **`?`** 用于标记一个类型是可空的，即该类型的变量可以是该类型的实例或是 `null`。声明一个变量为 `int?`，则它可以是 `int` 类型，也可以是 `null`，声明一个变量为 `int`，则它不能是 `null`。

# 变量声明

|      | Js                                            | Dart                                                         |
| ---- | --------------------------------------------- | ------------------------------------------------------------ |
| 变量 | var：全局作用域, let：块级作用域，可重新赋值  | **不明确类型**：var：根据右侧的值推断变量的类型，并且该变量在之后的代码中类型不会改变   dynamic：在运行时决定其具体类型的变量。后期可以改变 |
|      |                                               | **明确类型**：int a = 5; // 整数类型double b = 5.5; // 浮点数类型 |
| 常量 | const：块级作用域，运行时常量，赋值后不能改变 | final：声明一个不可变的变量（常量），运行时确认，该值只能被赋值一次 |
|      |                                               | const：编译时常量，必须在编译时就能确定其值，运行时不能变    |

**使用 `const` 的情况**：当 Widget 的内容、构造函数参数在编译时已知且不变时，使用 `const` 可以优化性能并减少不必要的重建。 **不使用 `const` 的情况**：当 Widget 的内容或参数是动态的、在运行时决定时，或者涉及到可变数据时，不适合使用 `const`。

通过late关键字修饰，使用到的时候才会进行初始化

```DART
var name = 'Bob'; // name 变量的类型被推断为 String
String name = 'Bob'; //显示指定类型
int? name; // 可空类型
late String description = getOrder(); //延迟初始化
Object str = "erdai666"; // Object声明变量
dynamic str = "erdai666"; // dynamic声明变量
```



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

# 数据类型

|                  | Js                                                           | Dart                                                         |
| ---------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| number           | **number**: 包含整数和浮点数，没有区分这两种类型。 **bigInt**: 用于表示任意精度的整数。 | **num：**数字类型的共同特征int**: 整数类型，支持任意精度。 **double**: 浮点数类型。 |
| string           | 字符串                                                       | 字符串                                                       |
| boolean          | 布尔值，`true` 或 `false`。                                  | 布尔值，`true` 或 `false`。                                  |
| symbol           | 唯一且不可变的值，主要用于对象属性的唯一标识。               | 用于表示符号（主要用于标识符）                               |
| undefined        | 未定义，表示变量未赋值。                                     |                                                              |
| null             | 表示空值或无值。int``? nullableInt; ``// Null，可能是null    | 表示空值，Dart 的 `Null` 类型是一个单一的值 `null`。         |
| 普通对象         |                                                              |                                                              |
| 数组Array        |                                                              | List类似于 JavaScript 的数组。                               |
| 日期Date         | 日期                                                         |                                                              |
| Function         | 函数类型                                                     | 函数类型                                                     |
| RegExp正则表达式 | 正则                                                         |                                                              |
| Map              | 键值对集合。                                                 | 键值对集合，类似于 JavaScript 的对象字面量。 Map<String, String> map = {  ``'key'``: value'} |
| Set              | 集合，包含唯一值的集合。                                     | 集合，包含唯一值的集合。                                     |
| enum             | 无                                                           | 枚举类型enum` `PlanetType { terrestrial, gas, ice }          |
| runes            | 无                                                           | 用于表示 UTF-16 编码的字符序列。var runes = Runes(``'Hello World'``); print(String.fromCharCodes(runes)); ``// 输出: Hello World |

## 1.数字类型

num：数字类型，int和double都是它的子类

+ int：整数类型
+ double：浮点数类型

```dart
int a = 42; // int
num a = 42；// int
 
double pi = 3.14; // double
```

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



```
常用属性：
   length       长度
   isEmpty      是否为空
   isNotEmpty   是否不为空
   reversed     倒叙排序，不再是数组，toList转成数组
 
 常用方法：
   add：增加数据
   addAll：拼接数组
   indexOf：查找某一项，找到返回索引值、找不到返回-1
   remove：删除某个值，返回删除后List
   removeAt：删除某个下标值，返回删除后List
   fillRange（1，3，‘aaa’）1到3之前修改成aaa，并返回修改后数组
   insert（1，’aaa’）指定位置插入，在下标1插入aaa
   insertAll（1， [‘ds’,’ddsa]）在下标1插入LIst
   toList() 其他类型转换成List
   join() List转换成字符串
   split() 字符串转换成LIst
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

```
// Set常用方法
   new Set() 去除重复集合数据
   new Set().toList() 在转换成集合
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

```
// Map：无序键值对
   var person={}
   var person=new Map()
 
 常用属性
   person.keys:获取key
   person.values:获取value
   isEmpty
   isNotEmpty
 
 常用方法
   addAll：增加键值对
   remove：删除键值对
   containsValue：查找映射某个值 返回true/false
```



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

## List、Set、Map常用方法

```dart
// List、Set、Map常用方法
 
 forEach：遍历LIst中的value
   List.forEach((value){
     // 只能传入一个值
   })
 
 map：修改集合里面的数据
   var newList = List.map((value， key){
     return value*2
   })
   newList.toList()
 
 
 where：循环遍历，满足条件的返回一个新数组
   var newList = List.where((value){
     return value > 5
   })
 
 any：是否有一个值满足条件的数据，返回true/false
 every：每一个都满足条件，返回true/false
 reduce 和 fold: 将集合中的元素组合成单个值。
 expand: 将集合中的每个元素展开为一个新的集合
 for 循环 和 for-in 循环: 用于遍历集合
```

# 类型转换

## 类型判断

```DART
// 类型判断：is关键词
var str = 'string';
print(str is String);
print(str is num);
print(str is int);
```

## 特殊运算符

```DART
// 赋值运算符
 ??= (判断是否为空，为空时才赋值)
 a??=10（判断a是否为空，为空时才赋值10，不为空保持不变）
 
 // ??条件运算符：
 b = a ?? 10 （a为空时10赋值给b，不为空时b = a）
 
 
// 条件属性访问：
 a?.b 判断属性是否存在，存在访问
 
 
// 级联运算符,连续调用对象的方法
class Person {aa() bb() cc()}
void main() {
  var person = Person()
    ..aa()  // 调用 aa() 方法
    ..bb()  // 调用 bb() 方法
    ..cc();  // 再次调用 cc() 方法
}
 
 
// 条件级联运算符，避免空指针异常
void main（）{
    person
    ?..aa();  // person 为 null 时不会调用aa() 方法
}
```

## 类型转换

```DART
// 字符转数字
String str1 = '123';
var myNum = int.parse(str1);
var myNum1 = double.parse(str1);
 
//数字转字符
var myNum2 = 25;
var str2 = myNum2.toString();
 
// 数字转 bool
int a = 10;
int b = 0;
bool boolA = a != 0; // true
bool boolB = b != 0; // false
 
 
// String、List、Set、Map 转bool
String nonEmpty = 'Hello';
String empty = '';
bool boolNonEmpty = nonEmpty.isNotEmpty; // true
bool boolEmpty = empty.isEmpty; // true
```



# 函数

函数定义 返回值类型  函数名（函数参数）{}

```dart
// dart函数声明，int代表返回值类型
int add(int a, int b) { return a + b; }
 
// 不定义返回值类型（不建议）
getValue（a） { return a; }
 
// 没有返回值的函数-void
void message(String b) {print(b);}
 
// 箭头函数，函数表达式
var divide = (int a, int b) => a + b;
 
// 匿名函数
var greet = (String name) { return 'name';};
 
// 必需参数
void greet(String name, int age) {
  print('$name $age');
}
greet('Alice', 30)
 
// 可选参数
void greet(String name, [int? age]) {
  if (age != null) {
    print('Hello, $name. You are $age years old.');
  }
}
// 命名参数 + 默认值参数
void greet(String name, {int? age, String? city = 'beijing'}) {
  print('$name. Age: ${age ?? 'unknown'}, City: ${city ?? 'unknown'}.');
}
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

## 异步操作

```DART
// Dart异步操作
Future fetchData() async {
  var data = await http.get('xxxxxx');
  return data
}
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

和js完全一样

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

# 泛型

泛型（Generics）是一种编程特性，允许你在定义类、接口、函数或方法时不指定具体的数据类型，而是在实际使用时再指定。泛型提供了类型安全和代码重用的能力，可以使代码更加灵活和强大。一般用<T>表示

```DART
T echo<T>(T value) {
  return value;
}
 
void main() {
  print(echo<int>(123)); // 输出: 123
  print(echo<String>('Hello')); // 输出: Hello
}
 
这里的 echo 方法是一个泛型方法，它的参数和返回值都是 T 类型。调用时可以指定 T 的具体类型。
```



# 类class

Dart 是面向对象编程语言，对象都是由类创建的，所有类都是由 Object 类派生出来的子类，除了 Object , 所有类只有一个父类（即只能继承一个父类）

尽管 Dart 语言中一个类只能继承一个父类，但是 Dart 语言提供了 mixin 机制，可以复用多个类，达到类似多继承的效果

类分为具体类和抽象类

## 1.具体类

+ 类的定义：使用 **class** 关键字
+ 构造函数：创建类的实例，并初始化其属性。new 关键字可写可不写
+ 属性和字段：属性是类中的数据成员，属性可以是公有的或私有的
+ 方法：方法是类中的函数，方法可以是公有的（可以被类外部调用）或私有的（以 _ 开头，类外部无法直接调用）， 受保护的（外部不能调用，只能在子类中访问）
+ 多态性：允许对象以父类的类型存在，但实际运行时可能是子类的实例
+ 访问器：“getter”（获取器）和“setter”（设置器）是定义访问器的关键字，属于计算属性，来控制对类属性的访问和修改

```dart
class Person{
 String name; // 变量：可以在类的外部直接访问和修改。
 int age;
 int _age; // 私有变量：以 _ 开头，只能在类内部访问。用于封装和保护内部状态。
 Person(this.name,this.age); // 默认构造函数的简写，初始化类的实例， 接受参数并将这些值赋给相应的属性
 Person.now(){} // 命名构造函数，处理不同的初始化情况
 // get、set修饰符定义访问器，计算属性 对私有字段的访问
 int get age1 => _age;
 set nowAge(value) {
 this._age = value;
 }
  
 // 公有方法，类外部可以调用
 void getInfo(){}
 // 私有方法，只能在类内部使用，用于实现内部逻辑
 void _getInfo(){}
 
 // @public：公有 、 @protected保护、 @private：私有
 void protectedMethod() {
    print('Protected method');
  }
 // 静态成员-属性 or 方法, 直接通过类去调用
 static const double pi = 3.14159;
}
 
// 使用默认构造函数实例化，允许对象以父类的类型存在，但实际运行时可能是子类的实例
Person A = Person('Alice', 20,);
A.getInfo()
// 使用命名构造函数实例化
Person B = Person.now();
// 访问静态属性,属于类本身而不是类的实例
Person.pi
```

## 抽象类

+ 抽象类：abstract 关键字来定义
+ 抽象方法： Dart中没有方法体的方法称为抽象方法
+ 如果子类继承抽象类必须得实现所有的抽象方法， 如果把抽象类当做接口实现的话必须得实现所有的抽象方法。
+ 抽象类不能被实例化，只能先子类继承后再实例化
+ `get` 和 `set` 可以定义抽象访问器，强制子类实现这些未实现的访问器。已实现直接使用或者重写

```dart
// 定义一个抽象类 Animal
abstract class Animal {
  String name;
  int age;  // 普通属性
   
  Animal(this.name, this.age); // 抽象类中的构造函数
 
  void makeSound();  // 抽象方法（子类必须实现）
   
  double get sleep; // 定义抽象访问器，强制子类实现这些访问器，如果已经实现可以直接使用
 
  // 普通方法（可以在子类中重写）
  void describe() {
    print('This is an animal named $name and it is $age years old.');
  }
 
}
```

## 3.具体类和抽象类区别

区别

- 1.实例化:
  - 具体类：可以被实例化（即可以创建对象）
  - 抽象类：不能被实例化。抽象类仅作为其他类的基础。
- 2.抽象方法:
  - 具体类不能包含抽象方法。
  - 抽象类可以包含抽象方法。继承抽象类的子类必须实现这些抽象方法。
- 3.目的:
  - 具体类则用于创建实际的对象，包含了具体的实现细节。
  - 抽象类用于定义一个通用的接口或模板，供其他类继承并实现。适合用于定义通用的行为和规范。
- 4.使用场景
  - 具体类: 用于实现实际的业务逻辑和功能。Flutter许多 UI 组件都是具体类。例如，Container、Text、Button 等都是可以直接实例化并使用的具体类
  - 抽象类: StatefulWidge/StatelessWidget 是两个抽象类，定义了有状态/无状态组件的通用行为，而具体的组件内容需要子类继承实现, 比如StatelessWidget类的build是抽象方法，继承了StatelessWidget组件的所有组件都必须实现build方法，它是widget类型

## 4.继承

继承是面向对象编程（OOP）的一个核心特性，它允许一个类（子类）继承另一个类（父类）的属性和方法

+ 单继承：Dart 支持单继承，即一个类只能继承自一个直接父类。
+ 继承成员：子类继承父类的实例变量和方法，但不能继承父类的构造函数。子类可以重写父类的方法，以改变或扩展其行为。
+ super 关键字：子类可以使用 super 关键字调用父类的构造函数或方法。super 允许子类在重写方法时访问父类的实现。
+ 重写方法：使用 @override 注解可以明确表明该方法是重写父类的方法，来提供自己的实现。
+ extends 关键字：子类使用 **extends** 关键字来继承父类。

### **继承具体类**

```dart
class Student extends Person {
  String studentId; // 新增的属性
  // 构造函数, super是指把参数传递到父类，调用父类的构造方法进行初始化
  Student(String name, int age, this.studentId) : super(name, age);
 
  // 重写父类的方法
  @override
  void greet() {
    print('Hello, I am $name, a student with ID $studentId, and I am $age years old.');
  }
 
  // 新增的方法
  void study() {
    print('$name is studying.');
  }
}
// 实例化
void main() {
  // 实例化 Student 类
  var student = Student('Alice', 20, 'S12345');
  // 调用重写的方法
  student.greet(); // 输出: Hello。。。
  // 调用子类特有的方法
  student.study(); // 输出: Alice is studying.
  // 由于 _getInfo 在 Person 类中是私有的，不能从 Student 实例中直接访问
}
```

### 继承抽象类

```dart
// 具体类 Dog 继承抽象类 Animal
class Dog extends Animal {
  Dog(String name, int age) : super(name, age);
 
  @override
  void makeSound() {
    print('Woof! Woof!');
  }
  @override
  double get sleep => print('')
}
 
// 具体类 Cat 继承抽象类 Animal
class Cat extends Animal {
  Cat(String name, int age) : super(name, age);
 
  @override
  void makeSound() {
    print('Meow! Meow!');
  }
  @override
   double get sleep => print('')
  }
}
 
// 实例化
void main() {
  // myDog使用父类的类型Animal
  Animal myDog = Dog('Buddy', 3);
  Animal myCat = Cat('Whiskers', 2);
 
  myDog.describe(); // 输出: This is 。。。
  myCat.describe(); // 输出: This is 。。。
 
  myDog.makeSound(); // 输出: Woof! Woof!
  myCat.makeSound(); // 输出: Meow! Meow!
}
```

### 区别

共同点：

- 1.无论是具体类继承还是抽象类继承，子类都继承父类的属性和方法，并可以重写父类的方法。
- 2.实现多态性：两者都可以通过多态性实现不同子类的行为统一处理。

区别

- 1.具体类：子类可以重写方法或使用父类提供的实现。
- 2.抽象类：子类必须实现抽象类中的抽象方法（如果有），但也可以使用抽象类中提供的具体方法实现。

使用场景

- 1、代码重用：继承可以避免重复代码，将通用功能放在父类中，子类可以直接继承这些功能。
- 2、构建层次结构：建立类之间的层次结构，使代码更具组织性和可维护性。例如，所有动物类可以继承自一个基类 Animal。

## 4.接口

+ Dart 中的接口没有使用 interface 关键字定义，而是普通类和抽象类都可以作为接口被实现。但是一般都是用抽象类来定义接口
+ 通过 implements 来实现接口
+ 多重继承：Dart 不支持多重继承，但允许一个类实现多个接口，结合不同的行为。class C implements A,B{}

### 具体类作为接口

将一个具体类作为接口，那么这个接口中的所有方法都需要在实现类中**重新实现**。

这是因为具体类作为接口时，接口中的方法没有实现，所有方法都被视为抽象的

```dart
class Vehicle {
  void start() {
    print('Starting vehicle');
  }
 
  void stop() {
    print('Stopping vehicle');
  }
}
 
// 实现接口的类
class Car implements Vehicle {
  @override
  void start() {
    print('Starting car');
  }
 
  @override
  void stop() {
    print('Stopping car');
  }
}
 
void main() {
  Vehicle myCar = Car();
  myCar.start(); // 输出: Starting car
  myCar.stop();  // 输出: Stopping car
}
```

### 抽象类作为接口

实现一个抽象类作为接口，你必须实现**所有**抽象方法。普通方法不需要。

```dart
abstract class Animal {
  void makeSound(); // 抽象方法
  void move();      // 另一个抽象方法
  void run(){ print()} // 普通方法
}
 
 
// 实现接口的类
class Dog implements Animal {
  @override
  void makeSound() {
    print('Bark');
  }
 
  @override
  void move() {
    print('Run');
  }
}
 
void main() {
  Animal dog = Dog();
  dog.makeSound(); // 输出: Bark
  dog.move();      // 输出: Run
}
```

## 5.mixins

使用**mixin with** 关键字

+ 代码重用：mixin 允许在多个类之间共享代码，避免代码重复。
+ 多重功能：一个类可以使用多个 mixin 来组合多种功能。
+ 解耦合：将功能模块化，使代码更加可维护和清晰。

```dart
// 定义多个 mixin
mixin Logger {
  void log(String message) {
    print('Log: $message');
  }
}
 
mixin Authenticator {
  void authenticate() {
    print('Authenticated');
  }
}
 
// 使用多个 mixin 的类
class User with Logger, Authenticator {
  final String name;
 
  User(this.name);
 
  void greet() {
    log('Hello, my name is $name');
    authenticate();
  }
}
 
void main() {
  var user = User('Alice');
  user.greet(); // 输出: Log: Hello, my name is Alice
               //          Authenticated
}
```

### **接口和mixin对比**

共同点

- 1.功能复用：都是用于代码复用和组织的机制。接口通过强制实现方法来实现标准化，mixin 通过将功能块插入类中来实现功能复用。
- 2.可组合性：接口和 mixin 都支持一定程度的可组合性。你可以在一个类中组合多个接口或 mixin，从而创建复杂的功能。
- 3.没有构造函数：接口（作为类）和 mixin 都不应该包含构造函数。接口定义方法签名，mixins 只定义功能。

区别

- 接口: 用于定义契约。接口的主要目的是提供一种标准化的方式来确保类实现了某些特定的方法。
- Mixin: 用于在类中复用代码。Mixin 允许将功能块添加到类中，避免了复杂的继承层次结构。
- 接口: 一个类可以实现多个接口，并且必须实现所有接口中的方法。
- Mixin: 一个类可以使用多个 mixin，而 mixin 不要求类必须实现特定的方法或属性。
- 接口: 类可以实现多个接口，但接口之间没有继承关系。
- Mixin: 类可以使用多个 mixin，实现代码的复用。Mixin 允许在类之间共享功能。

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

import 和 library 关键字可以帮助创建一个模块化和可共享的代码库。代码库不仅只是提供 API 而且还起到了封装的作用：以下划线（_）开头的成员仅在代码库中可见。 每个 Dart 程序都是一个库，即便没有使用关键字 library 指定。

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

## 如何管理package

+ 引入新的依赖package

  ```js
  dart pub add vector_math
  
  // 在pubspec.yaml中
  dependencies:
    vector_math: ^2.1.3
  ```

+ 更新package

  ```js
  // pubspec.lock是安装包的文档版本信息， 可以先修改版本再upgrade
  dart pub upgrade
  // 也可以直接get
  $ dart pub get
   vector_math 2.1.3
  ```

  

# 异步处理

## Future

+ Future 类似于 JavaScript 中的 Promise ，代表在将来某个时刻会返回一个结果。
+ 适合处理单一异步操作的结果或错误。
+ 用于需要等待单个异步结果的场景。
+ 支持链式调用和 `await` 关键字，使得处理异步结果和错误变得直观。
+ 例如，发起网络请求、读取文件或计算结果

```dart
Future<String> fetchData() async {
  // 模拟网络请求
  await Future.delayed(Duration(seconds: 2));
  return 'Data fetched';
}
 
void main() async {
  try {
    String data = await fetchData();
    print(data); // 输出: Data fetched
  } catch (e) {
    print('Error: $e');
  }
}
 
async：
返回一个 Future。适用于异步操作，函数在执行完成后将结果返回给调用者。
只能返回一个最终的结果或者错误。函数体内的 await 用于等待异步操作的完成。
```

## Stream

Stream 类可以用来获取一系列的值，比如，一系列事件。

- 适合处理一系列异步事件或数据流。
- 用于需要处理多个事件、动态数据更新或实时数据流的场景。
- 支持注册监听器，并使用 `await for` 循环处理数据流中的每个事件。
- 例如，用户输入、实时更新数据、文件的逐步读取等

```dart
import 'dart:async';
 
Stream<int> countStream() async* {
  for (int i = 1; i <= 5; i++) {
    await Future.delayed(Duration(seconds: 1));
    yield i; // 产生一个新事件
  }
}
 
void main() async {
  Stream<int> stream = countStream();
   
  // 监听流中的事件
  await for (int value in stream) {
    print(value); // 每秒输出 1, 2, 3, 4, 5
  }
}
 
async*:
返回一个 Stream。适用于异步生成器，允许逐步生成数据项，并且可以在多个时间点上发出事件。
使用 yield 关键字可以逐步产生多个数据项，并将它们发送到 Stream
```



学习资料

https://juejin.cn/post/7130647339294785549#heading-13





Arrsmn19951102.