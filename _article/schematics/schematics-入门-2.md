---
layout: post
index: 50
tags: 原理图
---

# 5.Schematics 入门 2(Tree)

## 文件操作

- 源码位置`packages\angular_devkit\schematics\src\tree\host-tree.ts`

### 查询

- actions
  > 每一个操作(添加,修改,删除,重命名等)就是一个 action,
- getDir 方法
- get 方法
- exists
- visit
  > 类似数组的 forEach
- read

### 创建

- create

### 修改

- overwrite
  > 文件存在,进行覆盖,文件不存在,抛出异常
- rename
  > 具有移动功能
- apply
  > 目前未实现
  > `packages\angular_devkit\schematics\src\tree\host-tree.ts:383`

#### 文件修改

- beginUpdate
  > 开始修改返回 record,相当于开了一个新分支,合并之前文件不会有任何改变
- commitUpdate
  > 将修改合并,可以理解为 git 的提交文件,当 commit 时,你的修改才会真正记录

```ts
let recorder = tree.beginUpdate('/file-action-folder/create-file.js');
recorder.insertRight(27, `;console.log('插入');`);
recorder.remove(0, 27);
tree.commitUpdate(recorder);
```

##### recorder

- insertRight
  > 右边不变,插到左边
- insertLeft
  > 左边不变,插到右边

#### 树修改

- branch
  > 与 git 的操作类似
- merge
  > 合并有各种策略

```ts
let tree2 = tree.branch();
tree2.create('/file-action-folder/create-file.js', `console.log("创建文件")`);
tree.create('/create-file.js', `console.log("创建文件")111`);
let source1 = url('./tempalte');
/** url传入tree */
let tree3 = source1(context) as Tree;
tree.merge(tree3, MergeStrategy.AllowCreationConflict);
```

##### 合并策略

- 要 merge 另一个 tree(上例`tree3`)出现冲突后怎么办(另一个树的修改是否允许)
- 如果要合并的树是子级(孙子级,重孙子级...),那么直接为覆盖操作
  > 简单理解的是`tree.branch()`出去的,都能无条件覆盖合并

```ts
export enum MergeStrategy {
  // 如果一个文件要被删除,而合并的树(`tree3`)是覆盖的,有了这个后就允许覆盖
  // 如果两个都是覆盖,有了这个后就使用这个覆盖
  AllowOverwriteConflict = 1 << 1,
  // 原:创建/覆写/存在 合并:创建 有:允许创建
  AllowCreationConflict = 1 << 2,
  // 原:不存在 合并:删除 有:允许删除
  AllowDeleteConflict = 1 << 3,
  // 默认,啥都没有
  // Uses the default strategy.
  Default = 0,

  // Error out if 2 files have the same path. It is useful to have a different value than
  // Default in this case as the tooling Default might differ.
  Error = 1 << 0,

  // Only content conflicts are overwritten.
  ContentOnly = AllowOverwriteConflict,
  // 三者全部起作用
  // Overwrite everything with the latest change.
  Overwrite = AllowOverwriteConflict + AllowCreationConflict + AllowDeleteConflict,
}
```

### 删除

- delete


## 视频
{% include video.html queryParams="aid=975746641&bvid=BV1k44y147AM&cid=419185364&page=1" %}

