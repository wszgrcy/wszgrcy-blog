---
layout: post
index: 150
tags: typescript typescript-compiler
title: Typescript-Compiler-API入门2
---
## ParseConfigHost
- ts在使用时需要先读取tsconfig
- tsconfig里面有很多路径类型,比如`exclude`,`include`,`files`等,需要解析,这里的路径并不是真正的硬盘指向(虽然我们默认使用的时候如此),可以通过实现这个接口,在不同环境下成功解析
> tsconfig虽然是个json,但是还有一些继承关系等,里面还可以写注释(jsonc)所以不能粗暴的直接`JSON.parse`



```ts
// 定义tsconfig host
import { Tree } from '@angular-devkit/schematics/src/tree/interface';
import { ParseConfigHost } from 'typescript';
import minimatch from 'minimatch';
import { join, normalize } from '@angular-devkit/core';
export class SchematicsTsconfigHost implements ParseConfigHost {
  constructor(private tree: Tree) {}
  useCaseSensitiveFileNames = true;
  readDirectory(
    rootDir: string,
    extensions: readonly string[],
    excludes: readonly string[] | undefined,
    includes: readonly string[],
    depth?: number
  ) {
    return this._readDirectory(rootDir, extensions, excludes, includes, depth);
  }
  _readDirectory(
    rootDir: string,
    extensions: readonly string[],
    excludes: readonly string[] | undefined,
    includes: readonly string[],
    depth?: number
  ): string[] {
    let dir = this.tree.getDir(rootDir);
    let files = dir.subfiles as string[];

    return files
      .map((item) => join(normalize(rootDir), item) as string)
      .filter((file) => extensions.some((extension) => file.endsWith(extension)))
      .filter((file) => !excludes?.some((exclude) => minimatch(file, exclude)))
      .filter((file) => includes.some((include) => minimatch(file, include)))
      .concat(
        ...dir.subdirs
          .filter((item) => item !== 'node_modules')
          .map((subdir) => join(normalize(rootDir), subdir))
          .map((filepath) => this._readDirectory(filepath, extensions, excludes, includes))
          .reduce((pre, cur) => {
            pre.push(...cur);
            return pre;
          }, [])
      );
  }

  fileExists(filePath: string) {
    return this.tree.exists(filePath);
  }

  readFile(filePath: string) {
    return this.tree.read(filePath)?.toString();
  }
}

//调用,与1相同
let fileName = 'tsconfig.json';
let tsconfig = tree.read(fileName);
let jsonSourceFile = ts.parseJsonText(fileName, tsconfig!.toString());
// 使用自定义的,而不是ts.sys
let schematicsTsconfigHost = new SchematicsTsconfigHost(tree);
// 解析好的config
let config = ts.parseJsonSourceFileConfigFileContent(jsonSourceFile, schematicsTsconfigHost, '');

```

## 视频

{% include video.html queryParams="aid=378382374&bvid=BV1uf4y1c7zN&cid=420332033&page=1" %}
