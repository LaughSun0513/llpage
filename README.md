<h1 align="center">
  <br>
	<img width="320" src="media/logo.png" alt="llpage">
  <br>
  <br>
  <br>
</h1>

> 🚀 page operation model (in memory) with LRU & lifecycle strategy.

[![All Contributors](https://img.shields.io/badge/all_contributors-1-orange.svg?style=flat-square)](#contributors) ![Travis (.org) branch](https://img.shields.io/travis/qddegtya/llpage/master.svg?style=flat-square) ![Codecov](https://img.shields.io/codecov/c/github/qddegtya/llpage.svg?style=flat-square) ![npm](https://img.shields.io/npm/v/llpage.svg?style=flat-square)

![model](https://user-images.githubusercontent.com/773248/52936637-b8803900-3397-11e9-834f-f5bb76776663.png) 

# Motivation

大型前端微应用架构中，往往需要 `在某个特定容器 DOM 节点里频繁操作各种业务入口页面`。无论是否使用 `渐进增强策略` 来完善和优化用户体验（比如是否启用 `iframe`，亦或是进化到某种 `前端微应用架构`），我们都希望这些页面在运行时之下能够做到：

* 灵活感知页面生命周期，为什么要这么做？好处显而易见，这个诉求的场景也可以举出很多。比如当页面不可见时，我们可以建议开发者停止页面中大量耗时的轮询任务、在页面进入退出的时候进行用户行为追踪、制作转场动画等。当我们的页面运行在浏览器中，我们可以利用一些现有的能力来支撑，如果你对此感兴趣，WICG 有一项正在进行中的 spec [Lifecycle API for Web Pages](https://github.com/WICG/page-lifecycle) 也阐述了这样的一个观点，**但是当我们的页面跑在特定的自行研发的容器时，这个时候我们需要一个非常类似的内存操作模型**，本质上是通过对容器内页面植入生命周期，来让页面针对生命周期节点，及时对`资源`进行一些回收，达到让容器更加稳定的目的
* 页面管理容器可以灵活设置最大页面保活 (keep alive) 数量，**我们不应该让业务页面入口对应的挂载点无限制地增长**，而应该根据运行时的资源情况，妥善分配这个值
* `向微应用架构集成、迈进`，2016 年底，[Thoughtworks](https://www.thoughtworks.com/cn/radar) 在技术雷达上首次提及`前端微应用`的架构理念，将大型前端应用的`分治`诉求跟服务端常见的`微服务`思想结合在一起。解决了大型前端应用多人协同的工程效能问题，也为`micro-frontends`提供了一种可行的[架构思路](https://micro-frontends.org/)。除此之外，为了让微应用架构跑的更好、更快、更稳定，往往需要定制一个健壮的`运行时容器`，在容器之上长出微应用架构，它可能长下图这样，因此需要容器能够随时随地跟`微应用架构`进行集成：

  ![](https://user-images.githubusercontent.com/773248/53077083-337a5880-352c-11e9-9969-b50c529fb3e8.png)

* 向成熟的`容器`架构借鉴，比如 `Android` 完善的 `Activity` 生命周期管理

# Feature

* ⚙️ 自由设置页面保活（keep alive）数量
* 🚀 `llpage` 在操作页面模型时内部采用两个双向链表来保证原子操作的高性能
* 💗 内置常见的页面操作 `open`/`close`/`closeAll`/`refresh`/`closeOthers`
* 👀 单向数据流，模型状态机内部执行，页面操作模型可以被 dump
* 📱 完善的类 `Android` 的生命周期模拟
* 💻 纯内存操作模型，提升微应用框架集成的便捷程度
* 🎉 支持异步生命周期 hook

# Quick Start

```javascript
import { createLLPageManager, createPage } from 'llpage'

const testPage = createPage({
  data: {},

  // llpage 支持异步生命周期 hook
  async onCreate() {
    // do something
    await xx()
  }

  async onStart() {
    // do something
  }
})

const ll = createLLPageManager({
  size: 10
})

// open page
ll.open(testPage)

// close page
ll.close(testPage)

// close others
ll.closeOthers(testPage)

// close all
ll.closeAll()
```

# API

## top api

### createLLPageManager

> 创建一个 llpage 管理容器

### createPage

> 创建一个页面对象

## manager instance api

### open

> 打开一个页面

### close

> 关闭某个页面

### closeAll

> 关闭所有页面

### closeOthers

> 关闭其他页面

### refresh

> 刷新页面

## page lifecycle

### onCreate

> 在页面对象被激活时执行

### onStart

> 在页面对象被激活后，即将开始 running 时执行

### onResume

> 在页面对象被隐藏后重新激活时执行

### onPause

> 在页面对象被隐藏时执行

### onStop

> 在页面对象被销毁前执行

### onDestroy

> 在页面对象被销毁时执行

### onRestart

> 当页面对象曾经被打开过，并且当前处于 dead 状态中，这个时候被重新激活时执行

# Async

> llpage 支持在生命周期内执行异步操作，llpage 内部会自动处理好这些 hook 的执行顺序，因此你可以自由地使用 `async`

# Run Example

![preview](https://user-images.githubusercontent.com/773248/52936756-1f055700-3398-11e9-843a-1899dba7c36a.gif)

* [How to run](./examples/README.md)

# Thanks

* [lru_map](https://www.npmjs.com/package/lru_map)
* [@humanwhocodes/circular-doubly-linked-list](https://www.npmjs.com/package/@humanwhocodes/circular-doubly-linked-list)

## Contributors

Thanks goes to these wonderful people ([emoji key](https://github.com/all-contributors/all-contributors#emoji-key)):

<!-- ALL-CONTRIBUTORS-LIST:START - Do not remove or modify this section -->
<!-- prettier-ignore -->
| [<img src="https://avatars2.githubusercontent.com/u/773248?v=4" width="100px;" alt="Archer (炽宇)"/><br /><sub><b>Archer (炽宇)</b></sub>](http://xiaoa.name)<br />[💻](https://github.com/qddegtya/llpage/commits?author=qddegtya "Code") [🚇](#infra-qddegtya "Infrastructure (Hosting, Build-Tools, etc)") [🚧](#maintenance-qddegtya "Maintenance") [🎨](#design-qddegtya "Design") [📖](https://github.com/qddegtya/llpage/commits?author=qddegtya "Documentation") |
| :---: |
<!-- ALL-CONTRIBUTORS-LIST:END -->

This project follows the [all-contributors](https://github.com/all-contributors/all-contributors) specification. Contributions of any kind welcome!
