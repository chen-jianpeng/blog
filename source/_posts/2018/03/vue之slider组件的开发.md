---
title: vue之slider组件的开发
date: 2018-03-02 09:40:26
tags: [vue, 组件]
categories: [阶段总结, 项目开发]
toc: true
thumbnail: images/vue之slider组件的开发.jpg
---

一个简单的滑块组件的开发，过程中也遇到了一些有趣的问题，这里总结一下

<!-- more -->

**问题：**
如果组件默认是隐藏的，那么滑动范围的初始宽度就是零了。滑块的位置是通过设置 left 值来设定的，刚开始计算 left 值的思路是通过比例乘以滑动范围得到固定值，但是初始宽度是零了，所以初始滑块位置就产生了问题。

> 平时要获取一个元素的尺寸一般都会直接使用 offsetWidth 和 offsetHeight 来获取，但是这两个属性是通过 CSS 渲染到页面上时候才计算的。而 dispaly 属性为 none 的元素将不参与渲染，它不会在渲染环境中生成任何盒子，自然也无法从属性中获取到尺寸。

**解决：**
left 值可以通过设置百分比值，不需要固定值，这样就不用滑动范围的宽度。

但是如果 100%时，滑块位置超过了滑动范围，这时通过设置 `transform: translateX(-50%);`，解决了问题。

滑动过程计算滑块位置还是需要用到滑动范围宽度，也不能直接用`this.$refs.sliderBar.clientWidth`，需要通过 computed 来获取

```js
computed: {
  barWidth() {
    return this.$refs.sliderBar.clientWidth
  }
}
```
