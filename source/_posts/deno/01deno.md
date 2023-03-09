---
title: deno 如何导入 npm 包
tags: deno import
categories: Deno
abbrlink: 1943163828
date: 2023-03-07 22:58:12
---

```shell
import * as typeorm from 'npm:typeorm'
import * as _ from "https://deno.land/x/lodash_es@v0.0.2/mod.ts";

```

<!-- more -->

# 总结

```ts
import * as _ from "https://deno.land/x/lodash_es@v0.0.2/mod.ts";
import chalk from "npm:chalk@5"; // 使用 npm 包 最靠谱
import typeorm from 'https://unpkg.com/typeorm/browser/index.js' // 需要此包build出 module 模块

import _ from "https://esm.sh/lodash-es"; // 使用esm.sh cdn，并不是每个包都可以被正确导入
import react from 'https://jspm.dev/react' // 使用 jspm
import react from 'https://esm.run/react' // 使用 esm.run

```

# 官方仓库
[https://deno.land/x](https://deno.land/x)

# 从 npmjs.com 导入 npm 包

[https://deno.land/manual@v1.31.1/node/npm_specifiers](https://deno.land/manual@v1.31.1/node/npm_specifiers)

