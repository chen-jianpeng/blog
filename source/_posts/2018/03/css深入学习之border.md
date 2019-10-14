---
title: css深入学习之border
date: 2018-03-01 09:44:26
tags: [css]
categories: [基础系列, css篇]
toc: true
thumbnail: images/css深入学习.jpg
---

css深入学习之border

<!-- more -->

## border-width 不支持百分比

边框不会随着容器大小的变化而变化
类似的还有 outline、box-shadow、text-shadow
关键字 thin medium thick
默认值为 3，因为 border-style：double 有效果至少要 3 像素

## border-style

dashed 虚线
谷歌火狐实色区域宽高 3:1，实色和透明色 1:1。IE 实色宽高 2:1，实色透明色 2:1
border-emate 解决此问题

dotted 点线
谷歌火狐小方，ie 小圆

double 双线
计算规则：双线宽度相等，中间区域+-1
实例：实现三道杠的菜单按钮

<iframe height='265' scrolling='no' title='padding与布局2' src='//codepen.io/chenjp/embed/bLyMBO/?height=265&theme-id=light&default-tab=css,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/chenjp/pen/bLyMBO/'>padding与布局2</a> by chenjp (<a href='https://codepen.io/chenjp'>@chenjp</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

inset 内凹 outset 外凸，山脊山谷很不常用

## border-color color

border-color 默认为 color 的颜色，border-shadow，text-shadow，outline 也是
用途：hover 变色，只需要在父级上添加即可

<iframe height='265' scrolling='no' title='加号按钮' src='//codepen.io/chenjp/embed/MVgMEo/?height=265&theme-id=light&default-tab=css,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/chenjp/pen/MVgMEo/'>加号按钮</a> by chenjp (<a href='https://codepen.io/chenjp'>@chenjp</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

## background-position

相对于左上角定位(css2.1)。
实现右下角定位，因为 position 定位不包括 border，所以设置 position 的 x 为 100%，border 为固定值的宽度即可。

<iframe height='265' scrolling='no' title='图片背景以右侧定位' src='//codepen.io/chenjp/embed/xWKvxB/?height=265&theme-id=light&default-tab=css,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/chenjp/pen/xWKvxB/'>图片背景以右侧定位</a> by chenjp (<a href='https://codepen.io/chenjp'>@chenjp</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

## border 之图形构建

IE78 实现圆角
三道杠
三角实现
模拟圆角，上下两个梯形

<iframe height='265' scrolling='no' title='border之三角形' src='//codepen.io/chenjp/embed/rdBXYR/?height=265&theme-id=light&default-tab=css,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/chenjp/pen/rdBXYR/'>border之三角形</a> by chenjp (<a href='https://codepen.io/chenjp'>@chenjp</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

## 透明边框

增加响应区域大小
filter：drop-shadow 改变 png 图片颜色

## 布局使用

border 等高布局，不支持百分比

<iframe height='265' scrolling='no' title='qoBWex' src='//codepen.io/chenjp/embed/qoBWex/?height=265&theme-id=light&default-tab=html,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/chenjp/pen/qoBWex/'>qoBWex</a> by chenjp (<a href='https://codepen.io/chenjp'>@chenjp</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

以上内容总结自张鑫旭老师在慕课网上的课程
