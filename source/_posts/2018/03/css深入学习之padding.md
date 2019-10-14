---
title: css深入学习之padding
date: 2018-03-03 09:44:26
tags: [css]
categories: [基础系列, css篇]
toc: true
thumbnail: images/css深入学习.jpg
---

css深入学习之padding

<!-- more -->

## padding

### 与容器尺寸

块状元素下
padding 值暴走，一定会影响尺寸。
width 为非 auto，padding 影响尺寸
width 为 auto，或 box-sizing 为 border-box 时，padding 没有暴走，不影响尺寸。

内敛元素下
垂直方向的 padding 不会影响尺寸，水平方向会。但是会影响背景色（占据空间）

用途：分割线的实现

<iframe height='265' scrolling='no' title='gvJoxd' src='//codepen.io/chenjp/embed/gvJoxd/?height=265&theme-id=light&default-tab=css,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/chenjp/pen/gvJoxd/'>gvJoxd</a> by chenjp (<a href='https://codepen.io/chenjp'>@chenjp</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

### padding 取值

不支持负值

百分比值相对于宽度计算
用途：实现所有屏幕下都是正方形

<iframe height='265' scrolling='no' title='bLyaLy' src='//codepen.io/chenjp/embed/bLyaLy/?height=265&theme-id=light&default-tab=css,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/chenjp/pen/bLyaLy/'>bLyaLy</a> by chenjp (<a href='https://codepen.io/chenjp'>@chenjp</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

内敛元素的 padding 同样相对于宽度计算，默认的高宽有细节差异，会换行。
宽高差异是因为内敛元素的垂直 padding 会让‘幽灵空白节点’（内敛元素文本的 control-area 区域）显现，也就是规范中的 strut 出现。当 font-size 为零时，就好了。

### 标签元素的内置 padding

ol/ul
ol/li 元素内置 padding-left，单位是 px 不是 em
谷歌浏览器下是 40px
字号很小时，左边间距很开
字号很大时，序号跑到外边。
一般文字不是很大时，padding-left 设置为 22 到 25

表单元素
input/textarea 输入框内置 padding，大约为 1 到 2 像素
button 元素内置 padding
部分浏览器 select 下拉内置 padding，如火狐。IE8+可以设置 padding。
radio/checkbox 无内置 padding

按钮的 padding 最难控制
火狐浏览器下 padding 为 0 时，左右依然有 padding。设置 button::-moz-focus-inner{padding:0}即可解决。IE7 下，按钮文字越多，左右 padding 值越大，设置 overflow:visible 解决。
padding 与高度计算不兼容。行高为 20，padding 为 10 时，IE7 高度为 45，IE8 和谷歌浏览器高度为 40，火狐为 42。利用 a 标签替换 button，或者借助 label。

### 图形绘制

实现三道杠，白眼效果

<p data-height="265" data-theme-id="light" data-slug-hash="aqrqgX" data-default-tab="css,result" data-user="chenjp" data-embed-version="2" data-pen-title="padding与图形绘制1" class="codepen">See the Pen <a href="https://codepen.io/chenjp/pen/aqrqgX/">padding与图形绘制1</a> by chenjp (<a href="https://codepen.io/chenjp">@chenjp</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

background-clip:content-box 实现背景色只在内容区域现实

### 布局实现

百分比单位构建固定比例布局的结构，例如上面的所有屏幕一比一
配合 margin 实现等高布局

<iframe height='265' scrolling='no' title='padding与布局1' src='//codepen.io/chenjp/embed/PQvRjj/?height=265&theme-id=light&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/chenjp/pen/PQvRjj/'>padding与布局1</a> by chenjp (<a href='https://codepen.io/chenjp'>@chenjp</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

两栏自适应布局

<p data-height="265" data-theme-id="light" data-slug-hash="bLyMBO" data-default-tab="html,result" data-user="chenjp" data-embed-version="2" data-pen-title="padding与布局2" class="codepen">See the Pen <a href="https://codepen.io/chenjp/pen/bLyMBO/">padding与布局2</a> by chenjp (<a href="https://codepen.io/chenjp">@chenjp</a>) on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

以上内容总结自张鑫旭老师在慕课网上的课程
