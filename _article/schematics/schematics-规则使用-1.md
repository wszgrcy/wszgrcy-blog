---
layout: post
index: 20
tags: 原理图
---

# 2.Schematics 规则使用 1

- 主要为模板相关规则及来源

## template

- ejs 语法,简化版
  > `packages\angular_devkit\schematics\src\rules\template.ts:166` > `packages\angular_devkit\core\src\utils\template.ts:357`
- 通常联合使用,将 tree 中所有的模板(假如有),进行相关替换
- 如果放到主 tree 中使用,可能会产生未知影响

## renameTemplateFiles

- 移除文件的`.template`后缀(使用的是 rename 规则)

## applyTemplates

- 会将`.template`的识别为模板
- 相当于`template`+`renameTemplateFiles`规则

## url

- 提取文件,转化为 tree
- 这里的相对路径其实是运行上下文的路径,也就是启动入口的相对路径

## chain

- 一个规则接着一个规则链式调用

## mergeWith

- 将某一个 Source 处理过的 Rule 合并会主 tree
- 如果不使用这个,会导致后面主 tree 的内容变成上一个 Source 的

## move

- 移动文件或文件夹
- 一个参数代表从根移动到某个文件夹


## 视频
{% include video.html queryParams="aid=718241663&bvid=BV1uQ4y1C7gG&cid=419209094&page=1" %}

