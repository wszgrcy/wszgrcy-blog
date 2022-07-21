---
layout: post
index: 160
tags: typescript typescript-compiler
title: Typescript-Compiler-API入门3
---

## CompilerHost

- 入门 2 中讲的 Host 是用来读取解析配置文件的,而这个是编译文件时读取具体文件用的
- 虽然两者有很多共通之处,部分方法其实可以一起使用一个,但是设计的时候还是分开了

```ts
let host = ts.createCompilerHost(config.options);
host.readFile = (filePath: string) => {
  return tree.read(filePath)?.toString();
};
host.fileExists = (filePath) => {
  return tree.exists(filePath);
};
host.directoryExists = (filePath: string) => {
  if (filePath.includes('node_modules')) {
    return false;
  }
  try {
    let dir = tree.getDir(filePath);
    return !!dir.subdirs.length || !!dir.subfiles.length;
  } catch (error) {
    return false;
  }
};
host.getCanonicalFileName = (filePath) => filePath;
host.getCurrentDirectory = () => '/';
let program = ts.createProgram({ rootNames: config.fileNames, options: config.options, host: host });
//读取文件
let list = program
  .getSourceFiles()
  .filter((sf) => !sf.isDeclarationFile)
  .filter((sf) => !sf.fileName.includes('node_modules'));
list;
let sf = program.getSourceFile('test.ts')!;
let statement = sf?.statements[1]!;
console.log(ts.SyntaxKind[statement.kind]);
```

## 视频

{% include video.html queryParams="aid=718392160&bvid=BV1FQ4y1X7Ne&cid=420542949&page=1" %}
