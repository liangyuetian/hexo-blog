---
title: flutter-文件储存
abbrlink: 3794814147
date: 2019-09-27 11:56:23
tags:
categories:
---

flutter 文件存储
[Flutter开发者文档-数据储存](http://flutter.link/2018/04/13/Flutter%E4%B8%AD%E7%9A%84%E6%9C%AC%E5%9C%B0%E5%AD%98%E5%82%A8/)

<!-- more -->

### 1 在pubspec.yaml文件中添加依赖
```yaml
path_provider: ^0.4.0
```
并更新
```bash
flutter packages get
```

### 2 导入使用

```datr
import ‘package:path_provider/path_provider.dart’;

```

在path_provider中有三个获取文件路径的方法：

* getTemporaryDirectory()//获取应用缓存目录，等同IOS的NSTemporaryDirectory()和Android的getCacheDir() 方法
* getApplicationDocumentsDirectory()获取应用文件目录类似于Ios的NSDocumentDirectory和Android上的 AppData目录
* getExternalStorageDirectory()//这个是存储卡，仅仅在Android平台可以使用


### 3 首先我们先获取存储目录

```dart
Future<String> get _localPath async {
   final directory = await getApplicationDocumentsDirectory();

   return directory.path;
}
 ```
### 4 然后在本地建立文件（不存在这个名字的自动创建并返回，存在则直接返回这个文件对象），名字就叫做nameFile吧。

```dart
Future<File> get _localFile async {
  final path = await _localPath;
  return new File('$path/nameFile.txt');
}
```

### 4 写入文件内容
```dart
Future<File> save(String name) async {
  final file = await _localFile;
  return file.writeAsString(name);
}
```

### 5 读取内容

```dart
Future<String> get() async {
  final file = await _localFile;
  return file.readAsString();
}

Future<String> get() async {
  final file = await _localFile;
  return file.readAsString();
}
```