---
title: nestjsx/crud 初体验
tags: nodejs
categories: Node.js
abbrlink: 3036132183
date: 2022-08-10 23:16:57
---

CRUD是指在做计算处理时的增加(Create)、读取查询(Retrieve)、更新(Update)和删除(Delete)几个单词的首字母简写。
主要被用在描述软件系统中DataBase或者持久层的基本操作功能。

项目地址:
[https://github.com/nestjsx/crud](https://github.com/nestjsx/crud)
[https://github.com/nestjsx/crud/wiki](https://github.com/nestjsx/crud/wiki)

<!-- more -->

## 引入 crud，自定生成接口

```ts
import { Controller } from "@nestjs/common";
import { Crud, CrudController } from "@nestjsx/crud";

import { Company } from "./company.entity";
import { CompaniesService } from "./companies.service";

@Crud({
  model: {
    type: Company,
  },
})
@Controller("companies")
export class CompaniesController implements CrudController<Company> {
  constructor(public service: CompaniesService) {}
}
```

1. 使用 @Crud 装饰器来描述需要增删改查的表或者orm实体
2. @Crud 装饰器绑定到 @Controller 上，会**生成**增删改查的相关接口
如：GET /heroes
   GET /heroes/:heroId/perks 等等
3. CrudOptions 中有关于增删改查的配置，可以按需修改

```ts
export interface CrudOptions {
  model: ModelOptions;
  dto?: DtoOptions;
  serialize?: SerializeOptions;
  query?: QueryOptions;
  routes?: RoutesOptions;
  routesFactory?: typeof CrudRoutesFactory;
  params?: ParamsOptions;
  validation?: ValidationPipeOptions | false;
}

// 其中 查询 相关的字段例子
export interface QueryOptions {
    allow?: QueryFields;
    exclude?: QueryFields;
    persist?: QueryFields;
    filter?: QueryFilterOption;
    join?: JoinOptions;
    sort?: QuerySort[];
    limit?: number;
    maxLimit?: number;
    cache?: number | false;
    alwaysPaginate?: boolean;
    softDelete?: boolean;
}
```

## 总结

在我们一般开发的项目中，如后台管理，会对单个表进行简单的查询，甚至修改，会有很多表，他们的逻辑几乎很相似，查询，修改，删除，添加等，
如果针对每个表都写一套相似的逻辑，不仅很累人，还会增大很多工作量，于是聪明的程序员就将这套行为相似的逻辑封装了起来。

对一个表进行装饰，同时会针对这个表会生成N多的接口（用于增删改查），这样就不用自己再写了！

# 大佬们 YYDS

