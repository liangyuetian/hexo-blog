域名
[https://liangyuetian.cn/](https://liangyuetian.cn/)

部署地址
```bash
https://liangyuetian.github.io/
```

主题bmw仓库
```bash
https://github.com/dongyuanxin/theme-bmw.git
```

比较好看的主题
---
[hexo-theme-awe](https://github.com/kywk/hexo-theme-awe) [demo](https://kywk.github.io/hexo-theme-awe/)
---
[hexo-theme-aiki](https://github.com/foreachsam/hexo-theme-aiki) [demo](https://foreachsam.github.io/blog-framework-semantic-ui/article/)
---
[hexo-theme-aiki](https://github.com/foreachsam/hexo-theme-aiki) [demo](https://foreachsam.github.io/blog-framework-semantic-ui/article/)


git submodule 使用
添加
```bash
git submodule add  https://github.com/iissnan/hexo-theme-next themes/next
```
删除
```bash
git rm --cached themes/next
```

更新
```bash

```

初始化 新拉项目时使用
```bash
git submodule init
git submodule update
```


有帮助的文章
[http://muyunyun.cn/posts/f55182c5/#more](http://muyunyun.cn/posts/f55182c5/#more)
[http://frankorz.com/2016/09/30/Hexo-patch](http://frankorz.com/2016/09/30/Hexo-patch)


不渲染 html 文件
在不想被渲染的 html 文件最上面添加如下代码
```BASH
layout: false
```

不渲染 md 文件
使用上面的办法虽然不会渲染 md 文件，但是还是将 md 文件转化成了 html 文件，如果想保留原 md 文件后缀要怎么做呢？这就需要在 站点配置文件 _config.yml 中配置，找到 skip_render 参数，开始匹配的位置是基于你的 source_dir 的，一般来说，是你的 source 文件夹下。下面我分别列举几种常见的情况进行说明：
```BASH
skip_render: test/* 单个文件夹下全部文件
skip_render: test/*.md 单个文件夹下指定类型文件
skip_render: test/** 单个文件夹下全部文件以及子目录
多个文件夹以及各种复杂情况：
skip_render: README.md
  - `test1/*.html`
  - `test2/**`
```