---
layout: post
index: 140
tags: typescript typescript-compiler
title: Typescript-Compiler-API入门1
---
- 手动编译ts文件
> 使用api编译ts

## 读取配置

## 创建程序

## 发射内容

```ts
import * as fs from 'fs';
import ts from 'typescript';
let fileName = require.resolve('../tsconfig.json');
let tsconfig = fs.readFileSync(fileName);
let jsonSourceFile = ts.parseJsonText(fileName, tsconfig.toString());
// 读取配置
let config = ts.parseJsonSourceFileConfigFileContent(jsonSourceFile, ts.sys, process.cwd());
// 创建程序
let program = ts.createProgram({
  rootNames: config.fileNames,
  options: config.options,
});
// 发射内容
program.emit();

```

## 视频
{% include video.html queryParams="aid=250968903&bvid=BV14v411g7YP&cid=420097744&page=1" %}
