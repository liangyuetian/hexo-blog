---
title: height与min-height高度继承问题
playlist:
  - name: Secret
    artist: 茶太
    url: //music.163.com/song/media/outer/url?id=449818741.mp3
    cover: >-
      //p2.music.126.net/4gzU-pTgbwBLHwx4-CJcgw==/903798558032135.jpg?param=90y90
date: 2019-02-17 14:59:05
tags:
  - css
  - 大坑
categories: 
---



<!-- more -->

在父元素设置<copy>min-height:100%</copy>时，子元素设置<copy>height:100%</copy>发现高度无法被撑起，
原来，height继承于父元素的height，当height不设置时，height高度为auto,所以子元素继承的高度为auto,所以无法撑起
只要设置height为一个固定的值，那么height就不在是auto，那么子元素继承的就当前值，
由于父元素设置了min-height:100%,那么min-height便会覆盖height，这样子元素的height就可以正常继承min-height产生的值了
