---
title: 编码规范之eslint
date: 2018-01-13 11:52:28
tags: [规范, eslint]
categories: [深入基础, 规范篇]
toc: true
thumbnail: images/编码规范之eslint.jpg
---

在团队协作中，为避免低级 Bug、产出风格统一的代码，会预先制定编码规范。使用 Lint 工具和代码风格检测工具，则可以辅助编码规范执行，有效控制代码质量。总之就是为了让别人看自己写的代码时，尽量不产生抵触情绪🙈。

<!-- more -->

**首先了解一下基本知识。**

1. eslint 结合 webpack 使用时，配置相关规则的文件在项目根目录的.eslintrc.js 文件中。

2. 当然可以针对某种情况单独配置规则。例如下面的代码，规则默认是 new 一个对象必须赋值给一个变量，但是这里是一个特殊情况，需要特殊处理，所以通过注释的方式忽略掉。但是请不要乱用，通常还是要遵守统一规则的。

   ```js
   /* eslint no-new: 0 */
   new Vue({
     el: "#app",
     router,
     template: "<App/>",
     components: { App }
   });
   ```

3. 一些参数的含义

   "off" 或 0 - 关闭规则
   "warn" 或 1 - 开启规则，使用警告级别的错误：warn (不会导致程序退出)
   "error"或 2 - 开启规则，使用错误级别的错误：error (当被触发的时候，程序会退出)

4. 项目中使用标准配置，[标准配置文档](https://github.com/standard/standard/blob/master/docs/RULES-zhcn.md)

**下面就来看一下，我在 utry-vue-doc 中都遇到了哪些问题吧。**

注：下面的设置为 0 的是我认为没必要的，设置为 2 的是我认为应该检查的。

```js
// 箭头函数的参数使用圆括号
'arrow-parens': 0,

// production下不允许有debugger
'no-debugger': process.env.NODE_ENV === 'production' ? 2 : 0,

// production下不允许有console
'no-console': process.env.NODE_ENV === 'production' ? 2 : 0,

// 禁用不必要的分号
'no-extra-semi': 2,

// (默认) 要求在语句末尾使用分号
'semi': 0,

// 强制分号前后有空格
'semi-spacing': 0,

// 函数圆括号之前有一个空格
'space-before-function-paren': 0,

// 语句块之前的空格
'space-before-blocks': 2,

// 强制在对象字面量的键和值之间使用一致的空格
'key-spacing': 2,

// 强制使用一致的反勾号、双引号或单引号
'quotes': 0,

// 强制将对象的属性放在不同的行上
// 建议复杂的对象还是放在不同行上
'object-property-newline': 0,

// 强制使用一致的缩进,2个空格。switch语句缩进设置
'indent': [2, 2, { "SwitchCase": 1 }],

// 当最后一个元素或属性与闭括号 ] 或 } 在 不同的行时，
// 允许（但不要求）使用拖尾逗号；
// 当在 同一行时，禁止使用拖尾逗号。
'comma-dangle': [2,'only-multiline'],

// 强制关键字周围空格的一致性
// 关键字前后都要有空格
'keyword-spacing': 2,

// 要求中缀操作符周围有空格
'space-infix-ops': 2,

// 强制在逗号周围使用空格
// 逗号前边没空格，逗号后面使用一个或多个空格
'comma-spacing': 2,

// 禁止出现多个空格
'no-multi-spaces': 2,

// 禁止所有tab
'no-tabs': 2,

// 要求使用 === 和 !==
'eqeqeq': 0,

// 逗号风格
// 要求逗号放在数组元素、对象属性或变量声明之后，且在同一行
'comma-style': 2,

// 禁用不必要的转义，什么叫不必要的转义这个还真不知道是啥意思
'no-useless-escape': 0,

// 块内填充（块语句以空行开始并且以空行结束）
'padded-blocks': 0,

// 禁止在对象字面量中出现重复的键
'no-dupe-keys': 2,

// 强制在花括号中使用一致的空格。
// 左右花括号在同一行时，左花括号右边有一个空格,并且右花括号左边有一个空格。
'object-curly-spacing': [2,'always'],

// 要求使用骆驼拼写法
'camelcase': 2,

// ${variable} 应该在两个 (`)之间，不应该在两个(')或(")之间
'no-template-curly-in-string': 0,

// 在注释有空白
'spaced-comment': 2,

// 禁用行尾空白
'no-trailing-spaces': 0,

// 大括号风格要求
// 默认one true brace style。
// 允许块的开括号和闭括号在 同一行
'brace-style': [2, "1tbs", { "allowSingleLine": true }],

// 禁止在返回语句中赋值
'no-return-assign': 2,

// 要求遵循大括号约定,即使代码块只有一条语句时，仍需要加大括号
'curly': 2,

// 禁用一成不变的循环条件
'no-unmodified-loop-condition': 0,

// 禁止或强制圆括号内的空格。(默认) 强制圆括号内没有空格
'space-in-parens': 0,

// new一个object必须赋值给一个变量
'no-new': 2
```

- 大家如果有什么疑问也可以到[eslint 官网](http://eslint.cn/docs/rules/)查询。（搜索框里搜索一下，很多例子，一看就懂了）。

- 还想说一下编辑器一般都会有问题面板，如果有报问题，还是修改一下的比较好。编辑器也有自动整理代码格式的功能，一般是 ctrl+shift+f，会把代码结构整理下，但是有的时候也会有意想不到的结果，所以慎用吧。

- 不规范的代码往往是因为从其他地方粘贴过来导致的，所以粘贴之后还是整理一下比较好。

- 整理后的代码我新开了一个分支叫 sortOut，有需要的也可以看一下。

- 一些个人的观点，请大家多多指教吧，为了公司前端开发有一个更好的规范，为了少一些坑，哈哈哈哈。
