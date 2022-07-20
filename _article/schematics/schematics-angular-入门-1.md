---
layout: post
index: 30
tags: 原理图
---

# 3.Schematics Angular 入门 1

## 构建
- ng下使用schematics与直接使用的依赖包一样,只不过是调用方式不同
## npm link/publish

- npm link 本地测试
- npm publish 生产,供别人使用

## npm link xxx/install xxx

- npm link schematics-tutorial 本地测试安装
> link测试有时候会因为一些依赖关系得不到想要的结果.所以最好建议能够模拟真实安装测试,比如verdaccio.或者直接复制整个依赖到相关项目下测试
- npm install schematics-tutorial 发布后安装

## 命令执行

- ng g schematics-tutorial:hello-world
> 只能在ng项目下使用

## 视频
{% include video.html queryParams="aid=250630517&bvid=BV1xv41137cs&cid=419214652&page=1" %}

