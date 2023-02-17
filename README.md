---
description: >-
  @dnd-kit – A lightweight, modular, performant, accessible and  extensible drag
  & drop toolkit for React.
---

# Overview

* **Feature packed:** 可定制的碰撞侦测算法、多激活器、可拖动覆盖、拖动手柄、自动滚动、约束等等。
* **Built for React:** exposes hooks such as [`useDraggable`](api-documentation/draggable/usedraggable.md) and [`useDroppable`](api-documentation/droppable/usedroppable.md), and  won't require you to re-architect your app or create additional wrapper DOM nodes.
* **Supports a wide range of use cases:** 列表、网格、多个容器、嵌套上下文、可变大小的项目、虚拟列表、2D 游戏等。
* **Zero dependencies and modular:** 该库的核心重量缩小到10kb 左右，没有外部依赖关系。它是围绕内置的 React 状态管理和上下文构建的，这使得库保持精简。
* **Built-in support for multiple input methods:** 指针, 键盘鼠标, 触摸传感器。
* **Fully customizable & extensible:** 定制每一个细节 – animations, transitions, behaviours, styles. Build your own [sensors](api-documentation/sensors/), [collision detection algorithms](api-documentation/context-provider/collision-detection-algorithms.md), customize key bindings and so much more.
* **Accessibility:** Keyboard support, sensible default aria attributes, customizable screen reader instructions and live regions built-in.
* **Performance:** It was built with performance in mind in order to support silky smooth animations.
* **Presets:** Need to build a sortable interface? Check out [`@dnd-kit/sortable`](presets/sortable/), which is a thin layer built on top of `@dnd-kit/core`. More presets coming in the future.

![](.gitbook/assets/concepts-illustration-large.svg)

**dnd kit** 暴露出来两个核心概念:

* [Draggable elements](api-documentation/draggable/)
* [Droppable areas](api-documentation/droppable/)

使用 [`useDraggable`](api-documentation/draggable/usedraggable.md) and [`useDroppable`](api-documentation/droppable/usedroppable.md) hooks 增强现有组件, or combine both to create components that can both be dragged and dropped over.

使用 [`<DndContext>`](api-documentation/context-provider/)  provider 处理事件和自定义draggable elements and droppable areas的行为.  配置 [sensors](api-documentation/sensors/) to 处理不同的输入行为.

使用 [`<DragOverlay>`](api-documentation/draggable/drag-overlay.md) 组件 
渲染一个可拖动的层，该层从正常的文档流中移除，并且 relative 于视口定位。

看看我们的快速入门指南，了解如何开始:

{% content-ref url="introduction/getting-started.md" %}
[getting-started.md](introduction/getting-started.md)
{% endcontent-ref %}

### 可扩展性

可扩展性是 **dnd kit** 的核心。它的构建是精简和可扩展的。它提供了我们认为大多数人在大部分时间都想要的功能，并提供了扩展点，以便在 `@dnd-kit/core` 之上构建其余的功能。

**dnd kit**的可扩展性级别的最佳示例 is the[ Sortable preset](presets/sortable/), 使用`@dnd-kit/core`暴露的扩展点构建。

主要扩展点包括：

* [sensors](api-documentation/sensors/)
* [modifiers](api-documentation/modifiers.md)
* [自定义碰撞检测算法](api-documentation/context-provider/collision-detection-algorithms.md#custom-collision-detection-strategies)

### 无障碍

构建每个人都可以访问的拖放界面并非易事，需要深思熟虑。

`@dnd-kit/core`库提供了多个起点，帮助您实现拖放界面的可访问性：

* [键盘支持](api-documentation/sensors/keyboard.md)out of the box
* [可定制的屏幕阅读器说明](guides/accessibility.md#screen-reader-instructions) for how to interact with draggable items
* [可定制的实时区域更新](guides/accessibility.md#screen-reader-announcements-using-live-regions) to provide screen reader announcements in real-time of what is currently happening with draggable and droppable elements.
* [属性的合理缺省值](api-documentation/draggable/usedraggable.md#attributes) that should be passed to draggable elements

Check out our Accessibility guide to learn more about how you can help make your drag and drop interface accessible for everyone:

{% content-ref url="guides/accessibility.md" %}
[accessibility.md](guides/accessibility.md)
{% endcontent-ref %}

### Architecture

Unlike many drag and drop libraries, **dnd kit** is **** intentionally **not** built on top of the [HTML5 Drag and drop API](https://developer.mozilla.org/en-US/docs/Web/API/HTML\_Drag\_and\_Drop\_API). This was a deliberate architectural decision, that does come with tradeoffs that you should be aware of before deciding to use it. For most web applications, we believe the benefits outweigh the tradeoffs.&#x20;

The HTML5 Drag and drop API has some severe **limitations**. It does not support touch devices, which means that the libraries that are built on top of it need to expose an entirely different implementation to support touch devices. This typically increases the complexity of the codebase and the overall bundle size of the library. Further, it requires workarounds to implement common use cases such as customizing the drag preview, locking dragging to a specific axis or to the bounds of a container, or animating the dragged item as it is picked up.&#x20;

The main **tradeoff** with not using the HTML5 Drag and drop API is that you won't be able to drag from the desktop or between windows. If the drag and drop use-case you have in mind involves this kind of functionality, you'll definitely want to use a library that's built on top of the HTML 5 Drag and drop API. We highly recommend you check out [react-dnd](https://github.com/react-dnd/react-dnd/) for a React library that's has a native HTML 5 Drag and drop backend.

### Performance

#### **Minimizing DOM mutations**

**dnd kit** lets you build drag and drop interfaces without having to mutate the DOM every time an item needs to shift position.&#x20;

This is possible because **dnd kit** lazily calculates and stores the initial positions and client rects of your droppable containers when a drag operation is initiated. These positions are passed down to your components that use `useDraggable` and `useDroppable` so that you can compute the new positions of your items while a drag operation is underway, and move them to their new positions using performant CSS properties that do not trigger a repaint such as `translate3d` and `scale`. For an example of how this can be achieved, check out the implementation of the sorting strategies that are exposed by the [`@dnd-kit/sortable`](presets/sortable/) library.

This isn't to say that you can't shift the position of the items in the DOM while dragging, this is something that **is supported** and sometimes inevitable. In some cases, it won't be possible to know in advance what the new position and layout of the item until you move it in the DOM. Just know that these kind of mutations to the DOM while dragging are much more expensive and will cause a repaint, so if possible, prefer computing the new positions using `translate3d` and `scale`.

#### Synthetic events

Sensors use [SyntheticEvent listeners](https://reactjs.org/docs/events.html) for the activator events of all sensors, which leads to improved performance over manually adding event listeners to each individual draggable node.
