 ![react-flow](https://user-images.githubusercontent.com/2857535/67417109-516ef000-f5c8-11e9-938a-c2121d3ebbe4.png)

# :ocean: React Flow

React Flow is a library for building node-based graphs. You can easily implement custom node types and it comes with plugins like a mini-map and graph controls. Feel free to check out the [examples](https://react-flow.netlify.com/) or read the [blog post](https://webkid.io/blog/react-flow-node-based-graph-library/) to get started.

- [Key Features](#key-features)
- [Installation](#installation)
- [Usage](#usage)
- [ReactFlow Component Props](#reactflow-component-props)
- [Nodes](#nodes)
  - [Props](#props-1)
  - [Node Types / Custom Nodes](#node-types--custom-nodes)
- [Edges](#nodes)
  - [Props](#props-2)
  - [Edge Types / Custom Edges](#edge-types--custom-edges)
- [Helper Functions](#helper-functions)
- [Plugins](#plugins)
  - [Minimap](#minimap)
  - [Controls](#controls)
- [Examples](#examples)
- [Development](#development)
- [Testing](#testing)

# Key Features

* **Easy to use:** Seamless zooming & panning behaviour and single and multi-selections of elements
* **Customizable:** Different [node](#node-types--custom-nodes) and [edge types](#edge-types--custom-edges) and support for custom nodes and edges
* **Fast rendering:** only elements that are in the view port are displayed
* **Utils:** Snap-to-grid, background styles and graph [helper functions](#helper-functions)
* **Plugin system:** [Mini map and graph controls](#plugins)
* **Reliable**: Written in [Typescript](https://www.typescriptlang.org/) and tested with [cypress](https://www.cypress.io/)

In order to make this library as flexible as possible we don’t do any state updates besides the positions. This means that you need to pass all functions that change the displayed nodes and edges by yourself. You can implement your own ones or use the helper functions that come with the library. 

## Installation

```
npm install react-flow-renderer
```

## Usage

This is a very basic example of how to use react-flow. There are more advanced examples in the [example](/example/src) folder.

```javascript
import React from 'react';
import ReactFlow from 'react-flow-renderer';

const elements = [
  { id: '1', data: { label: 'Node 1' }, position: { x: 250, y: 5 } },
  { id: '2', data: { label: 'Node 2' }, position: { x: 100, y: 100 } },
  { id: 'e1-2', source: '1', target: '2', animated: true },
];

const graphStyles = { width: '100%', height: '100%' };

const BasicGraph = () => (
  <ReactFlow elements={elements} style={graphStyles} />
);
```

## ReactFlow Component Props

- `elements`: array of [nodes](#nodes) and [edges](#edges) *(required)*
- `onElementClick`: element click handler
- `onElementsRemove`: element remove handler
- `onNodeDragStop`: node drag stop handler
- `onConnect`: connect handler
- `onLoad`: editor load handler
- `onMove`: move handler
- `nodeTypes`: object with [node types](#node-types--custom-nodes)
- `edgeTypes`: object with [edge types](#edge-types--custom-edges)
- `style`: css style passed to the wrapper
- `connectionLineType`: connection line type = `straight` or `bezier`
- `connectionLineStyle`: connection style as svg attributes
- `deleteKeyCode`: default: `8` (delete)
- `selectionKeyCode`: default: `16` (shift)
- `showBackground`: default: `true`
- `backgroundGap`: gap size - default: `16`
- `backgroundColor`: color of dots or lines - default: `#eee`
- `backgroundType`: background type = `dots` or `lines` - default: `dots`
- `snapToGrid`: default: `false`
- `snapGrid`: [x, y] array - default: `[16, 16]`
- `onlyRenderVisibleNodes`: default: `true`
- `isInteractive`: default: `true`. If the graph is not interactive you can't drag any nodes

## Nodes

There are three different [node types](#node-types--custom-nodes) (`default`, `input`, `output`) you can use. You can also create [custom nodes](#node-types--custom-nodes).

Node example: `{ id: '1', type: 'input', data: { label: 'Node 1' }, position: { x: 250, y: 5 } }`

### Props

- `id`: string *(required)*
- `position`: { x: number, y: number } *(required)*
- `data`: {} *(required if you are using a standard type, otherwise depends on your implementation)*
- `type`: 'input' | 'output' | 'default' or a custom one you implemented
- `style`: css properties
- `targetPosition`: 'left' | 'right' | 'top' | 'bottom' handle position - default: 'top'
- `sourcePosition`: 'left' | 'right' | 'top' | 'bottom' handle position - default: 'bottom'

### Node Types / Custom Nodes

The standard node types are `input`, `default` and `output`. The default node types object looks like this:

```javascript
{
  input: InputNode,
  default: DefaultNode,
  output: OutputNode
}
```

The keys represent the type names and the values are the node components that gets rendered.
If you want to introduce a new node type you can pass a `nodeTypes` object to the React Flow component:

```javascript
nodeTypes={{
  special: MyCustomNode
}}
```

You can now use type `special` for a node.
The `default`, `input` and `output` types will be still available except you overwrite one of them.
You can find an example of how to implement a custom node in the [custom node example](example/src/CustomNode).

## Edges

There are three [edge types](#edge-types--custom-edges) (`straight`, `default`, `step`) you can use. The default type is `default`. You can also create [custom edges](#edge-types--custom-edges).

Edge example: `{ id: 'e1-2', type: 'straight', source: '1', target: '2', animated: true }`

### Props

- `id`: string *(required)*
- `source`: string (an id of a node) *(required)*
- `target`: string (an id of a node) *(required)*
- `type`: 'input' | 'output' | 'default' or a custom one you implemented
- `animated`: boolean
- `style`: css properties

### Edge Types / Custom Edges

The basic edge types are `straight`, `default` and `step`. The default `edgeTypes` object looks like this:

```javascript
{
  default: BezierEdge,
  straight: StraightEdge,
  step: StepEdge
}
```

The keys represents the type names and the values are the edge components.
If you want to introduce a new edge type you can pass an `edgeTypes` object to the React Flow component:

```javascript
edgeTypes={{
  special: MyCustomEdge
}}
```

You can now use type `special` for an edge.
The `straight`, `default` and `step` types will be still available except you overwrite one of them.

## Helper Functions

If you want to remove a node or connect two nodes with each other you need to pass a function to `onElementsRemove` or `onConnect`. In order to simplify this process we are exporting some helper functions so that you don't need to implement them by yourself:

```javascript
import ReactFlow, { isNode, isEdge, removeElements, addEdge } from 'react-flow-renderer';
```

#### isEdge

`isEdge = (element: Node | Edge): boolean`

#### isNode

`isNode = (element: Node | Edge): boolean`

#### removeElements

`removeElements = (elementsToRemove: Elements, elements: Elements): Elements`

#### addEdge

`addEdge = (edgeParams: Edge, elements: Elements): Elements`

You can use these function as seen in [this example](/example/src/Rich/index.js#L40-L41) or implement them by yourself to update your elements.

## Plugins

### MiniMap

You can use the MiniMap plugin by passing it as a children to the React Flow component:

```javascript
import ReactFlow, { MiniMap } from 'react-flow-renderer';

const GraphWithMiniMap = () => (
  <ReactFlow
    elements={elements}
  >
    <MiniMap />
  </ReactFlow>
);
```

#### Props

- `nodeColor`: string | function - if you pass a color as a string all nodes will get that color. If you pass a function you can return a color depending on the node.
- `nodeBorderRadius`: number
- `maskColor`: string
- `style`: css properties
- `className`: class name

### Controls

The control panel contains a zoom-in, zoom-out and a fit-view button. You can use it by passing it as a children to your React Flow component:

```javascript
import ReactFlow, { Controls } from 'react-flow-renderer';

const GraphWithControls = () => (
  <ReactFlow
    elements={elements}
  >
    <Controls />
  </ReactFlow>
);
```

#### Props

- `style`: css properties
- `className`: class name

## Examples

You can find all examples in the [example](example) folder or check out the live versions:

- [rich](https://react-flow.netlify.app/)
- [basic](https://react-flow.netlify.app/basic)
- [custom node](https://react-flow.netlify.app/custom-node)
- [stress](https://react-flow.netlify.app/stress)
- [empty](https://react-flow.netlify.app/empty)
- [inactive](https://react-flow.netlify.app/inactive)

## Development

If you want to contribute or develop some custom features the easiest way is to start the dev server:

```
npm run dev
```

This serves the content of the `examples` folder and watches changes inside the `src` folder. The examples are using the source of the `src` folder.

## Testing

Testing is done with cypress. You can find all test in the [`integration/flow`](/cypress/integration/flow) folder. In order to run the test do:

```
npm run test
```


## Thanks!

Special thanks to [Andy Lindemann](https://github.com/AndyLnd) for a lot of helpful contributions!
