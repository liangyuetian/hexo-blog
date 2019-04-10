---
title: flutter-PopupMenu-下拉菜单
playlist:
  - name: Secret
    artist: 茶太
    url: //music.163.com/song/media/outer/url?id=640866.mp3
    cover: >-
      //p2.music.126.net/4gzU-pTgbwBLHwx4-CJcgw==/903798558032135.jpg?param=90y90
tags: flutter
categories: Flutter
abbrlink: 38154
date: 2019-02-02 13:31:10
---

PopupMenuButton 下拉菜单按钮
PopupMenuItem 下拉元素

<!-- more -->

### 重点方法

**PopupMenuButton** 下拉菜单按钮
**PopupMenuItem** 下拉元素
[https://flutterchina.club/catalog/samples/basic-app-bar/](https://flutterchina.club/catalog/samples/basic-app-bar/)·
```dart
new PopupMenuButton<Choice>( // overflow menu
  onSelected: _select,
  icon: new Icon(Icons.add), // 默认图标是竖着的三个点，但是可以重新设置
  itemBuilder: (BuildContext context) {
    // skip(2) 方法是跳过前2个
    return choices.skip(2).map((Choice choice) {
      return new PopupMenuItem<Choice>(
        value: choice,
        child: new Text(choice.title),
      );
    }).toList();
  },
)
```

> main.dart 
```dart
// Copyright 2017 The Chromium Authors. All rights reserved.
// Use of this source code is governed by a BSD-style license that can be
// found in the LICENSE file.

import 'package:flutter/material.dart';

// This app is a stateful, it tracks the user's current choice.
class BasicAppBarSample extends StatefulWidget {
  @override
  _BasicAppBarSampleState createState() => new _BasicAppBarSampleState();
}

class _BasicAppBarSampleState extends State<BasicAppBarSample> {
  Choice _selectedChoice = choices[0]; // The app's "state".

  void _select(Choice choice) {
    setState(() { // Causes the app to rebuild with the new _selectedChoice.
      _selectedChoice = choice;
    });
  }

  @override
  Widget build(BuildContext context) {
    return new MaterialApp(
      home: new Scaffold(
        appBar: new AppBar(
          title: const Text('Basic AppBar'),
          actions: <Widget>[
            new IconButton( // action button
              icon: new Icon(choices[0].icon),
              onPressed: () { _select(choices[0]); },
            ),
            new IconButton( // action button
              icon: new Icon(choices[1].icon),
              onPressed: () { _select(choices[1]); },
            ),
            new PopupMenuButton<Choice>( // overflow menu
              onSelected: _select,
              icon: new Icon(Icons.add), // 默认图标是竖着的三个点，但是可以重新设置
              itemBuilder: (BuildContext context) {
                // skip(2) 方法是跳过前2个
                return choices.skip(2).map((Choice choice) {
                  return new PopupMenuItem<Choice>(
                    value: choice,
                    child: new Text(choice.title),
                  );
                }).toList();
              },
            ),
          ],
        ),
        body: new Padding(
          padding: const EdgeInsets.all(16.0),
          child: new ChoiceCard(choice: _selectedChoice),
        ),
      ),
    );
  }
}

class Choice {
  const Choice({ this.title, this.icon });
  final String title;
  final IconData icon;
}

const List<Choice> choices = const <Choice>[
  const Choice(title: 'Car', icon: Icons.directions_car),
  const Choice(title: 'Bicycle', icon: Icons.directions_bike),
  const Choice(title: 'Boat', icon: Icons.directions_boat),
  const Choice(title: 'Bus', icon: Icons.directions_bus),
  const Choice(title: 'Train', icon: Icons.directions_railway),
  const Choice(title: 'Walk', icon: Icons.directions_walk),
];

class ChoiceCard extends StatelessWidget {
  const ChoiceCard({ Key key, this.choice }) : super(key: key);

  final Choice choice;

  @override
  Widget build(BuildContext context) {
    final TextStyle textStyle = Theme.of(context).textTheme.display1;
    return new Card(
      color: Colors.white,
      child: new Center(
        child: new Column(
          mainAxisSize: MainAxisSize.min,
          crossAxisAlignment: CrossAxisAlignment.center,
          children: <Widget>[
            new Icon(choice.icon, size: 128.0, color: textStyle.color),
            new Text(choice.title, style: textStyle),
          ],
        ),
      ),
    );
  }
}

void main() {
  runApp(new BasicAppBarSample());
}
```