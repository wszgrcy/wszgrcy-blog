---
layout: post
index: 110
tags: 原理图
---

# 11.Schematics 交互 2(动态交互)

- 自带的根据`schema.json`的交互系统只能在调用时提前传入参数进行交互,无法处理在代码执行过程中,遇到问题应该怎么处理

## schema.CoreSchemaRegistry

- 用于验证交互提示是否有问题
- 初始化后调用`usePromptProvider`,传入一个函数,返回值为输入值(异步)
  > 因为是异步返回,所以可以使用任意方式返回
- 通过`compile`来传验证+传入参数(使用上面的 usePromptProvider 传入函数),执行交互
- 通过`ajv`来传递验证信息,通过`inquirer`来进行交互(加载 ajv 的验证)

### addPreTransform

- 处理在验证前处理数据,进行转换

```ts
schemaRegistry.addPreTransform((value, pointer, schema) => {
  console.log(value, pointer, schema);
  if (pointer === '/string1') {
    return `5666`;
  }
  return value;
});
```

### addPostTransform

- 在验证后处理数据,进行转换

```ts
schemaRegistry.addPostTransform((value, pointer, schema) => {
  if (pointer === '/number1') {
    return 99999999;
  }
  return value;
});
```

### compile

- 编译验证,并收集需要交互的数据
- 然后通过交互设置使用值
- 最后 ajv 验证

```ts
import { Rule, SchematicContext, Tree } from '@angular-devkit/schematics';
import { schema } from '@angular-devkit/core';
import * as inquirer from 'inquirer';
import { map, switchMap, tap } from 'rxjs/operators';
export default function (options: HintType): Rule {
  return (tree: Tree, context: SchematicContext) => {
    let schemaRegistry = new schema.CoreSchemaRegistry();
    schemaRegistry.usePromptProvider((definitions) => {
      const questions: inquirer.QuestionCollection = definitions.map((definition) => {
        const question: inquirer.Question = {
          name: definition.id,
          message: definition.message,
          default: definition.default,
        };

        const validator = definition.validator;
        if (validator) {
          question.validate = (input) => validator(input);
        }

        switch (definition.type) {
          case 'confirmation':
            return { ...question, type: 'confirm' };
          case 'list':
            return {
              ...question,
              type: definition.multiselect ? 'checkbox' : 'list',
              choices:
                definition.items &&
                definition.items.map((item) => {
                  if (typeof item == 'string') {
                    return item;
                  } else {
                    return {
                      name: item.label,
                      value: item.value,
                    };
                  }
                }),
            };
          default:
            return { ...question, type: definition.type };
        }
      });
      return inquirer.prompt(questions);
    });

    return schemaRegistry
      .compile({
        $schema: 'http://json-schema.org/draft-07/schema',
        $id: 'custom://a/b.json',
        properties: {
          test: {
            type: 'boolean',
            'x-prompt': {
              type: 'confirmation',
              message: '确认?',
            },
          },
          // 默认的`schema.json`无法实现读取某一个文件夹内的文件供选择
          'multi-value': {
            type: 'string',
            description: 'string类型',
            'x-prompt': {
              type: 'list',
              items: tree.getDir('/').subfiles.map((item) => ({ label: item, value: item })),
              message: '选择数组',
            },
          },
        },
      })
      .pipe(
        switchMap((validator) => validator({})),
        tap((options) => {
          console.log('参数', options);
        }),
        map(() => tree)
      );
  };
}
```

### addFormat

- 添加自定义格式
- `"format":"xxx"`中使用

```ts
schemaRegistry.addFormat({
  name: 'custom-xx',
  formatter: (value) => {
    return false;
  },
});
```

### addSmartDefaultProvider

- 默认字段处理

```ts
schemaRegistry.addSmartDefaultProvider('custom-default', () => '默认的');
```

### registerUriHandler

- 引用处理
- 注册了后就可以引用这个`json schema`的定义了

```ts
schemaRegistry.registerUriHandler((uri) => {
  console.log(uri);
  return of({
    $schema: 'http://json-schema.org/draft-07/schema',
    $id: 'XXX',
    properties: {},
    definitions: {
      base: {
        type: 'string',
        'x-prompt': '请输入string222',
      },
    },
  });
});
```

```json
{
  //...
  "custom": {
    "$ref": "./a.json#/definitions/base"
  }
}
```

### useXDeprecatedProvider

- 废弃字段提示

```ts
schemaRegistry.useXDeprecatedProvider((message) => {
  console.log(message);
  console.log('字段废弃了');
});
```


## 视频
{% include video.html queryParams="aid=420809727&bvid=BV1R3411y7Nz&cid=418995080&page=1" %}

