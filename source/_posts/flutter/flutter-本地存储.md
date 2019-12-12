---
title: flutter-本地存储
abbrlink: 1340560355
date: 2019-09-27 11:35:13
tags:
categories:
---

在Flutter中，我们使用shared_preferences库来同时支持Android和ios平台。
相当于浏览器的 localStorage

<!-- more -->

[Flutter开发者文档-数据储存](http://flutter.link/2018/04/13/Flutter%E4%B8%AD%E7%9A%84%E6%9C%AC%E5%9C%B0%E5%AD%98%E5%82%A8/)

### 1 在pubspec.yaml文件中添加依赖
```yaml
shared_preferences: "^0.4.2"
```
并更新
```bash
flutter packages get
```

### 2 导入使用

```datr
import 'package:shared_preferences/shared_preferences.dart';

```
### 3 增删改查
```dart
SharedPreferences prefs = await SharedPreferences.getInstance();
// 增 改(重新赋值就好)
prefs.setString(key, value)
prefs.setBool(key, value)
prefs.setDouble(key, value)
prefs.setInt(key, value)
prefs.setStringList(key, value)

// 删
prefs.remove(key); //删除指定键
prefs.clear();//清空键值对

// 查
_prefs.then((SharedPreferences prefs) {
    return (prefs.getBool('disclaimer::Boolean') ?? false);
});
// 或
Future<String> get(String mUserName = 'userName') async {
  var userName;
    SharedPreferences prefs = await SharedPreferences.getInstance();
    userName = await prefs.getString(mUserName);
  return userName;
}
Future<String> userName = get();
userName.then((String userName) {
Scaffold.of(context).showSnackBar(
        SnackBar(content: Text("数据获取成功：$userName")));
});
```

### 4 支持类型
* setBool
* setInt
* setDouble
* setString
* setStringList



