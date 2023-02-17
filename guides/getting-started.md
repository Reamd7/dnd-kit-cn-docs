---
description: >-
  急于开始吗？本快速入门指南将帮助您熟悉
  让您了解dnd-kit的核心概念。
---

# Quick start

{% hint style="info" %}
在开始之前，请确保您已按照[安装指南](installation.md)中概述的安装步骤进行操作。
{% endhint %}

### Context provider

首先，我们将设置应用程序的一般结构。为了让[`useDraggable`]()和[`useDroppable`]()钩子正常工作，您需要确保使用它们的组件包装在[`<DndContext/>`](../api-Documentation/Context-Provider/)组件中：

{% tabs %}
{% tab title="App.jsx" %}
```jsx
import React from 'react';
import {DndContext} from '@dnd-kit/core';

function App() {
  return (
    <DndContext>
      <Draggable />
      <Droppable />
    </DndContext>
  )
}
```
{% endtab %}
{% endtabs %}

### Droppable

![](../.gitbook/assets/droppable-large.svg)

接下来，让我们设置您的第一个**Droppable**组件。为此，我们将使用`useDroppable`挂钩。
  
`useDroppable`挂钩对你的应用程序应该如何构建并不关心。至少，它们要求您传递一个对DOM元素的引用，您希望该元素成为 droppable 。您还需要为所有 droppable 的组件提供唯一的`id`属性。

当**Draggable**项位于Droppable元素上方时，`isOver`属性将变为 true

{% tabs %}
{% tab title="Droppable.jsx" %}
```jsx
import React from 'react';
import {useDroppable} from '@dnd-kit/core';

function Droppable(props) {
  const {isOver, setNodeRef} = useDroppable({
    id: 'droppable',
  });
  const style = {
    color: isOver ? 'green' : undefined,
  };
  
  
  return (
    <div ref={setNodeRef} style={style}>
      {props.children}
    </div>
  );
}
```
{% endtab %}
{% endtabs %}

正如您所看到的，只需几行代码就可以将现有组件转换为可拖放的容器。

### Draggable

![](../.gitbook/assets/draggable-large.svg)

最后，让我们来看看如何实现我们的第一个**可拖动**组件。为此，我们将使用`useDraggable`挂钩。

`useDraggable`挂钩对于应用程序应该如何构建并不关心。至少，它要求您能够将侦听器listeners和引用附加到您希望成为可拖动的DOM元素。您还需要为所有可拖动组件提供唯一的`id`属性。

当一个项目开始被拖动后，您需要在屏幕上移动该项目所需的`Translate`坐标将填充到`Transform`属性中。对象符合以下形状：`{x：number，y：number，scaleX：number，scaleY：number}`

{% tabs %}
{% tab title="Draggable.jsx" %}
```jsx
import React from 'react';
import {useDraggable} from '@dnd-kit/core';

function Draggable(props) {
  const {attributes, listeners, setNodeRef, transform} = useDraggable({
    id: 'draggable',
  });
  const style = transform ? {
    transform: `translate3d(${transform.x}px, ${transform.y}px, 0)`,
  } : undefined;

  
  return (
    <button ref={setNodeRef} style={style} {...listeners} {...attributes}>
      {props.children}
    </button>
  );
}
```
{% endtab %}
{% endtabs %}

正如您从上面的示例中看到的，只需几行代码就可以将现有组件转换为可拖动的组件。

{% hint style="success" %}
**提示：**

*出于性能考虑，我们建议您优先使用**`Transform`**，而不是其他位置的CSS属性来移动被拖动的元素。

*您可能希望更改可拖动组件的**`z-index`**，以确保它出现在其他元素的顶部。

*如果您的项目需要从一个容器移动到另一个容器，我们建议您使用[`<DraggableClone>`](../api-documentation/draggable/clone.md)组件，这样项目就不会受到其父项目的堆叠上下文和溢出约束的限制。

{% endhint %}

将`transform`对象转换为字符串可能会让人感觉很乏味。不用担心，您可以通过从`@dnd-kit/utilities`包中导入`CSS`工具函数来避免手动操作：

```jsx
import {CSS} from '@dnd-kit/utilities';

// Within your component that receives `transform` from `useDraggable`:
const style = {
  transform: CSS.Translate.toString(transform),
}
```

### 把所有的碎片组装起来

一旦您设置了 **Droppable** 和 **Draggable** 组件，您会希望返回到设置[`<DndContext>`](../api-documentation/context-provider/)组件的位置，这样您就可以添加事件侦听器，以便能够响应触发的不同事件。

在本例中，我们假设您希望将`<Draggable>`组件从外部移到`<Droppable>`组件中：

![](../.gitbook/sets/example.png)

为此，您需要监听`<DndContext>`的`onDragEnd`事件，以查看您的draggable项是否落在了您的droppable项上：

{% tabs %}
{% tab title="App.jsx" %}
```jsx
import React from 'react';
import {DndContext} from '@dnd-kit/core';

import {Droppable} from './Droppable';
import {Draggable} from './Draggable';

function App() {
  const [isDropped, setIsDropped] = useState(false);
  const draggableMarkup = (
    <Draggable>Drag me</Draggable>
  );
  
  return (
    <DndContext onDragEnd={handleDragEnd}>
      {!isDropped ? draggableMarkup : null}
      <Droppable>
        {isDropped ? draggableMarkup : 'Drop here'}
      </Droppable>
    </DndContext>
  );
  
  function handleDragEnd(event) {
    if (event.over && event.over.id === 'droppable') {
      setIsDropped(true);
    }
  }
}
```
{% endtab %}

{% tab title="Droppable.jsx" %}
```jsx
import {useDroppable} from '@dnd-kit/core';

export function Droppable(props) {
  const {isOver, setNodeRef} = useDroppable({
    id: 'droppable',
  });
  const style = {
    color: isOver ? 'green' : undefined,
  };
  
  
  return (
    <div ref={setNodeRef} style={style}>
      {props.children}
    </div>
  );
}
```
{% endtab %}

{% tab title="Draggable.jsx" %}
```jsx
import React from 'react';
import {useDraggable} from '@dnd-kit/core';

export function Draggable(props) {
  const {attributes, listeners, setNodeRef, transform} = useDraggable({
    id: 'draggable',
  });
  const style = transform ? {
    transform: `translate3d(${transform.x}px, ${transform.y}px, 0)`,
  } : undefined;

  
  return (
    <button ref={setNodeRef} style={style} {...listeners} {...attributes}>
      {props.children}
    </button>
  );
}
```
{% endtab %}
{% endtabs %}

就这样!您已经设置了第一个[**Droppable**](../api-documentation/droppable/)*和[**Draggable**](../api-documentation/draggable/)组件。

### 再往前推一点

我们上面设置的例子有点简单化。在现实世界中，您可能有多个可放入的容器，并且您可能还希望能够在项目被拖入可放入的容器中后将其拖出。

下面是一个稍微复杂一点的示例，它包含多个**Droppable**容器：

```jsx
import React from 'react';
import {DndContext} from '@dnd-kit/core';

import {Droppable} from './Droppable';
import {Draggable} from './Draggable';

function App() {
  const containers = ['A', 'B', 'C'];
  const [parent, setParent] = useState(null);
  const draggableMarkup = (
    <Draggable>Drag me</Draggable>
  );

  return (
    <DndContext onDragEnd={handleDragEnd}>
      {parent === null ? draggableMarkup : null}

      {containers.map((id) => (
        // We updated the Droppable component so it would accept an `id`
        // prop and pass it to `useDroppable`
        <Droppable key={id} id={id}>
          {parent === id ? draggableMarkup : 'Drop here'}
        </Droppable>
      ))}
    </DndContext>
  );

  function handleDragEnd(event) {
    const {over} = event;

    // If the item is dropped over a container, set it as the parent
    // otherwise reset the parent to `null`
    setParent(over ? over.id : null);
  }
};
```

我们希望本快速入门指南能让您对**dnd ki**的简单性和强大功能有一个初步了解。还有更多的东西需要学习，我们鼓励您继续阅读下一节中可以传递给`<DndContext>`的所有不同选项。

