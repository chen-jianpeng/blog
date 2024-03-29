---
title: 实现一个简单的MVVM框架
date: 2018-01-12 11:48:12
tags: [hexo]
categories: [深入基础, vue篇]
toc: true
thumbnail: images/实现一个简单的MVVM框架.jpg
---

实现一个简单的MVVM框架

<!-- more -->

## 首先什么是 MVVM

MVVM 是 Model-View-ViewModel 的简写。微软的 WPF 带来了新的技术体验，如 Silverlight、音频、视频、3D、动画……，这导致了软件 UI 层更加细节化、可定制化。同时，在技术层面，WPF 也带来了 诸如 Binding、Dependency Property、Routed Events、Command、DataTemplate、ControlTemplate 等新特性。MVVM（Model-View-ViewModel）框架的由来便是 MVP（Model-View-Presenter）模式与 WPF 结合的应用方式时发展演变过来的一种新型架构框架。它立足于原有 MVP 框架并且把 WPF 的新特性糅合进去，以应对客户日益复杂的需求变化。

## 实现思路

简单来看 MVVM 可以拆分成三部分：View 是视图界面，ViewModel 存放各种业务逻辑和网络请求。实现一个简单的 MVVM 框架，就需要将这三个部分组织起来。

具体实现思路：遍历 dom，替换相应的数据，绑定相应方法等。通过 defineProperty 进行数据劫持，当修改数据时进行视图变更。

## 类图

![类图](leitu.jpg)

## 关键代码

Latte 是整个框架的核心。定义了数据路径对象，获取需要处理的 dom，定义数据，方法。遍历 dom，替换 dom，完成数据劫持等操作。

```js
class Latte {
  constructor(latte) {
    if (!latte.el || !latte.data) {
      throw new Error("Latte need an object to observe.");
    }
    this.$data = latte.data;
    this.$register = new Register();
    this.$el = document.querySelector(latte.el);
    this.$eventList = latte.eventList;
    this.$frag = Latte.node2Fragment(this.$el);
    this.scan(this.$frag);
    this.$el.appendChild(this.$frag);
    this.$register.build();
  }

  // 取出原有的dom存放于fragment
  static node2Fragment(el) {}

  // 采用递归的方式，深层遍历dom。
  // 如果没有list，那么就直接进行dom绑定事件，设置class，替换数据的操作。
  scan(node) {}

  // 返回一个对象 {path, data}
  // path表示当前model在数据中的路径，data表示model的值。
  parseData(str, node) {}

  // 判断需要绑定的事件类型，为dom绑定事件
  parseEvent(node) {}

  // 为dom绑定class
  parseClass(node) {}

  //为dom替换相应的数据，并调用regist方法处理当数据变化时，更新视图
  parseModel(node) {}

  // 处理list数据
  parseList(node) {}

  //处理list数据中的每一项
  parseListItem(node) {}
}
```

其中 scan 通过迭代的方式，完成一个深度优先遍历。通过下面几种标识完成 dom 的解析。调用 parseEvent、parseClass、parseModel 方法，为相应的 dom 绑定事件，设置相应的 class，替换相应的数据等。

- data-model——用于将 DOM 的文本节点替换为制定内容
- data-class——用于将 DOM 的 className 替换为制定内容
- data-list——用于标识接下来将出现一个列表，列表为制定结构
- data-list-item——用于标识列表项的内部结构
- data-event——用于为 DOM 节点绑定指定事件

首先要判断当前的 dom 不是 list，则直接进行 dom 绑定事件，设置 class，替换数据的操作。如果存在子节点则继续遍历。如果是 list 需要单独处理，单独对 list 的每一项进行同样的操作中，这里代码省略。

```js
scan(node) {
  if (node === this.$frag || !node.getAttribute('lait-list')) {
    let childs = [];
    if (node.children) {
      childs = [...node.children];
    } else {
      [...node.childNodes].forEach((child) => {
        if (child.nodeType === 1) {
          childs.push(child);
        }
      });
    }
    childs.forEach((child) => {
      if (node.path) {
        child.path = node.path;
      }
      this.parseEvent(child);
      this.parseClass(child);
      this.parseModel(child);
      if (child.children.length) {
        this.scan(child);
      }
    });
  } else {
    this.parseList(node);
  }
}
```

对于 model 的处理，根据 lait-model 得到 key，然后从 data 获取数据。如果当前 dom 时 input 时设置 value 为获取到的数据，其他情况都是设置其 innerText 。最后调用 register.regist 构建路径对象。

```js
parseModel(node) {
  if (node.getAttribute('lait-model')) {
    const modelName = node.getAttribute('lait-model');
    const dataObj = this.parseData(modelName, node);
    if (node.tagName === 'INPUT') {
      node.value = dataObj.data;
    } else {
      node.innerText = dataObj.data;
    }
    this.$register.regist(this.$data, dataObj.path, (old, now) => {
      if (node.tagName === 'INPUT') {
        node.value = now;
      } else {
        node.innerText = now;
      }
    });
  }
}
```

Register.regist 方法用来构建一个路径对象 routes ，每个路径对象包括数据的 value 值，当前数据在 model 的路径，当前数据拦截后触发的方法。build 方法用于给每个 model 设置数据拦截。

```js
class Register {
  constructor() {
    this.routes = [];
  }

  regist(obj, k, fn) {
    const route = this.routes.find(el => {
      let result;
      if (
        (el.key === k || el.key.toString() === k.toString()) &&
        Object.is(el.obj, obj)
      ) {
        result = el;
      }
      return result;
    });
    if (route) {
      route.fn.push(fn);
    } else {
      this.routes.push({
        obj,
        key: k,
        fn: [fn]
      });
    }
  }
  build() {
    this.routes.forEach(route => {
      observer(route.obj, route.key, route.fn);
    });
  }
}
```

对于 class 和 event 的处理方式与 model 类似，这里省略代码。

上文中提到的 observer 方法通过劫持数据的方式，在数据层发生变化时，视图层随之变化。对数据的劫持是通过 Object.defineProperty 方法进行的，当发现数据值改变了之后，调用相应的路径对象 routes 中定义的方法，更新 dom。由于数据复杂程度不同，因此这里需要分多种情况考虑：对于普通对象直接处理。对于嵌套对象，需要对嵌套的对象的每个属性进行劫持。对于复杂路径的数据，要先取到数据然后再进行处理。对于数组的 push、pop、shift、unshift、splice、sort、reverse 方法进行处理，触发视图更新。

下面只是对简单对象进行了处理，其他复杂情况只有个入口，具体省略

```js
function observer(obj, k, callback) {
  if (!obj || (!k && k !== 0)) {
    throw new Error("Please pass an object to the observer.");
  }
  if (Object.prototype.toString.call(k) === "[object Array]") {
    observePath(obj, k, callback);
  } else {
    let old = obj[k];
    if (!old) {
      throw new Error("The key to observe is undefined.");
    }
    if (Object.prototype.toString.call(old) === "[object Array]") {
      observeArray(old, callback);
    } else if (old.toString() === "[object Object]") {
      observeAllKey(old, callback);
    } else {
      Object.defineProperty(obj, k, {
        enumerable: true,
        configurable: true,
        get: () => old,
        set: now => {
          if (now !== old) {
            callback.forEach(fn => {
              fn(old, now);
            });
          }
          old = now;
        }
      });
    }
  }
}
```

## 使用方法

View 层代码：

```html
<ul lait-list="todos">
  <h1 lait-model="title"></h1>
  <div>
    <li lait-list-item="todos">
      <p lait-class="todos:done" lait-model="todos:creator"></p>
      <p lait-model="todos:date"></p>
      <p lait-model="todos:content"></p>
      <ul lait-list="todos:members">
        <li lait-list-item="todos:members">
          <span lait-model="todos:members:name"></span>
        </li>
      </ul>
    </li>
  </div>
</ul>
<button lait-event="click">添加</button>
```

Model 层代码：

```js
let data = {
  title: "todo list!",
  user: "mirone",
  todos: [
    {
      creator: "keal",
      done: "undone",
      content: "writeMVVM",
      date: "2016-11-17",
      members: [{ name: "kaito" }, { name: "hito" }, { name: "QAQ" }]
    },
    {
      creator: "mirone",
      done: "done",
      content: "writeNode",
      date: "2016-12-17",
      members: [{ name: "hitomiao" }, { name: "miaomiao" }]
    }
  ]
};
```

ViewModel 层代码：

```js
new Latte({
  el: "#root",
  data,
  eventList: {
    click: {
      type: "click",
      fn: function() {
        data.todos.push({
          creator: "k",
          done: "undone",
          date: "2016-12-24",
          content: "get mvvm",
          members: [{ name: "hito" }]
        });
      }
    }
  }
});
```

最终显示效果：

![最终显示效果](result.jpg)
