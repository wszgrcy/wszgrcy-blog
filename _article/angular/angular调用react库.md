---
layout: post
tags: Angular React
title: 在 Angular 中调用 React 库
---
# 在 Angular 中调用 React 库
- 在Angular中可以调用React库,但是,需要手动插入,所有写法全部是以React为主,Angular中只负责传入数据,这样写的很麻烦也很不优雅
- 这样不妨使用 ngx-bridge, 实现与 react 一样的节点结构,在ng组件中使用react组件或在react组件中调用ng组件
## ngx-bridge 简介
- 在Angular开发中,为了方便开发者调用任意React库而设计
- 为了使任意React库都可以像ng组件一样调用,减少开发心智
- 在Angular开发中使开发使用库时多一种选择

## 演示地址
- [https://wszgrcy.github.io/cyia-bridge/](https://wszgrcy.github.io/cyia-bridge/)

## npm包
- [https://www.npmjs.com/package/@cyia/ngx-bridge](https://www.npmjs.com/package/@cyia/ngx-bridge)
## 目前支持调用
- ng组件中直接调用react组件


```html
<react-outlet [component]="xxxx" [root]="true" #root></react-outlet>
```

- react组件中直接调用ng组件

```tsx
<NgOutlet component={OutletRefTestComponent}></NgOutlet>
```

- react组件中的函数组件/节点可以使用ng组件

```ts
componentWrapper(xxxx, {}).reactFunctionComponent
componentWrapper(xxxx, {}).reactElement
```

- ng调用react组件时,children可以为react组件,也可以是ng组件(投影)

```html
<!-- 直接子组件,也就是react-outlet的子级 -->
<react-outlet [component]="xxxx" #child></react-outlet>
<!-- 非直接子组件,父级不是react-outlet,但是父级的?级有react-outlet -->
<react-outlet [component]="xxxx" [parent]="root"></react-outlet>

<xxx [parent]="root"></xxx>
```

## 已测试库
- `ngx-bridge` 在开发时还在以下库中进行了测试,均可正常执行
> 理论上支持所有react库,但是无法一一测试,只是挑选了一些 star 比较高的库进行测试
- reactflow
- slate
- @tiptap
- react-hot-toast
- @react-pdf
- antd
- react-icons
- react-spinners
- react-dnd
- react-hook-form

