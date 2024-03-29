---
title: vue之table组件的开发
date: 2018-01-15 11:52:28
tags: [vue, 组件]
categories: [阶段总结, 项目开发]
toc: true
thumbnail: images/vue之table组件的开发.jpg
---

开发一套 vue 的 table 组件，通过零零散散的时间算是基本上写完了。今天就来总结一下吧。

<!-- more -->

## 设计文档

为了开发过程中有一个参照，所以简单的写了一个计划。

![设计计划文档](jihua1.jpg)
![设计计划文档](jihua2.jpg)
![设计计划文档](jihua3.jpg)

一个大而全的表格组件即将在我手中诞生了，想想就有点小激动。给领导看过之后说了一句话，让我受益匪浅，赶紧记录一下，大意就是“并不是大而全就好用，重要的是要让别人觉得好用，可以用。”

反思总结了一下，重新整理计划，发现先前设计的搜索区域和工具区域其实很难控制好，因为不同的项目界面设计上差别很大，因此去除了这两部分。还有之前是想传 url 给组件，然后组件内部去请求后台数据，这样组也很难控制，还是传递数据给组件这样比较好。

设计好了就开工吧

## 开发要点、难点

1. 为完成表头表体分离的功能，表头和表体需要两个 table，这就需要维护每个表头和相应表体的对齐关系。

2. 如果表体滚动时会产生滚动条，所以表头需要留出相应大小的位置。

3. 为了完成列固定的功能，同样需要 table，又要维护对齐关系了，同时还要同步滚动。

4. 表头分组功能数据处理。

5. 行合并，列合并的功能。

6. 异步请求导致出错。

## 数据准备

用户将表头信息 columns、表格数据 data、参数信息 options 传给组件。

表头数据示例内容如下：title 显示的文字；type 表示这一列的类型，包括 selection（选择）、detail（详情）；key 对应数据，用于获取数据；width 设置当前列的宽度；aligin 内容对齐方式；fixed 固定列位置；sortable 是否可以排序；children 表示分组；render 渲染的内容，可以自定义处理内容。

```js
columns: [
  {
    type: "selection",
    key: "selection",
    width: 60,
    align: "center",
    fixed: "left"
  },
  {
    title: "姓名",
    key: "name",
    sortable: true
  },
  {
    title: "其他",
    fixed: "left",
    children: [
      {
        title: "年龄",
        key: "age",
        sortable: (a, b, isAsc) => {
          if (isAsc) {
            return a - b;
          } else {
            return b - a;
          }
        }
      },
      {
        title: "地址",
        key: "address",
        children: [
          {
            title: "街道",
            key: "street"
          },
          {
            title: "街区",
            key: "block",
            children: [
              {
                title: "楼栋",
                key: "building"
              },
              {
                title: "门牌号",
                key: "number"
              }
            ]
          }
        ]
      }
    ]
  },
  {
    title: "公司",
    key: "company",
    children: [
      {
        title: "公司地址",
        key: "companyAddress"
      },
      {
        title: "公司名称",
        key: "companyName"
      }
    ]
  },
  {
    title: "性别",
    key: "gender"
  },
  {
    title: "操作",
    key: "render",
    type: "render",
    fixed: "right",
    width: 210,
    render: (h, params) => {
      return [
        h(
          "Button",
          {
            props: {
              type: "primary",
              size: "small"
            },
            on: {
              click: function() {
                console.log("你点击了删除按钮");
              }
            }
          },
          "删除"
        ),
        h(
          "Button",
          {
            props: {
              type: "primary",
              size: "small"
            }
          },
          "修改"
        )
      ];
    }
  }
];
```

options 参数的配置形式如下，height 表示表格的高度，minWidth 表示表格最小宽度，currentPage 当前页码，total 总条目数，pageSize 当前页大小，headBackground 表头背景色，stripe 斑马线颜色，bodyHover 悬浮颜色,rowRender 表体渲染函数，bodyEndRender 表格结尾

```js
options: {
  height: 263,
  minWidth: 1500,
  currentPage: 1,
  total: 10,
  pageSize: 10,
  headBackground: '#f5f7fa',
  stripe: '#fafafa',
  bodyHover: '#f5f7fa',
  rowRender: (h, params) => { },
  bodyEndRender: (h, params) => { }
}
```

data 是数据，以一个对象数组的形式传入即可。

## 关键点举例分析

下面就几个关键点的代码进行解析

### 表头和相应表体的对齐关系

利用 colgroup 统一表头和表体每一列的宽度，如果有设置具体宽度那么使用设置的宽度，如果没有设置宽度，那么以表体每一列的宽度我基准进行设置。但要注意如果给表体设置了固定的高度，当产生滚动条时，表头也要预留出滚动条的宽度。具体代码如下

```html
<colgroup>
  <col
    v-for="(item, index) in cloneColumns"
    :key="index"
    :width="item.width?item.width:item.newWidth"
  />
  <col v-if="scrollBarWidth" :width="scrollBarWidth" />
</colgroup>
```

```js
const $td = this.$refs.tableBodyEl.$el
  .querySelectorAll("tbody tr")[0]
  .querySelectorAll("td");
for (let i = 0; i < $td.length; i++) {
  let width = parseInt(getStyle($td[i], "width"));
  if (!this.cloneColumns[i].width) {
    Vue.set(this.cloneColumns[i], "newWidth", width);
  }
}
```

### 同步滚动

业务需求是这样的，出现横向滚动条时某些列需要固定。实现效果如下

![列固定](table_fixed.jpg)

遇到的问题主要是因为，三个表格在滚动时，滚动其中一个时另外两个需要同时随着滚动，但由于设置 scrollTop 时同样会触发 onScroll 事件，导致滚动时的卡顿。解决方案是在 mouseenter 里面绑定 scroll 事件，这样卡顿的问题就解决了。代码很简单，就不放了。

### 表头分组

表格分组时计算 colspan rowspan 是一个难点。需求是要解析上面提到的 columns 数据，然后生成如下的表头

![表格头部示例](table_head.jpg)

解决的思路是递归的方式，让父辈的 colspan 是子辈的和。深层遍历时记录深度，最上层的 level 就是 1，每深入一层，level 加 1。这样有子辈的 rowspan 就是 1，没有子辈的就填满空白就可以了。具体代码的实现如下

```js
// 表格分组时计算colspan rowspan
const convertToRows = (originColumns, tag) => {
  //表头colspan设置
  const traverse = (column, parent) => {
    if (parent) {
      column.level = parent.level + 1;
      if (this.maxLevel < column.level) {
        this.maxLevel = column.level;
      }
    }
    if (column.children) {
      let colSpan = 0;
      column.children.forEach(subColumn => {
        traverse(subColumn, column);
        colSpan += subColumn.colSpan;
      });
      column.colSpan = colSpan;
    } else {
      column.colSpan = 1;
    }
  };

  originColumns.forEach(column => {
    column.level = 1;
    traverse(column);
  });

  const rows = [];
  for (let i = 0; i < this.maxLevel; i++) {
    rows.push([]);
  }

  const allColumns = getAllColumns(originColumns);

  allColumns.forEach((column, index) => {
    column._index = index;

    // 表头rowspan设置
    if (!column.children) {
      column.rowSpan = this.maxLevel - column.level + 1;
    } else {
      column.rowSpan = 1;
    }

    // 设置表体要显示的内容的key
    if (tag === "center" && column.colSpan === 1) {
      this.cloneColumns.push(column);
    }

    rows[column.level - 1].push(column);
  });

  return rows;
};
```

### 复杂业务需求

变态需求年年有，想做一个“万精油”一样的组件很难。先看一个这样的需求，实现效果如图所示。

![复杂表格，那就纯手工吧](table_render.jpg)

这其实也是最近一个版本的需求，这个版本之前不知道改了多少版。这种定制化要求极高的就纯手工来吧，上代码：

```js
options5: {
  rowRender: (h, params) => {
    let _this = this;
    let renderHtml = [];
    for (var i = 0; i < params.rows.length; i++) {
      let tempRender = [];
      let row = params.rows[i];
      let rowStyle = i % 2 === 0 ? "background-color:#eef7fc" : false;
      for (var j = 0; j < params.columns.length; j++) {
        let column = params.columns[j];
        let temp = null;

        switch (column.key) {
          case "acceptanceTime":
            temp = formatDate(new Date(row[column.key]), "YYYY-MM-dd hh:mm:ss");
            tempRender.push(
              h("td", { attrs: { style: rowStyle } }, [
                h("div", { attrs: { class: "table-cell" } }, temp)
              ])
            );
            break;
          case "ownerPhone":
            temp = row.ownerPhone + ";" + row.callinUser;
            tempRender.push(
              h("td", { attrs: { style: rowStyle } }, [
                h("div", { attrs: { class: "table-cell" } }, temp)
              ])
            );
            break;
          case "voice":
            tempRender.push(
              h("td", { attrs: { style: rowStyle } }, [
                h(
                  "div",
                  {
                    attrs: { class: "table-cell", style: "cursor: pointer" },
                    on: {
                      click: (function(i) {
                        return function() {
                          _this.playVoice((i % 4) + 1);
                        };
                      })(i)
                    }
                  },
                  [h("button", {}, "播放音频")]
                )
              ])
            );
            break;
          default:
            temp = row[column.key];
            tempRender.push(
              h("td", { attrs: { style: rowStyle } }, [
                h("div", { attrs: { class: "table-cell" } }, temp)
              ])
            );
            break;
        }
      }

      renderHtml.push(h("tr", tempRender));
      let tempHtml = [
        h("td", { attrs: { colSpan: 4, style: rowStyle } }, [
          h("div", { attrs: { class: "table-cell" } }, [
            h("span", { attrs: { style: "font-weight: bold" } }, "移车地址："),
            row.movingPosition
          ])
        ])
      ];
      tempHtml.push(
        h("td", { attrs: { colSpan: 4, style: rowStyle } }, [
          h("div", { attrs: { class: "table-cell" } }, [
            h("span", { attrs: { style: "font-weight: bold" } }, "备注："),
            row.remark
          ])
        ])
      );
      renderHtml.push(h("tr", tempHtml));
    }

    return renderHtml;
  };
}
```

这里遇到了一个闭包的问题，播报语音时需要四种类型的语音循环匹配。循环中用到的 i 是同一个变量，所以导致最终都是播放的同一个语音。后来加了一个立即执行函数，解决了问题。后来发现其实声明 i 的时候用 let 就可以了，更简单。

## 总结

第一次做组件开发，收获还是挺多的，包括对迭代，闭包等知识有了进一步的了解。结束。
