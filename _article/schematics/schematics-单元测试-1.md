---
layout: post
index: 70
tags: 原理图
---

# 7.Schematics 单元测试 1

## jasmine

- 执行命令`ts-node -P ./tsconfig.spec.json ./node_modules/jasmine/bin/jasmine --config=jasmine.json`
- 无测试覆盖率,需要配合 karma+一堆插件实现
  > 单独覆盖率的话可以用 nyc
- ng 指定

### 依赖包

- jasmine
- @types/jasmine
- ts-node

## jest

- 执行命令`jest`
- 初始化命令`jest init`
- ts 配置友好
- 带测试覆盖率
- 自带兼容 jasmine 策略

  > testRunner

- 运行速度稍慢

### 依赖包

- jest
- @types/jest
- ts-jest
- ts-node

## 测试方法(jasmine,jest 相同)

```ts
// 执行原理图首先需要指定collection,然后再运行某个原理图
describe('unit-demo', () => {
  it('运行内部原理图', async () => {
    let runner = new SchematicTestRunner('test', require.resolve('../collection.json'));
    // 这里
    let tree = await runner.runSchematicAsync('unit-demo').toPromise();
    expect(tree).toBeTruthy();
    expect(tree.exists('/1.log')).toBeTruthy();
    expect(tree.readContent('/1.log')).toEqual('111');
  });
  it('运行外部原理图', async () => {
    let runner = new SchematicTestRunner('test', require.resolve('../collection.json'));
    let tree = await runner.runExternalSchematicAsync('@angular-devkit/schematics-cli', 'blank', { name: 'unit-name' }).toPromise();
    expect(tree).toBeTruthy();
    // 这里
    tree = await runner.runSchematicAsync('unit-demo', undefined, tree).toPromise();
    expect(tree.readContent('/unit-name/package.json')).toEqual(`{"change":true}`);
  });
  it('运行runner规则', async () => {
    let runner = new SchematicTestRunner('test', require.resolve('../collection.json'));
    let tree: Tree = await runner.runExternalSchematicAsync('@angular-devkit/schematics-cli', 'blank', { name: 'unit-name' }).toPromise();
    // 这里
    tree = await runner.callRule(rule1, tree).toPromise();
    expect(tree.read('/unit-name/package.json')?.toString()).toEqual(`{"change":true}`);
  });
  it('单独调用规则', async () => {
    let runner = new SchematicTestRunner('test', require.resolve('../collection.json'));
    let tree: Tree = await runner.runExternalSchematicAsync('@angular-devkit/schematics-cli', 'blank', { name: 'unit-name' }).toPromise();
    let context = runner.engine.createContext(
      runner.engine.createSchematic('unit-demo', runner.engine.createCollection(require.resolve('../collection.json')))
    );
    // 这里
    tree = await callRule(rule1, tree, context).toPromise();
  });
});
```

### 初始化

- 传入集合名(随意),集合路径(绝对)

```ts
new SchematicTestRunner('', '');
```

### 调用外部原理图

```ts
runner.runExternalSchematicAsync('@schematics/angular', 'workspace', {}, this.hostTree);
```

| 外部集合名 | schematic 名 | 选项 | 树实例 |
| ---------- | ------------ | ---- | ------ |
|            |              | 可选 | 可选   |

### 调用内部原理图

- 即初始化 runner 传入的原理图集合中的

```ts
runner.runSchematicAsync(schematicName, options, this.tree);
```

| schematic 名 | 选项 | 树   |
| ------------ | ---- | ---- |
|              | 可选 | 可选 |

### 创建上下文

```ts
let context = runner.engine.createContext(
  runner.engine.createSchematic('ng-new', runner.engine.createCollection(require.resolve('../schematics/collection.json')))
);
```

### 规则调用

- 直接测试单一规则

```ts
callRule(Rule({}), tree, context);
```

| 规则 | 树  | 上下文 |
| ---- | --- | ------ |

- 已有 runner 测试

```ts
runner.callRule(rule, tree, parentContext);
```

| 规则 | 树  | 父级上下文 |
| ---- | --- | ---------- |
|      |     | 可选       |


## 视频
{% include video.html queryParams="aid=463352483&bvid=BV1iL411s7MG&cid=419081790&page=1" %}

