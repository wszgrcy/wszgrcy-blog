---
layout: post
tags: Angular React
title: 在 Angular 中调用 React 库
---
# Call React library in Angular
- In Angular, the React library can be called, but manual insertion is required. All writing methods are mainly based on React, and Angular is only responsible for passing in data, which is very cumbersome and not elegant to write
- In this way, we can use `ngx-bridge` to implement the same node structure as react, and use the react component in the ng component or call the ng component in the react component

## ngx-bridge introduction
- In Angular development, designed for the convenience of developers calling any React library
- In order to make any React library callable like ng components and reduce development mindset
- Provide an additional option for developers to use libraries in Angular development

## demo
- [https://wszgrcy.github.io/cyia-bridge/](https://wszgrcy.github.io/cyia-bridge/)

## npm
- [https://www.npmjs.com/package/@cyia/ngx-bridge](https://www.npmjs.com/package/@cyia/ngx-bridge)

## Currently supports calling
- Directly calling the react component in the ng component


```html
<react-outlet [component]="xxxx" [root]="true" #root></react-outlet>
```

- Directly calling the ng component in the react component

```tsx
<NgOutletReact component={OutletRefTestComponent}></NgOutletReact>
```

- The function components/nodes in the react component can use the ng component

```ts
wrapperToReact(xxxx, {}).reactFunctionComponent
wrapperToReact(xxxx, {}).reactElement
```

- When ng calls the react component, children can be either the react component or the ng component (projection)

```html
<!-- 直接子组件,也就是react-outlet的子级 -->
<react-outlet [component]="xxxx" #child></react-outlet>
<!-- 非直接子组件,父级不是react-outlet,但是父级的?级有react-outlet -->
<react-outlet [component]="xxxx" [parent]="root"></react-outlet>

<xxx [parent]="root"></xxx>
```

## illustrate
- `react-flow`[example](https://reactflow.dev/learn/layouting/layouting#d3-hierarchy)
- react

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

- Angular

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

- We can see that the node part is completely separated from the HTML template and has the same structure as the example. The logical part of the function component can be directly copied and passed into the `runInReact` attribute. We only need to write the export related methods in the `output` of the returned object, which can be called in the ng environment

## Tested Library

- `ngx-bridge` During development, testing was also conducted in the following libraries, and they can all be executed normally
> In theory, it supports all React libraries, but cannot be tested one by one. We only selected some libraries with higher star values for testing
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

