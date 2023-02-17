# Installation

To get started with **dnd kit**, install the core library via `npm` or `yarn`:  

```
npm install @dnd-kit/core
```

You'll also need to be make sure you have **dnd kit**'s peer dependencies installed. Chances are you already have `react` and `react-dom` installed in your project, but if not, make sure to ins:

```bash
npm install react react-dom
```

## Sub-packages

{% hint style="info" %}
 **dnd kit** is a [monorepo](https://en.wikipedia.org/wiki/Monorepo). Depending on your needs, you may also want to install other  sub-packages that are available under the `@dnd-kit` namespace.
{% endhint %}

为了保持库的核心较小，***dnd kit**仅附带核心构建块，大多数用户将在大部分时间内使用这些构建块来构建拖放体验：

* [Context provider](../api-documentation/context-provider/)
* Hooks for: 
  * [Draggable](../api-documentation/draggable/)
  * [Droppable](../api-documentation/droppable/)
* Sensors for:
  *  [Mouse](../api-documentation/sensors/mouse.md)
  * [Touch](../api-documentation/sensors/touch.md)
  * [Keyboard](../api-documentation/sensors/keyboard.md)
* Accessibility features

If you don't need any other features, you can skip right ahead to the [Quick start](getting-started.md) guide. 

### [Sortable](../presets/sortable/)

`@dnd-kit/core`包提供了从头开始构建可排序接口所需的所有构建块，但幸运的是，您不需要这样做。

如果您计划构建可排序界面，我们强烈推荐您尝试`@dnd-kit/sortable`，这是一个构建在`@dnd-kit/sortable`之上的，针对构建丝滑、灵活、可访问的可排序界面进行了优化。

```
npm install @dnd-kit/sortable
```

