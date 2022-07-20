---
layout: post
index: 40
tags: 原理图
---

# 4.Schematics 交互 1

## 前置知识(可选)

- [jsonschema](http://json-schema.org/) json 格式
  > 简单来讲就是规定 json 的格式(可以理解为 ts 中的类型声明)
- ajv 验证数据是否正确
  > 基于`json schema`判断数据是否正确
- inquirer 终端提示交互

## 流程

- 手写`json schema` 定义原理图的输入参数是哪些
- ajv 验证输入参数是否正确,同时会处理自定义字段定义的字段
  > 自定义字段在`compile`时进行处理,提取到变量中供`inquirer`使用(进行交互)
- inquirer 对那些声明要交互的字段进行交互

## 输入参数

- 文本
- 数字
- boolean
- 列表
- ...

```json
{
  "$schema": "http://json-schema.org/draft-07/schema",
  "$id": "HintType",
  "properties": {
    "string1": {
      "type": "string"
    },
    "number1": {
      "type": "number"
    },
    "boolean1": {
      "type": "boolean"
    }
  },
  "required": ["string1"]
}
```

## 额外字段

- 可以理解为非标准 Json Schema 中存在的,是原理图中自行约定的一些字段

### 默认值

- default 写什么是什么
- $default 命令行中去拿
  > `packages\angular_devkit\core\src\json\schema\registry.ts:416`

```json
     "$default": {
        "$source": "argv",
        "index": 0
      },
```

### x-prompt 交互提示

- `packages\angular_devkit\core\src\json\schema\registry.ts:457`
- 直接字符串或者对象

```json

  "number1": {
    //...
    "x-prompt": "请输入number类型",
    //...
    "x-prompt": {
      "type": "number",
      "message": "请输入number类型"
    }
  }

```

### x-deprecated

- 标记废弃
  > `packages\angular_devkit\core\src\json\schema\registry.ts:666`

```json

  "multi-value": {
    //...
    "type": "number",
    "x-deprecated": "字段废弃"
  }

```

## 验证

- ajv 主管对输入进来的格式进行验证,如果输入错误,在交互模式下,会进行循环验证,再非交互模式下(--xxx yyy)会抛出异常
- ajv 支持 jsonSchema 的验证格式
-

### 类型转换

- dtsgenerator
  > 根据定义好的 json schema 生成 d.ts,方便开发
  > 一般用在入口传参数的时候

### 格式化

- 使用`format`字段
- 输入字段是否符合某种格式
- `ajv-formats`中的参数
- ng 自定义`html-selector`,`path`
> `html-selector` 验证输入的是不是 html选择器,源码 `packages\angular_devkit\schematics\src\formats\html-selector.ts:46`
> `path` 验证输入的是不是一个标准路径, 源码`packages\angular_devkit\schematics\src\formats\path.ts`
```json

  "test-format": {
    "type": "string",
    "format": "html-selector",
  }
// 验证失败提示
// Data path "/test-format" must match format "html-selector".
```

## 其他

### required

- 标记必要字段(非交互模式下比较有用)

### visible

- 为`false`将在 help 中不可见,但是可以使用

### alias

- 别名(短名称)
- 使用方法`-x yyy`

## ng g xx:yy

### --help(独有)

- help 时会列出这个原理图中的一些配置项说明(告诉我们如何传参)
- help 时有 arguments 和 options
  > `packages\angular\cli\models\command.ts:79`

### arguments

- json 中使用$default 字段,指定参数从第几个传入参数中获取
- ng g xx:yy aa bb
  > 其中 aa bb 就是 arguments 可以不写健值对的方式

### options

- 需要`--xx yy`或`-x yy`这种方式指定健值对


## 视频
{% include video.html queryParams="aid=720701659&bvid=BV1UQ4y1z7iK&cid=419199689&page=1" %}

