[toc]

# 资料

《Flutter实战·第二版》(https://book.flutterchina.club/)https://book.flutterchina.club/chapter2/first_flutter_app.html

https://flutter.cn/docs/development/ui/layout

# 安装

+ Android studio
+ xcode
+ flutter
+ vscode
  + 安装flutter插件
  + 查看-命令- **File>New Flutter Project** 新建flutter
  + 查看-命令- choose flutter select device
  + flutter run
  + 热更新：在命令行输入r

# widget简介

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

# 相关快捷键

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





