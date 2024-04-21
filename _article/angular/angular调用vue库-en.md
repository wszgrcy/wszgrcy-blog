---
layout: post
index: 320
tags: Angular VUE
title: Call Vue library in Angular
---

# Call Vue library in Angular
- Previously implemented the call to the [React library](https://wszgrcy.github.io/wszgrcy-blog/article/angular/angular%E8%B0%83%E7%94%A8react%E5%BA%93.html)Currently, Vue library calls are supported
- React and Vue have similarities in some designs, so their implementations and calls are basically the same

## demo

- [https://wszgrcy.github.io/cyia-bridge/](https://wszgrcy.github.io/cyia-bridge/)

## npm 

- [https://www.npmjs.com/package/@cyia/ngx-bridge](https://www.npmjs.com/package/@cyia/ngx-bridge)

## Currently supports calling

- Directly calling the vue component in the ng component

```html
<vue-outlet [component]="xxxx" [root]="true" #root></vue-outlet>
```

- Directly calling the ng component in the vue component

```tsx
<NgOutletVue component={OutletRefTestComponent}></NgOutletVue>
```

- When ng calls the Vue component, children can be either the Vue component or the ng component (projection)

```html
<vue-outlet [component]="xxxx" #child></vue-outlet>
<!-- named slot -->
<vue-slot-group [name]="'xxx'">
  <ng-template #child let-input1="input1"></ng-template>
</vue-slot-group>
```

## example

- `ant-design-vue` 


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
