---
title: Extjs-lesson6
tags: extjs
categories: extjs
date: 2020-06-29 18:16:22
---

<center>Author：闫玉良</center>

`Ext.js` 系列课程笔记「布局」

<!--more-->

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***

## 1.AccordionLayout

**介绍**：经常用来对左侧功能列表进行分类

**效果**：手风琴式布局，可以折叠的布局

### 1.1 展示

![AccordionLayout](https://gitee.com/Ethanyan/pic_data/raw/c34b732d4c43f42e62044916ec83fd25e9f162da/AccordionLayout.png)

### 1.2 代码

```js
Ext.onReady(function() {
  var AccordionLayout = new Ext.Panel({
    title: "AccordionLayout",
    //布局样式
    layout: "accordion",
    height: 200,
    items: [
      new Ext.Panel({
        title: "国家",
        items: [
          new Ext.BoxComponent({
            autoEl: { tag: "div", html: "中国" }
          })
        ]
      }),
      new Ext.Panel({
        title: "省",
        items: [
          new Ext.BoxComponent({
            autoEl: { tag: "div", html: "台湾省" }
          })
        ]
      }),
      new Ext.Panel({
        title: "市",
        items: [
          new Ext.BoxComponent({
            autoEl: { tag: "div", html: "台北市" }
          })
        ]
      })
    ],
    //将面板显示到HTML中：
    //<div id="AccordionLayout" style="width: 300px; float: left; height: 200px"></div>
    renderTo: "AccordionLayout"
  });
});

```



## 2.BorderLayout

**介绍**：经常用来做后台框架的布局。

**效果**：将页面分为了上下左右和中间几个位置

### 2.1 展示

![BorderLayout](https://gitee.com/Ethanyan/pic_data/raw/c34b732d4c43f42e62044916ec83fd25e9f162da/BorderLayout.png)

### 2.2 代码

```js
Ext.onReady(function() {
  var BorderLayout = new Ext.Panel({
    title: "BorderLayout",
    layout: "border",
    width: 1100,
    height: 300,
    items: [
      new Ext.Panel({
        title: "北宫玄武",
        region: "north",
        html: "可以放个logo什么的"
      }),
      new Ext.Panel({
        title: "南方朱鸟",
        region: "south",
        html: "版权信息？",
        autoEl: "center"
      }),
      new Ext.Panel({
        title: "中间",
        region: "center",
        html: "《史记-天宫书》"
      }),
      new Ext.Panel({
        title: "东宫苍龙",
        region: "west",
        html: "树型菜单或是手风琴"
      }),
      new Ext.Panel({
        title: "西宫咸池",
        region: "east",
        html: "常用功能或是去掉？"
      })
    ],
    //将面板显示到HTML中：
    //<div id="BorderLayout" style="padding: 10px 0px; clear: both"></div>
    renderTo: "BorderLayout"
  });
});
```



## 3.ColumnLayout

**效果：**将内部组件水平方向展开

### 3.1 展示

![ColumnLayout](https://gitee.com/Ethanyan/pic_data/raw/c34b732d4c43f42e62044916ec83fd25e9f162da/ColumnLayout.png)

### 3.2 代码

```js
Ext.onReady(function() {
  var ColumnLayout = new Ext.Panel({
    width: 600,
    title: "ColumnLayout",
    layout: "column",
    items: [
      new Ext.form.FormPanel({
        title: "第一列",
        columnWidth: 0.33,
        labelWidth: 50,
        items: [new Ext.form.TextField({ fieldLabel: "姓名" })]
      }),
      new Ext.form.FormPanel({
        title: "第二列",
        columnWidth: 0.33,
        labelWidth: 50,
        items: [new Ext.form.TextField({ fieldLabel: "年龄" })]
      }),
      new Ext.form.FormPanel({
        title: "第三列",
        columnWidth: 0.34,
        labelWidth: 60,
        items: [new Ext.form.TextField({ fieldLabel: "爱好" })]
      })
    ],
    //将面板显示到HTML中：
    //<div id="ColumnLayout" style="float: left; width: 500px; padding-left: 10px;"></div>
    renderTo: "ColumnLayout"
  });
});
```



## 4.ContainerLayout

**介绍**：这种是默认的布局方式，其他的布局都继承了该类进行适当的扩展。

**效果**：将内部组件竖直方向进行堆叠

> 当你写一个面板，没有 `layout` 时，默认就采用了这种布局。

### 4.1 展示

![ContainerLayout](https://gitee.com/Ethanyan/pic_data/raw/c34b732d4c43f42e62044916ec83fd25e9f162da/ContainerLayout.png)

### 4.2 代码

```js
Ext.onReady(function() {
  var box1 = new Ext.BoxComponent({
    autoEl: {
      tag: "div",
      style: "background:red;width:300px;height:30px",
      html: "box1"
    }
  });
  var box2 = new Ext.BoxComponent({
    autoEl: {
      tag: "div",
      style: "background:yellow;width:300px;height:30px",
      html: "box2"
    }
  });
  var box3 = new Ext.BoxComponent({
    autoEl: {
      tag: "div",
      style: "background:blue;width:300px;height:30px;color:#fff",
      html: "box3"
    }
  });
  //创建一个面板，没有设置 layout 字段
  var containerlayout = new Ext.Panel({
    items: [box1, box2, box3],
    //将面板显示到HTML中：
    //<div id="ContainerLayout" style="float: left; width: 300px"> ContainerLayout：垂直方式放置 </div>
    renderTo: "ContainerLayout"
  });
});
```



## 5.FormLayout

**介绍**：这个看起来就像表单一样

**效果**：将内部组件竖直方向进行堆叠

### 5.1 展示

![FormLayout](https://gitee.com/Ethanyan/pic_data/raw/c34b732d4c43f42e62044916ec83fd25e9f162da/FormLayout.png)

### 5.2 代码

```js
Ext.onReady(function() {
  var formlayout = new Ext.Panel({
    title: "FormLayout",
    layout: "form",
    items: [
      new Ext.form.TextField({ fieldLabel: "姓名" }),
      new Ext.form.TextField({ fieldLabel: "年级" }),
      new Ext.form.TextField({ fieldLabel: "班级" })
    ],
    //将面板显示到HTML中：
    //<div id="FormLayout" style="float: left; width: 240px; padding-left: 10px;"></div>
    renderTo: "FormLayout"
  });
});
```

## 6.FitLayout

**介绍**：强迫子组件填充满容器的布局

### 6.1 展示

![FitLayout](https://gitee.com/Ethanyan/pic_data/raw/c34b732d4c43f42e62044916ec83fd25e9f162da/FitLayout.png)

### 6.2 代码

```js
Ext.onReady(function() {
  var FitLayout = new Ext.Panel({
    title: "FitLayout",
    height: 100,
    renderTo: "FitLayout",
    layout: "fit",
    items: [
      new Ext.Panel({
        bodyStyle: "background:red",
        html: "使用了fit布局,填充满"
      }),
      new Ext.Panel({
        bodyStyle: "background:yellow",
        html: "这个panel不会显示，因为是fit布局"
      })
    ]
  });
  var NoFitLayout = new Ext.Panel({
    title: "NoFitLayout",
    height: 100,
    //将面板显示到HTML中：
    //<div id="FitLayout" style="width: 300px; float: left; height: 200px; padding-left: 10px;"></div>
    renderTo: "FitLayout",
    items: [
      new Ext.Panel({
        bodyStyle: "background:yellow",
        html: "未使用了fit布局,没有填充满"
      })
    ]
  });
});
```



## 7.TableLayout

**介绍**：表格布局，含有行与列的概念

### 7.1 展示

![TableLayout](https://gitee.com/Ethanyan/pic_data/raw/c34b732d4c43f42e62044916ec83fd25e9f162da/TableLayout.png)

### 7.2 代码

```js
Ext.onReady(function() {
  var TableLayout = new Ext.Panel({
    title: "TableLayout",
    layout: "table",
    layoutConfig: { columns: 3 },
    defaults: {
      width: 133,
      height: 100,
      autoEl: "center"
    },
    defaultType: "panel",
    items: [
      { html: "行1列1" },
      { html: "行1列2" },
      { html: "行[1,2]列3", rowspan: 2, height: 180 },
      { html: "行2列[1,2]", colspan: 2, width: 266 }
    ],
    //将面板显示到HTML中：
    //<div id="TableLayout" style="width: 400px; float: left; padding-left: 10px;"></div>
    renderTo: "TableLayout"
  });
});
```

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***