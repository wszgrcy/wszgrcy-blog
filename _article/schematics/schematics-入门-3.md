---
layout: post
index: 80
tags: 原理图
---

# 8.Schematics 入门 3(SchematicContext)

## 属性(只读)

- debug 调试
- engine 引擎
- interactive 交互
- logger 调试日志打印(类似 console.log)
- schematic 当前原理图实例
- strategy 合并策略

## addTask

- 添加任务
- 不允许添加自定义任务,只能使用预构建的https://github.com/angular/angular-cli/issues/12678

### NodePackageInstallTask

- 安装依赖使用

### RepositoryInitializerTask

- git 仓库初始化

### RunSchematicTask

- 执行其他原理图使用

## schematic

- 当前原理图实例

### call

- 再执行下自己

### collection

- 当前原理图所在的集合
- 可以通过这个实例执行其他原理图
- 列出集合中原理图名字

### description

- 当前原理图的一些元数据信息

## engine

- defaultMergeStrategy 默认合并策略

### createCollection

- 创建集合

### createSchematic

- 创建原理图

### createContext

- 创建上下文

### createSourceFromUrl

- 读文件
- url 规则用的就是这个

### executePostTasks

- 执行任务(addTask)里面的

### transformOptions

- 测试下,可以对参数进行一些处理(比如根据 schema 验证,添加默认值)
- 非测试下,如果没有参数并且设置过交互,可以提示输入

### workflow

- 测试环境下不存在,无法测试
- 非测试下 context 内容基本上与 schematicsContext 重合
- execute 相当于手动执行原理图(更底层,相当于多个分支)


## 视频
{% include video.html queryParams="aid=208255556&bvid=BV1ch411H756&cid=419062444&page=1" %}
