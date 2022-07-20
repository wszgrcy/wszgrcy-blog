---
layout: post
index: 10
tags: 原理图
---

# 1.Schematics 入门 1

## 流程

### 安装

- `npm i @angular-devkit/schematics-cli -g`

### 快速初始化

- `schematics @angular-devkit/schematics-cli:blank schematics-tutorial`
  > 上面的命令意思是执行`@angular-devkit/schematics-cli`库中的`blank`原理图,传入参数为`schematics-tutorial`
  > 源码位置`angular-cli`下`packages\angular_devkit\schematics_cli\blank`

### 执行

- `schematics .:schematics-tutorial`

### hello world

- 修改`src/schematics-tutorial/index.ts`

```ts
import { Rule, SchematicContext, Tree } from '@angular-devkit/schematics';
export default function (options: any): Rule {
  return (tree: Tree, context: SchematicContext) => {
    context.logger.info('hello world');
    return tree;
  };
}
```

- tsc 手动编译一下(因为原理图还是执行 js 的)

### 手动实现

- 看[视频](https://www.bilibili.com/video/BV1T34y1D7mK/?spm_id_from=333.788&vd_source=e12a10b2c48383fa34d7ec0e25a880c7)

### template 规则的调用

```ts
import { chain, mergeWith, Rule, SchematicContext, Tree, url } from '@angular-devkit/schematics';
export default function (options: any): Rule {
  return (tree: Tree, context: SchematicContext) => {
    let source = url('./template');
    return chain([mergeWith(source)]);
  };
}
```

## 路径与包名

- 使用路径时,会认为是开发模式,使用包名,为正常模式
  > 开发模式不会真实的写入文件,但是会进行正常的逻辑,也就是`dry-run`,所以如果需要修改真实文件,那么需要添加参数`-debug false`
  > `Debug mode enabled by default for local collections.`表示为开发模式
  > 本地模式位置`packages\angular_devkit\schematics_cli\bin\schematics.ts:141`
- 路径与包名都是相对于 cwd 及内部的 bin 进行查找
  > 后者 bin 内显然查不到.....也就是相对于 cwd
  > 包查找位置`packages\angular_devkit\schematics\tools\node-module-engine-host.ts:65`

## 工作空间

- 工作空间的意思就是原理图操作时,默认的修改文件夹是哪个,这里为 cwd 的位置
  > 源码`packages\angular_devkit\schematics_cli\bin\schematics.ts:150`

## `collection.json`

- 声明原理图的一个文件,只有写到这里,才会在调用的时候调用到

```json
{
    "schematics": {
        // 你的原理图名字
        "hello-world": {
            // 入口文件.如果是index.ts并且为export default可以这么省略写
            // 如果是指定某个导出函数`zz`,则需要`./path/to#zz`
            "factory": "./hello-world",
            "description": "hello-world"
        },
    }
}
```
- 在`package.json`中增加` "schematics": "./src/collection",`

```json
{
  //...
  // 你的collection.json的路径
  "schematics": "./src/collection"
  //...
}
```

## 视频
{% include video.html queryParams="aid=805689341&bvid=BV1T34y1D7mK&cid=419221835&page=1" %}
