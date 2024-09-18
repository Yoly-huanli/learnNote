[toc]



# 跨端技术分析

## 原生绘制

直接调用系统提供的SDK API开发的应用程序，是某一平台（Android、IOS）特有的应用

优点：

+ 可访问平台全部功能（GPS、摄像头）；
+ 速度快、性能高、可以实现复杂动画及绘制，整体用户体验好；

缺点：

+ 平台特定，开发成本高， 比如IOS和Android要维护两套代码，人力成本随之变大；
+ 内容固定，动态化弱，大多数情况下，有新功能更新时只能发版；

## 跨平台开发

一套代码，各平台通用

### web绘制

也叫 HTMLybrid App

纯前端开发，原生应用作为壳子工程提供内置浏览器组件，核心业务通过h5实现，通过webView渲染；

调用原生的能力需要有协议的规定， 也就是 JsBridge， 才能使用到原生的能力

### 原生绘制

类前端基础技术栈开发，绘制和渲染是原生绘制，比如React Native、Weex，主要原理是利用虚拟DOM映射为原生控件

优点

1. 采用 Web 开发技术栈，社区庞大、上手快、开发成本相对较低。
2. 原生渲染，性能相比 H5 提高很多。
3. 动态化较好，支持热更新。

不足：

1. 渲染时需要 JavaScript 和原生之间通信，在有些场景如拖动可能会因为通信频繁导致卡顿。
2. JavaScript 为脚本语言，执行时需要解释执行 （这种执行方式通常称为 JIT，即 Just In Time，指在执行时实时生成机器码），执行效率和编译类语言（编译类语言的执行方式为 AOT ，即 Ahead Of Time，指在代码执行前已经将源码进行了预处理，这种预处理通常情况下是将源码编译为机器码或某种中间码）仍有差距。
3. 由于渲染依赖原生控件，不同平台的控件需要单独维护

### 自绘引擎

自带渲染引擎，原生只提供了画布，在高性能的前提下能多端保持高度一致的呈现效果，flutter

优点如下：

1. 性能高；由于自绘引擎是直接调用系统API来绘制UI，所以性能和原生控件接近。
2. 灵活、组件库易维护、UI外观保真度和一致性高；由于UI渲染不依赖原生控件，也就不需要根据不同平台的控件单独维护一套组件库，所以代码容易维护。

# Flutter

为什么使用flutter

+ 原生架构效率提升有限
+ 人力紧张
+ 多端，多技术栈的效率问题
+ 需要保障产品的良好性能，交互视觉
+ 需要覆盖多业务形态：Android， IOS， 桌面端
+ 需要成熟度高、快速上手

缺点：

动态缺失

## 架构

![image-20221122200738251](../img/image-20221122200738251.png)

+ Framework: 用dart编程， 基础组件库
+ Engine: 绘制
+ Embedder: 兼容不同平台

## 资料

《Flutter实战·第二版》(https://book.flutterchina.club/)https://book.flutterchina.club/chapter2/first_flutter_app.html

https://flutter.cn/docs/development/ui/layout

## 安装

+ Android studio  注意java的path_room, 注意zsh的配置
+ xcode
+ flutter：Flutter Widget Snippets 插件
+ [cocoapods](https://cocoapods.org/)(pod) <= 12.x
+ vscode
  + 安装flutter插件：vscode前三个
  + 查看-命令- **File>New Flutter Project** 新建flutter
  + 查看-命令- choose flutter select device
  + flutter run
  + 热更新：在命令行输入r
  + 启动，右上角小三角-run without debugger(或者查看-命令-run without debugge)

## widget简介

```JS
// 导入组件库
import 'package:flutter/material.dart';
void main() {
  runApp(
    const Center(
      child: Text(
        'Hello, world!',
        textDirection: TextDirection.ltr,
      ),
    ),
  );
}
```

+ 根组件要放在runApp里作为入口
+ 框架会强制让根 widget 铺满整个屏幕

## 相关快捷键

- 创建widget：
  - 创建StatelessWidget：stless
  - 创建StatefulWidget：stful
- Alt + Enter(在mac vscode里 Alt + Enter 是 command + .)：
  - 给widget添加padding：按下Alt + Enter，单击Add Padding就可以给widget的外面加上一层Padding
  - 设置widget居中：按下Alt + Enter，单击Center Widget就可以给widget的外面加上一层Center的小部件，使widget居中
  - 将widget放到Container中：按下Alt + Enter，单击Wrap with Container就可以给widget的外面加上一层Container
  - 将多个widget放到Row,Column中：按下Alt + Enter，单击Wrap with Column或者Row就可以一次性将多个widget放置到Row或者Column中
  - 将widget放到任意的小部件中：按下Alt + Enter，单击Wrap with a new widget就可以将widget放到任意的小部件中
  - 移除widget：按下Alt + Enter，单击Remove Widget就可以将当前的widget进行删除
- 快速选择整个小部件：Ctrl + W（Windows）或 Option + Up（Mac）
- 格式化代码：Ctrl+Alt+L
- Flutter Outline：查看部件排列和嵌套

#  基础开发

## 组件引入

```d
import 'package:soda_bd_flutter_i18n/ui/page/home/page/workbench_page/widgets/commercial_offer/test.dart';

child: Test(),
```

新组件

```DART
import 'dart:ui';
import 'package:flutter/material.dart';

class StrategyDetail extends StatelessWidget {
  const StrategyDetail({ Key? key }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return const Placeholder();
  }
}
```

## 样式

### 边距

```dart
margin:const EdgeInsets.all(16),
padding: const  EdgeInsets.all(16),
// 或者直接由padding的widget
Padding()
```

### 颜色

```dart
color:Colors.red,
color: Color(0x1400000000),
```

### 边框

```dart
decoration: BoxDecoration(
        color: Colors.white,
        borderRadius: BorderRadius.circular(20.0),
        border:Border(
          bottom: BorderSide(
            color: Colors.red,
            width:1.0,
            style: BorderStyle.solid
          )
        ),
        boxShadow: const [
          BoxShadow(
            color: Colors.grey,
            blurRadius: 5,
            spreadRadius: 0.5,
            offset: Offset(1,5),
            blurStyle: BlurStyle.outer
          )
        ]
      ),
```

### 文字

```dart
Text(
		'营销活动营销活动营销活动营销活动营销活动营销活动营销活动营销活动营销活动营销活动营销活动营销活动营销活动营销活动',
		style: TextStyle(
					fontSize: 16,
					fontWeight: FontWeight.w700,
					fontFamily: 'DiDiSans-Pro-Bold',
		),
  // 文字省略号
		maxLines: 2,
		overflow: TextOverflow.ellipsis,
),
```



### flex布局

```dart
Flex(direction: Axis.horizontal, children:[
                  Flexible(flex:1, child: Container(
                    child: Text('000')
                  )),
                   Text('222iiwwwi')
                ]),
```

Row

```
Row(
  children: [Expanded(child: Text('测试')), Text('测试222')]
),
// 文字对齐
Row(
  crossAxisAlignment: CrossAxisAlignment.baseline,
  textBaseline: TextBaseline.alphabetic,
  children: [Expanded(child: Text('测试')), Text('测试222')]
```

### 其他

#### 按钮设置

```dart
ElevatedButton(
	onPressed: null,
	child: const Text('查看', style: TextStyle(color:Colors.red)),
	style: ButtonStyle(
			shape: MaterialStateProperty.all<RoundedRectangleBorder>(
						RoundedRectangleBorder(
								borderRadius: BorderRadius.circular(10), // 修改圆角半径为10
						),
			),
	)
)
```

#### 查看调试点击

```
onTap: () {
// 展示达标标准popup
_showBottomSheet(context, '23424');
    debugPrint('我被点击了');
},
```



# 资源管理

## 图片

+ 图片标签`Image` 组件

  - 网络图片：Image.network

  - 本地图片：Image.asset

  - 通过 `BoxFit` 控制图片的适应方式。

+ 背景图片: 使用 **`BoxDecoration`** 和 **`DecorationImage`** 小部件设置背景图

  - 网络图片：NetworkImage

  - 本地图片：AssetImage

  - 通过 `BoxFit` 控制图片的适应方式。

```dart
child: Image.network('https://img0.didiglobal.com/static/soda_public/do1_7dP9lvNVWKJb37WwlPVV', fit: BoxFit.cover),

// 背景图
Container(
  width: double.infinity,
  height: 200,
  decoration: const BoxDecoration(
    image: DecorationImage(
      image: NetworkImage('https://img0.didiglobal.com/static/soda_public/do1_7dP9lvNVWKJb37WwlPVV'),
      fit: BoxFit.cover,
    ),
  ),
),
```

### 本地图片资源

```
1、将图片添加到项目中：将图片文件放置在 assets/images 目录中。目录不存在，创建目录。
2、更新 pubspec.yaml 文件：在 pubspec.yaml 文件中声明资产目录。
assets":
  -assets/images/
```

## ICON使用

### flutter默认icon使用

flutter默认包含图标，这些图标来自 Google 的 Material Design 规范。可以直接使用这些图标，无需额外配置Icons.home

```dart
Icon(
	Icons.home_filled,
	size: 30,
	color:Colors.red
)
```

### 自定义icon

```dart
1.引入资源包 assets/fonts/xxx.ttf  assets/fonts/xxx.json
2.配置pubspec.yaml
- family: AutoIcons
      fonts:
        - asset: assets/iconfonts/auto-icons.ttf
3.定义字体类，方便统一管理
import 'package:flutter/material.dart' show IconData;
class AutoIcons {
  static const String _family = 'AutoIcons';
  // 私有构造函数，防止该类被实例化
  AutoIcons._();
  static const IconData Crm__CalendarVisit = IconData(0xEA01, fontFamily: _family); 

使用 
Icon(
	AutoIcons.didiacademy,
	size: 40,
	color:Colors.blue
),
```

# 状态管理

在 Flutter 框架下，状态管理是一个重要的主题。状态管理的主要目的是管理和维护应用程序的状态，以便用户界面可以响应状态的变化。Flutter 提供了多种状态管理的方法，这些方法可以类比到前端开发中的一些概念和工具，例如 Vue 的状态管理。

## setState

最基本的状态管理方法。它是 `StatefulWidget` 类的一部分，通过调用 `setState`，Flutter 会重新构建 `State` 对象的 `build` 方法，从而更新 UI。

适用于小型应用或单一页面的状态管理，只影响当前的 `State` 对象，适合简单的 UI 状态，如表单输入、按钮点击等

```dart
class Counter extends StatefulWidget {
  @override
  _CounterState createState() => _CounterState();
}
 
class _CounterState extends State<Counter> {
  int _count = 0;
 
  void _increment() {
    setState(() {
      _count++;
    });
  }
 
  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        Text('Count: $_count'),
        ElevatedButton(
          onPressed: _increment,
          child: Text('Increment'),
        ),
      ],
    );
  }
}
```

## Provider

```
优点
+ 功能完善，涵盖了ScopedModel和InheritedWidget的所有功能
+ 数据逻辑完美融入了widget树中，代码结构清晰，可以管理局部状态和全局状态
+ 解决了多model和资源回收的问题
+ 对不同场景下使用的provider做了优化和区分
+ 支持异步状态管理和provider依赖注入
缺点
+ 使用不当可能会造成性能问题（大context引起的rebuild）
+ 局部状态之前的数据同步不支持
```

官方推荐的状态管理方式之一 Provider ，提供了集中管理应用状态的功能，类比Vuex，是 Vue.js 的官方状态管理库，用于在 Vue 组件之间共享和管理状态

### 1.在 `pubspec.yaml` 文件中添加 `provider` 依赖

是一个用于提供值的 Widget。它将一个对象（通常是状态）提供给 Widget 树中的子 Widget，使得子 Widget 可以访问这个值

```
deoendencies:
	provider: 6.1.1
```

### 2.创建状态类

+ **ChangeNotifier**：Flutter 提供的一个类，它允许对象通知其监听者状态发生变化

+ **notifyListeners()：**通知所有监听者状态已经更新

```dart
创建一个文件 CounterModel.dart
 
import 'package:flutter/material.dart';
 
// 创建一个状态类，它继承 ChangeNotifier，用于管理计数器的状态
class CounterModel extends ChangeNotifier{
  int _counter;
  int get counter => _counter;
  CounterModel(this._counter);
 
  void add() {
    _counter++;
    notifyListeners();  // 通知所有监听者状态已经更新
  }
}
```

### 3.初始化类的实例

**ChangeNotifierProvider**：

- create：初始化和提供一个 ChangeNotifier 实例，并将其注入到 Widget 树中，使得子 Widget 可以方便地访问和监听这个状态对象。
- value：用于将一个已存在的 ChangeNotifier 实例提供给 widget 树，而不是创建新的实例。这对于共享和重用现有实例非常有用

```dart

import 'package:provider/provider.dart';
import 'model/counter_model.dart';
void main() {
  runApp(
    // 设置 ChangeNotifierProvider，创建CounterModel实例 提供给 widget 树
    // ChangeNotifierProvider.value: 用于将一个已存在的 ChangeNotifier 实例提供给 widget 树，而不是创建新的实例。这对于共享和重用现有实例非常有用。
    ChangeNotifierProvider(
      create: (context) => CounterModel(1),
      child: const MyApp(),
    ),
   )
  );
}
```

### 4.改变状态

**Provider.of<CounterModel**>(context, listen: false).add()

- listen：true：默认态
- listen：false：不希望该调用会对状态变化进行监听。这通常用于在状态发生变化时不重新构建当前 Widget 的情况

**context.read**<CounterModel>().add();

访问实例CounterModel，并调用add方法

```dart
class _MyHomePageState extends State<MyHomePage> {
 
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text(widget.title),
      ),
      body: Center(
        child: Column(
          // horizontal).
          mainAxisAlignment: MainAxisAlignment.center,
          children: <Widget>[
            const Text(
              'You have pushed the button this many times:',
            ),
            Text(
             '$_counter',
              style: Theme.of(context).textTheme.headline4,
             ),
          ],
        ),
      ),
      floatingActionButton: FloatingActionButton(
        // 改变值的状态！
        onPressed: () {
          // listen: false状态发生变化时不重新构建当前 Widget 的情况  true：默认态
          // Provider.of<CounterModel>(context, listen: false).add();
          context.read<CounterModel>().add();
        },
        tooltip: 'Increment',
        child: const Icon(Icons.add),
      ), // This trailing comma makes auto-formatting nicer for build methods.
    );
  }
}
```

### 5.监听状态改变

**Consumer**: 是一个 Widget，用于监听并响应 Provider 提供的状态变化

**`Provider.of<T>(context)`** ：用于在 Widget 中访问提供的状态。通常与 `listen` 参数一起使用，以决定是否需要监听状态变化。

**Selector**: 用于选择和监听 `Provider` 中的特定状态部分，将监听刷新的范围控制到最小，以避免不必要的重建

```dart
class _MyHomePageState extends State<MyHomePage> {
 
 
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text(widget.title),
      ),
      body: Center(
        child: Column(
          // horizontal).
          mainAxisAlignment: MainAxisAlignment.center,
          children: <Widget>[
            const Text(
              'You have pushed the button this many times:',
            ),
            // 以下三种方法都是监听值的变化
 
            // 使用 Consumer 来监听 CounterModel 的变化
            Consumer<CounterModel>(builder: (context, counterModel, child ) => Text(
              '${counterModel.counter}',
              style: Theme.of(context).textTheme.headline4,
            ),),
 
            // 使用 Provider.of 来获取 CounterModel 值的变化
            // Text(
            //   '${Provider.of<CounterModel>(context).counter}',
            //   style: Theme.of(context).textTheme.headline4,
            // ),
 
            // 使用 Selector 监听 CounterModel 中的 counter 属性, 用于监听 Provider 中的特定状态部分，以避免不必要的重建
            Selector<CounterModel, int>(
              selector: (context, counterModel) => counterModel.counter,
              builder: (context, counter, child) {
                return Text(
                  'Counter: $counter',
                  style: Theme.of(context).textTheme.headline4,
                );
              },
            ),
 
          ],
        ),
      ),
      floatingActionButton: FloatingActionButton(
         
        onPressed: () {
          // listen: false状态发生变化时不重新构建当前 Widget 的情况  true：默认态
          // Provider.of<CounterModel>(context, listen: false).add();
          context.read<CounterModel>().add();
        },
        tooltip: 'Increment',
        child: const Icon(Icons.add),
      ), // This trailing comma makes auto-formatting nicer for build methods.
    );
  }
}
```

**注意：** Provider 会监听 Value 的变化而更新整个 context 上下文，因此如果 build 方法返回的 Widget 过大过于复杂的话，

刷新的成本是非常高的。那么我们该如何进一步控制 Widget 的更新范围呢？

那就是将真正需要更新的 Widget 封装成一个独立的 Widget，将取值方法放到该 Widget 内部。

# 网络请求



# 存储方法

## **SharedPreferences**

存储键值对形式的数据

+ 添加依赖

```dart
// 在 pubspec.yaml 文件中添加 shared_preferences 依赖
dependencies:
  shared_preferences: ^2.0.0
```

+ SharedPreferences实例化

```dart
// 通过异步方法 `SharedPreferences.getInstance()，这个方法返回一个 Future 对象`
import 'package:shared_preferences/shared_preferences.dart';
 
Future<void> example() async {
  final prefs = await SharedPreferences.getInstance();
}
```

+ 存储数据

```dart
Future<void> saveData() async {
  final prefs = await SharedPreferences.getInstance();
  // 存储字符串
  await prefs.setString('username', 'JohnDoe');
  // 存储整数
  await prefs.setInt('userAge', 25);
  // 存储布尔值
  await prefs.setBool('isLoggedIn', true);
  // 存储浮点数
  await prefs.setDouble('userHeight', 5.9);
  // 存储字符串列表
  await prefs.setStringList('userHobbies', ['Reading', 'Gaming']);
}
```

+ 读取数据

```
prefs.getXXX
```

+ 删除数据 or 清除数据

```
await prefs.remove('username');
await prefs.clear();
```

## File Storage

适合需要文件系统操作的场景。

+ 创建文件和目录

```dart
// 使用 Directory 和 File 类来处理文件和目录操作
import 'dart:io';
 
Future<void> createFile() async {
  // 获取应用的文档目录
  final directory = await Directory.systemTemp.createTemp();
   
  // 定义文件路径
  final filePath = '${directory.path}/example.txt';
   
  // 创建文件
  final file = File(filePath);
   
  // 写入数据到文件
  await file.writeAsString('Hello, Flutter!');
   
  print('File created at: ${file.path}');
}
```

+ 写入文件

```dart

Future<void> writeFile() async {
  final file = File('path/to/your/file.txt');
  // 写入字符串数据：将字符串数据写入文件。如果文件不存在则创建文件，文件内容会被覆盖
  await file.writeAsString('Hello, Flutter! This is a test.');
   
  // 或者写入字节数据：将字节数据写入文件。如果文件不存在则创建文件，文件内容会被覆盖。
  final bytes = Uint8List.fromList([0, 1, 2, 3, 4]);
  await file.writeAsBytes(bytes);
   
  // 同步地将字符串数据写入文件。与 writeAsString 类似，但在调用时不会返回 Future
  file.writeAsStringSync(contents);
  // 同步地将字节数据写入文件。与 writeAsBytes 类似，但在调用时不会返回 Future。
  file.writeAsBytesSync(bytes);
}
```

+ 读取文件

```dart
Future<void> readFile() async {
  final file = File('path/to/your/file.txt');
   
  // 读取字符串数据：将文件内容作为字符串读取，适用于文本文件。
  final contents = await file.readAsString();
   
  // 读取字节数据：将文件内容作为字节数组读取，适用于二进制文件或需要处理原始数据的场景。
  final bytes = await file.readAsBytes();
   
  // 按行读取：将文件内容按行读取并返回一个字符串列表。适用于每行需要单独处理的文本文件。
  final lines = await file.readAsLines();
   
  print('File contents: $contents');
  print('File bytes: $bytes');
}
```

+ 删除文件

```dart
Future<void> deleteFile() async {
  final file = File('path/to/your/file.txt');
  // 判断文件存不存在，异步方法，返回一个 Future<bool>
  if (await file.exists()) {
    await file.delete();
    print('File deleted');
  } else {
    print('File does not exist');
  }
  // existsSync()：同步方法，会立即返回结果
}
```

+ 列出目录中的文件

```dart

Future<void> listFiles() async {
  final directory = Directory('path/to/your/directory');
   
  // 获取目录中的所有文件
  final files = directory.listSync();
   
  for (var file in files) {
    print(file.path);
  }
}
```

+ 获取目录路径

```
依赖spath_provider 插件

dependencies:
  path_provider: ^2.0.0
```

```dart
import 'package:path_provider/path_provider.dart';
 
Future<void> writeToFile() async {
  // 获取应用的文档目录-永久位置
  final directory = await getApplicationDocumentsDirectory();
  // 获取应用的文档目录-临时位置
  final documentsDir = await getTemporaryDirectory();
   
  // 定义文件路径
  final filePath = '${directory.path}/example.txt';
   
  final file = File(filePath);
   
  // 写入数据
  await file.writeAsString('Hello, Flutter with path_provider!');
   
  print('File created at: ${file.path}');
}
```



