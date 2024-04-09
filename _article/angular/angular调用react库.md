---
layout: post
index: 301
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
## 举例
- 以`react-flow`[官方例子](https://reactflow.dev/learn/layouting/layouting#d3-hierarchy)为例
- react下

```tsx
import { stratify, tree } from 'd3-hierarchy';
import React, { useCallback, useMemo } from 'react';
import ReactFlow, {
  ReactFlowProvider,
  Panel,
  useNodesState,
  useEdgesState,
  useReactFlow,
} from 'reactflow';

import { initialNodes, initialEdges } from './nodes-edges.js';
import 'reactflow/dist/style.css';

const g = tree();

const getLayoutedElements = (nodes, edges, options) => {
  if (nodes.length === 0) return { nodes, edges };

  const { width, height } = document
    .querySelector(`[data-id="${nodes[0].id}"]`)
    .getBoundingClientRect();
  const hierarchy = stratify()
    .id((node) => node.id)
    .parentId((node) => edges.find((edge) => edge.target === node.id)?.source);
  const root = hierarchy(nodes);
  const layout = g.nodeSize([width * 2, height * 2])(root);

  return {
    nodes: layout
      .descendants()
      .map((node) => ({ ...node.data, position: { x: node.x, y: node.y } })),
    edges,
  };
};

const LayoutFlow = () => {
  const { fitView } = useReactFlow();
  const [nodes, setNodes, onNodesChange] = useNodesState(initialNodes);
  const [edges, setEdges, onEdgesChange] = useEdgesState(initialEdges);

  const onLayout = useCallback(
    (direction) => {
      const { nodes: layoutedNodes, edges: layoutedEdges } = getLayoutedElements(nodes, edges, {
        direction,
      });

      setNodes([...layoutedNodes]);
      setEdges([...layoutedEdges]);

      window.requestAnimationFrame(() => {
        fitView();
      });
    },
    [nodes, edges]
  );

  return (
    <ReactFlow
      nodes={nodes}
      edges={edges}
      onNodesChange={onNodesChange}
      onEdgesChange={onEdgesChange}
      fitView
    >
      <Panel position="top-right">
        <button onClick={onLayout}>layout</button>
      </Panel>
    </ReactFlow>
  );
};

export default function () {
  return (
    <ReactFlowProvider>
      <LayoutFlow />
    </ReactFlowProvider>
  );
}
```

- ng下

```html
<react-outlet [component]="ReactFlowProvider" [root]="true">
  <react-outlet [component]="ReactFlow" #child #root [runInReact]="context">
    <react-outlet
      [component]="Panel"
      #child
      [props]="$any({ position: 'top-right' })"
    >
      <button #child (click)="root.output()?.['onLayout']('TB')">
        vertical layout
      </button>
      <button #child (click)="root.output()?.['onLayout']('LR')">
        horizontal layout
      </button>
    </react-outlet>
  </react-outlet>
</react-outlet>

```
```ts
import { Component } from '@angular/core';
import { ReactOutlet } from '@cyia/ngx-bridge';
import ReactFlow, {
  ReactFlowProvider,
  Panel,
  useReactFlow,
  useNodesState,
  useEdgesState,
} from 'reactflow';
import Dagre from '@dagrejs/dagre';
import { initialNodes, initialEdges } from './nodes-edges';
import { useCallback } from 'react';

const g = new Dagre.graphlib.Graph().setDefaultEdgeLabel(() => ({}));

const getLayoutedElements = (nodes: any, edges: any, options: any) => {
  g.setGraph({ rankdir: options.direction });

  edges.forEach((edge: any) => g.setEdge(edge.source, edge.target));
  nodes.forEach((node: any) => g.setNode(node.id, node));

  Dagre.layout(g);

  return {
    nodes: nodes.map((node: any) => {
      const { x, y } = g.node(node.id);

      return { ...node, position: { x, y } };
    }),
    edges,
  };
};
@Component({
  selector: 'app-custom-layout',
  standalone: true,
  imports: [ReactOutlet],
  templateUrl: './custom-layout.component.html',
  styleUrl: './custom-layout.component.scss',
  host: { style: 'display:block;height:400px' },
})
export class CustomLayoutComponent {
  ReactFlowProvider = ReactFlowProvider;
  ReactFlow = ReactFlow;
  Panel = Panel;
  context = (props: any, output: any) => {
    const { fitView } = useReactFlow();
    const [nodes, setNodes, onNodesChange] = useNodesState(initialNodes);
    const [edges, setEdges, onEdgesChange] = useEdgesState(initialEdges);
    const onLayout = useCallback(
      (direction: any) => {
        const layouted = getLayoutedElements(nodes, edges, { direction });
        setNodes([...layouted.nodes]);
        setEdges([...layouted.edges]);
        window.requestAnimationFrame(() => {
          fitView();
        });
      },
      [nodes, edges]
    );
    return {
      props: {
        nodes,
        edges,
        onNodesChange,
        onEdgesChange,
        fitView: true,
      },
      output: {
        onLayout,
      },
    };
  };
}

```

- 我们可以看到节点部分,完全分离到html模板中,并且结构完全与例子相同.而函数组件的逻辑部分,直接复制传入`runInReact`属性中就可以了,我们只需要在返回对象的`output`中写出导出相关方法,供给ng环境下调用
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

