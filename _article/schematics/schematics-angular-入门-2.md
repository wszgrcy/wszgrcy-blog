---
layout: post
index: 100
tags: 原理图
---

# 10.Schematics Angular 入门 2(命令相关)

## ng new

## ng g(generate)

- 常规使用

## ng add

- `ng add` 会先安装某个包(可以理解为 npm i),然后再执行里面为`ng-add`的原理图
- 新增,只要以下两个地方设置了就行了

```json
// package.json
{
  //...
  "ng-add": {
    // 当添加这个包的时候,保存到哪里
    "save": "devDependencies"
  }
}
```

```json
//collection.json
{
  //...
  "ng-add": {
    "factory": "./ng-add",
    "description": "ng-add",
    "schema": "./ng-add/schema.json"
  }
}
```

## ng update

- 更新

```json
{
  //...
  "ng-update": {
    "migrations": "./src/migrations/migrations.json",
    // 这里的意思是这个组的也会连带升级
    "packageGroup": {
      "@angular/core": "12.2.8"
    }
  }
}
```


## 视频
{% include video.html queryParams="aid=250764904&bvid=BV1Nv411G7MU&cid=419022848&page=1" %}

