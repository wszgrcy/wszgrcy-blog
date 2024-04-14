---
layout: post
index: 320
tags: Angular VUE
title: 在 Angular 中调用 Vue 库
---

# 在 Angular 中调用 Vue 库

- 之前实现了[React 库的调用](https://wszgrcy.github.io/wszgrcy-blog/article/angular/angular%E8%B0%83%E7%94%A8react%E5%BA%93.html),目前已经支持 Vue 库调用
- React 与 Vue 在部分设计上存在相似性,因此实现基本一样,调用基本一样

## 演示地址

- [https://wszgrcy.github.io/cyia-bridge/](https://wszgrcy.github.io/cyia-bridge/)

## npm 包

- [https://www.npmjs.com/package/@cyia/ngx-bridge](https://www.npmjs.com/package/@cyia/ngx-bridge)

## 目前支持调用

- ng 组件中直接调用 vue 组件

```html
<vue-outlet [component]="xxxx" [root]="true" #root></vue-outlet>
```

- vue 组件中直接调用 ng 组件

```tsx
<NgOutletVue component={OutletRefTestComponent}></NgOutletVue>
```

- ng 调用 vue 组件时,children 可以为 vue 组件,也可以是 ng 组件(投影)

```html
<vue-outlet [component]="xxxx" #child></vue-outlet>
<!-- 具名slot -->
<vue-slot-group [name]="'xxx'">
  <ng-template #child let-input1="input1"></ng-template>
</vue-slot-group>
```

## 举例

- 以`ant-design-vue`为例

- ng 下

```html
<vue-outlet [component]="Tree" [root]="true" [(model)]="bindModel" [props]="props">
  <vue-slot-group [name]="'title'">
    <ng-template #child let-key="key" let-title="title">
      @if (key === '0-0-1-0') {
      <span style="color: #1890ff">{{ title }}</span>
      }@else{ {{ title }} }
    </ng-template>
  </vue-slot-group>
</vue-outlet>
```

```ts
import { Component } from '@angular/core';
import { VueOutlet, VueSlotGroup } from '@cyia/ngx-bridge/vue-outlet';
import { Button, Tree, TreeProps } from 'ant-design-vue';
const treeData: TreeProps['treeData'] = [
  {
    title: 'parent 1',
    key: '0-0',
    children: [
      {
        title: 'parent 1-0',
        key: '0-0-0',
        disabled: true,
        children: [
          { title: 'leaf', key: '0-0-0-0', disableCheckbox: true },
          { title: 'leaf', key: '0-0-0-1' },
        ],
      },
      {
        title: 'parent 1-1',
        key: '0-0-1',
        children: [{ key: '0-0-1-0', title: 'sss' }],
      },
    ],
  },
];
const expandedKeys = ['0-0-0', '0-0-1'];
const selectedKeys = ['0-0-0', '0-0-1'];
const checkedKeys = ['0-0-0', '0-0-1'];
@Component({
  selector: 'app-ant-design-vue-demo',
  standalone: true,
  imports: [VueOutlet, VueSlotGroup],
  templateUrl: './ant-design-vue-demo.component.html',
  styleUrl: './ant-design-vue-demo.component.scss',
})
export class AntDesignVueDemoComponent {
  Tree = Tree;
  treeData = treeData;
  bindModel = { expandedKeys, selectedKeys, checkedKeys };
  props = {
    checkable: true,
    treeData: treeData,
  };
}
```
