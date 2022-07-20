---
layout: post
index: 120
tags: 原理图
---

# 12.规则使用 2

### random(random.ts 默认导出)

- 随机生成文件及内容

### rename

- 符合判断的文件,自定义返回重命名

### externalSchematic

- 运行其他仓库的额外原理图,也就是不是你这个仓库中使用的(自己的也行...不过应该没人这么用吧)

### schematic

- 运行当前仓库下的其他原理图

### source(Source)

- tree 作为来源

### empty(Source)

- 空来源

### noop

- 没有任何事情发生

### filter

- 过滤文件用

### asSource(Source)

- rule 转 source
- rule 是在空 tree 中运行

### branchAndMerge

- 先创建一个分支,在分支中修改再合并
- 不过不太懂什么意思,因为创建和合并是一个操作...
- 指定规则的合并策略?

### partitionApplyMerge

- 分成两部分处理,处理后将两部分合并

### forEach

- 对符合文件的操作进行更新处理
- 删除,重命名,修改

### applyToSubtree

- 防止规则在使用中产生新的子 tree(保证使用时的 tree 实例,就是返回时的 tree 实例)


## 视频
{% include video.html queryParams="aid=250851082&bvid=BV1rv411G7ay&cid=418974688&page=1" %}
