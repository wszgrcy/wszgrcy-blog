---
layout: post
index: 170
tags: typescript typescript-compiler
title: Typescript-Compiler-API入门4
---

## 节点类型断保护

- ts.isxXXXX
  > 比如`ts.isExpressionStatement(statement)`判断是否是一个表达式声明

## 更新节点

- factory.updateXXXX(原来的 ts.updateXXXX 已经废弃,目前改为 factory)
  > 比如`factory.updateVariableDeclaration`更新变量声明,传参就是变量声明里面的节点

```ts
// 更新声明的赋值为456789
let value = factory.createNumericLiteral(456789);
node = factory.updateVariableDeclaration(node, node.name, node.exclamationToken, node.type, value);
```

### 正常更新节点

### 更新节点注释

- 获得类型检查
- addSyntheticLeadingComment
> 在节点前面上添加多行注释

```ts
node = ts.addSyntheticLeadingComment(node, ts.SyntaxKind.MultiLineCommentTrivia, '这是一个注释', false);
```

- getSyntheticLeadingComments
> 获取节点注释

- addSyntheticTrailingComment
- getSyntheticTrailingComments

- getLeadingCommentRanges
> 也可以拿到注释,但是需要注释的位置
## 打印节点

- ts.createPrinter

## 视频

{% include video.html queryParams="aid=720895318&bvid=BV1qQ4y16798&cid=421289039&page=1" %}
