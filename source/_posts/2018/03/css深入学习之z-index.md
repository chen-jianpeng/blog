---
title: css深入学习之z-index
date: 2018-03-04 09:44:26
tags: [css]
categories: [基础系列, css篇]
toc: true
thumbnail: images/css深入学习.jpg
---

css深入学习之z-index

<!-- more -->

## 基础

z-index 支持负值，支持长 css3 animation 动画，css2.1 时需要和定位元素配合使用。

## 与 css 定位属性

没有嵌套：后来居上，谁大谁上。
嵌套：祖先优先原则。

例子：
祖先优先原则失效了？
z-index 为 auto 时，当前层叠上下文的生成盒子层叠水平是 0，盒子（除非是根元素）不会创建一个新的层叠上下文。
所以导致 block1 的 z-index 生效了。

<iframe height='265' scrolling='no' title='css深入学习之z-index' src='//codepen.io/chenjp/embed/aqrEzN/?height=265&theme-id=light&default-tab=css,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/chenjp/pen/aqrEzN/'>css深入学习之z-index</a> by chenjp (<a href='https://codepen.io/chenjp'>@chenjp</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

## 层叠上下文和层叠水平

层叠上下文表示谁离皇帝更近，层叠水平决定谁离官员更近

### 层叠上下文

三维概念，表示普通元素高人一等了。

#### 存在

页面根元素天生有层叠上下文。
z-index 为数值的定位元素也有层叠上下文。
设置其他属性，使得其存在。(包括设置了 flex，opacity 不为 1，tansform：roatate，filter 等)

#### 特性

可嵌套。
每个层叠上下文和兄弟元素独立。
每个层叠上下文都自成体系，元素内容被层叠之后，整个元素被认为是在父层的层叠顺序中。

### 层叠水平

1. 层叠上下文中的每个元素都有一个层叠水平。
2. 层叠水平就是普通元素的论资排辈
3. 层叠水平和 z-index 不是一个东西

## 层叠顺序

元素发生层叠时候有着特定的垂直显示顺序

### 七阶层叠水平

- 装饰：
  - 层叠上下文的 background/border
  - 负 z-index，
- 布局：
  - block 块状水平盒子，
  - float 浮动盒子，
- 内容：
  - inline/inline-block 水平盒子，
  - z-index:auto 或看成 z-index:0，不依赖 z-index 的层叠上下文（就是设置了除 z-index 以外的属性产生的层叠上下文）
  -正 z-index

![七阶层叠水平](七阶层叠顺序.png)

### 意义：规范元素重叠时候的呈现规则

### 为什么有七阶层叠水平

符合页面加载和页面呈现。内容最重要。

例子：
背景色是因为 inline-block 的层级更高，文字都是内联元素遵循后来居上。

<iframe height='265' scrolling='no' title='七阶层叠水平1' src='//codepen.io/chenjp/embed/rJgYdg/?height=265&theme-id=light&default-tab=css,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/chenjp/pen/rJgYdg/'>七阶层叠水平1</a> by chenjp (<a href='https://codepen.io/chenjp'>@chenjp</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

## z-index 与层叠上下文

1. 定位元素默认 z-index：auto 可以看成 z-index：0
2. z-index 不为 auto 的定位元素会创建层叠上下文
3. z-index 层叠顺序的比较止步于父级层叠上下文

例子 1：
为何定位元素会覆盖普通元素？因为定位元素未设置 z-index 时就是 auto，根据七阶层叠水平得到，其层级高于 inline-block 元素。

<iframe height='265' scrolling='no' title='z-index与层叠上下文1' src='//codepen.io/chenjp/embed/GQaOgq/?height=265&theme-id=light&default-tab=css,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/chenjp/pen/GQaOgq/'>z-index与层叠上下文1</a> by chenjp (<a href='https://codepen.io/chenjp'>@chenjp</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

例子 2：
block 的 z-index 为-1，找到他的第一个父级层叠上下文元素就是 wrap，根据七阶层叠水平 z-index 为负，但仍高于背景色，所以产生如下效果

<iframe height='265' scrolling='no' title='z-index与层叠上下文2' src='//codepen.io/chenjp/embed/jZoaVx/?height=265&theme-id=light&default-tab=css,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/chenjp/pen/jZoaVx/'>z-index与层叠上下文2</a> by chenjp (<a href='https://codepen.io/chenjp'>@chenjp</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

例子 3：
受制于父级层叠上下文

<iframe height='265' scrolling='no' title='z-index与层叠上下文3' src='//codepen.io/chenjp/embed/WMBXXJ/?height=265&theme-id=light&default-tab=css,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/chenjp/pen/WMBXXJ/'>z-index与层叠上下文3</a> by chenjp (<a href='https://codepen.io/chenjp'>@chenjp</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

## z-index 与非定位元素层叠上下文

1. 不支持 z-index 的层叠上下文元素的层叠顺序均是 z-index=auto 的级别
2. 依赖 z-index 的层叠上下文，层叠顺序依赖 z-index 值。包括两种情况：

- position 为 relative/absolute 或者 fixed（但是 chrome 下，fixed 元素不需要设置 z-index 即可创建层叠上下文）
- display 为 flex/inline-flex 容器的子 flex 项

例子 1：
除了第一个 div，其他的都创建了层叠上下文，z-index 都是 auto，遵循后来居上，所以产生如下效果

<iframe height='265' scrolling='no' title='z-index与非定位元素层叠上下文1' src='//codepen.io/chenjp/embed/LQoyBv/?height=265&theme-id=light&default-tab=css,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/chenjp/pen/LQoyBv/'>z-index与非定位元素层叠上下文1</a> by chenjp (<a href='https://codepen.io/chenjp'>@chenjp</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

例子 2：
由于 block1 设置了 display：absolute，block2 设置了 flex，都创建了层叠上下文，层级相同，但背景色的七阶水平更低，所以在下边。

<iframe height='265' scrolling='no' title='z-index与非定位元素层叠上下文2' src='//codepen.io/chenjp/embed/yvWXEZ/?height=265&theme-id=light&default-tab=css,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/chenjp/pen/yvWXEZ/'>z-index与非定位元素层叠上下文2</a> by chenjp (<a href='https://codepen.io/chenjp'>@chenjp</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

例子 3：
图片设置一个淡出的效果，文字在 div 完全显示后才显示。原因是 opacity 不是 1 时会创建层叠上下文，这是他的层级与文字的层级是一样的，相当于 z-index：auto，遵循后来居上，所以 div 遮住文字。当 opacity 为 1 时，div 变成普通元素，所以文字在 div 之上。

<iframe height='265' scrolling='no' title='z-index与非定位元素层叠上下文 3' src='//codepen.io/chenjp/embed/vdwmxw/?height=265&theme-id=light&default-tab=css,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/chenjp/pen/vdwmxw/'>z-index与非定位元素层叠上下文 3</a> by chenjp (<a href='https://codepen.io/chenjp'>@chenjp</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

## z-index 使用忠告

1. 最小化原则，能不用就别用
2. 不随意设置，尽量不超过 2，使用后来居上覆盖
3. 组件层级计数器，通过 js 设置 z-index。
4. 通过设置 z-index 使元素可访问性隐藏

以上内容总结自张鑫旭老师在慕课网上的课程
