---
layout: post
index: 60
tags: 原理图
---

# 6.tsc 语法节点查询 1

## Typescript

- typescript 自带 api

```ts
let nodeOrString = ts.createSourceFile('', nodeOrString, ts.ScriptTarget.Latest);

function tsNodeVisit(node: ts.Node, fn: (node: ts.Node) => unknown) {
  ts.forEachChild(node, (node) => {
    fn(node);
    tsNodeVisit(node, fn);
  });
}
```

## json

- `packages/angular_devkit/core/src/json/parser.ts:920`
- ng 推荐使用`jsonc-parser`

```ts
let parseTreeResult = parseTree(jsonString, parseError);
```

## html

- `@angular/compiler`

```ts
let parser = new HtmlParser();
let parseTreeResult = parser.parse(htmlString, '');
```

## 使用封装好的库

### cyia-code-util

- `https://github.com/wszgrcy/cyia-code-util`
- 同时支持`html`,`ts`,`json`
- 查询,修改,更新


## 视频
{% include video.html queryParams="aid=293224288&bvid=BV1Uf4y1E7Ya&cid=419172207&page=1" %}

