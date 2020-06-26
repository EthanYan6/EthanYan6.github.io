---
title: Extjs-lesson3
date: 2020-06-26 15:59:24
tags: extjs
categories: extjs
---

<center>Author：闫玉良</center>

`Ext.js` 系列课程笔记「组件」

<!--more-->

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***

## 1.Ext.Window

### 1.1 展示

![演示窗口](https://gitee.com/Ethanyan/pic_data/raw/master/%E6%BC%94%E7%A4%BA%E7%AA%97%E5%8F%A3.png)

### 1.2 代码

```js
(function() {
  Ext.onReady(function() {
    var win = new Ext.Window({
      title: "演示窗口",
      width: 600,
      height: 300,
      modal: true,
      closable: true,
      maximizable: true,
      minimizable: true,
      bodyStyle: "padding:30px",
      html: '<h1 style="text-align: center">欢迎关注：全栈技术精选</h1>'
    });
    win.show();
  });
})();
```

### 1.3 详解

介绍几个常用的**配置项**：

`title` ：标题栏显示文字

`width` ：窗口宽度

`height` ：窗口高度

`modal` ：是否设置为模态窗体

> 模态窗体：打开此窗体后，不能对其他窗体进行操作

`closable` ：是否显示关闭按钮

`maximizable` ：是否显示最大化按钮

`minimizable` ：是否显示最小化按钮

`html` ：窗口显示的内容

`resizable` ：用户是否可以调整窗口的大小

`bodyStyle` ：窗口内容与边框的间距

`buttonAlign` ：窗口中 `button` 的对齐方式（`left`/`center`/`right`）默认是 `right`

介绍几个**方法**：

`show` ：窗口显示

`hide` ：窗口隐藏

`close` ：窗口关闭

### 1.4 学习方法

学习的方法就是通过官方的 `API` 文档，上篇文章介绍了使用方法，下面再提供一副图片详细介绍每个类的说明如何查看：

![类说明](https://gitee.com/Ethanyan/pic_data/raw/master/api%20%E6%96%87%E6%A1%A3%E8%AF%B4%E6%98%8E.png)

文档地址为「https://www.pythonnote.cn/OfficialDocuments/」，可点击跳转文档页面。

> 小闫同学将开发工程师常用的官方文档进行了汇总，文档会不定期补充更新，请收藏或转发给好友。

![document](https://gitee.com/Ethanyan/pic_data/raw/master/%E5%AE%98%E6%96%B9%E6%96%87%E6%A1%A3.png)

## 2.Ext.FormPanel

### 2.1 展示

![表单](https://gitee.com/Ethanyan/pic_data/raw/master/%E8%A1%A8%E5%8D%95.png)

### 2.2 代码

```js
(function() {
  Ext.onReady(function() {
    // 初始化提示信息设置，比如图片中错误提示信息`This field is required`
    Ext.QuickTips.init();
    // 创建一个表单面板对象
    var movie_form = new Ext.FormPanel({
      // 表单提交的地址
      url: "/movie_submit",
      // 将该组件放置在页面的 body 标签中
      renderTo: document.body,
      // 如果为True，则使用自定义的圆形边框渲染面板，如果为false，则使用纯1px正方形边框渲染（默认为false）。
      frame: true,
      // 面板的标题栏文字
      title: "Movie Information Form",
      // 宽度
      width: 250,
      // 表单内容
      items: [
        {
          // 该元素是文本字段
          xtype: "textfield",
          // 输入框前显示内容
          fieldLabel: "Title",
          // 选项名称
          name: "title",
          // 校验不许为空
          allowBlank: false
        },
        {
          xtype: "textfield",
          fieldLabel: "Director",
          name: "director",
          // 校验内容只能是英文字母
          vtype: "alpha"
        },
        {
          // 该元素是时间字段
          xtype: "datefield",
          fieldLabel: "Released",
          name: "released",
          // 校验不能选择周一到周五的日期
          disabledDays: [1, 2, 3, 4, 5]
        }
      ],
      // 按钮
      buttons: [
        {
          // 按钮显示文本
          text: "Save",
          // 按钮的处理函数
          handler: function() {
            // 将表单内容提交
            movie_form.getForm().submit({
              // 成功后的处理逻辑
              success: function(f, a) {
                Ext.Msg.alert("Success", "It worked");
              },
              // 失败后的处理逻辑
              failure: function(f, a) {
                Ext.Msg.alert("Warning", "Error");
              }
            });
          }
        },
        {
          text: "Reset",
          handler: function() {
            // 将表单内容重置
            movie_form.getForm().reset();
          }
        }
      ]
    });
    movie_form.show();
  });
})();

```

## 3.Ext.grid.GridPanel

### 3.1 展示

![GridPanel](https://gitee.com/Ethanyan/pic_data/raw/master/gridpanel.png)

### 3.2 代码

```js
(function() {
  Ext.onReady(function() {
    // 创建数据源
    var mystore = new Ext.data.Store({
      // 数据本身，数组
      data: [
        [1, "Office Space", "Mike Judge", "1999-02-19", 1, "Work Sucks", "19.95", 1],
        [3, "Super Troopers2", "Jay Chandrasekhar", "2002-02-15", 1, "Altered State Police", "14.95", 1],
        //...more rows of data removed for readability...//
      ],
      // 从上面数组中读取数据时，字段与数据一一对应解释为 Extjs 使用的数据
      // 参数为 id 列，以及其他各个字段的名称
      reader: new Ext.data.ArrayReader({ id: "id" }, [
        "id",
        "title",
        "director",
        { name: "released", type: "date", dateFormat: "Y-m-d" },
        "genre",
        "tagline",
        "price",
        "available"
      ])
    });
    // 创建一个 GridPanel 对象
    var grid = new Ext.grid.GridPanel({
      // 数据再页面中的 body 标签中显示
      renderTo: document.body,
      // 如果为True，则使用自定义的圆形边框渲染面板，如果为false，则使用纯1px正方形边框渲染（默认为false）
      frame: true,
      // 标题栏显示文字
      title: "Movie Database",
      // 高度
      height: 200,
      // 宽度
      width: 500,
      // 数据源
      store: mystore,
      // 指定表格的表头
      columns: [
        { header: "Id", dataIndex: "id" },
        { header: "Title", dataIndex: "title" },
        { header: "Director", dataIndex: "director" },
        {
          header: "Released",
          dataIndex: "released",
          renderer: Ext.util.Format.dateRenderer("m/d/Y")
        },
        { header: "Genre", dataIndex: "genre" },
        { header: "Tagline", dataIndex: "tagline" }
      ]
    });
    // 展示表格面板
    grid.show();
  });
})();

```

## 4.Ext.tree.TreePanel

### 4.1 展示

![TreePanel](https://gitee.com/Ethanyan/pic_data/raw/master/treepanel.png)

### 4.2 代码

```js
(function() {
  Ext.onReady(function() {
    // 树的节点数据源
    var node = {
      text: "根",
      // 是否展开子节点
      expanded: true,
      // 是否为叶子节点
      leaf: false,
      children: [
        {
          text: "根下节点一[user图标]",
          leaf: true,
          // ExtJs自带的图标显示为“文件夹”或是“列表”，通过 iconCls 可以列换树型菜单的图标。
          iconCls: "nodeicon"
        },
        { text: "根下节点二", leaf: true },
        {
          text: "根下节点三",
          leaf: false,
          children: [
            { text: "节点三子节点一", leaf: true },
            {
              text: "节点三子节点二",
              leaf: false,
              expanded: true,
              children: [
                {
                  text: "节点三子节点二节点一",
                  leaf: true
                },
                {
                  text: "节点三子节点二节点二",
                  leaf: true
                }
              ]
            }
          ]
        }
      ]
    };
    // 树面板(本地数据源)
    // 创建一个新的TreePanel表单对象
    var treelocal = new Ext.tree.TreePanel({
      title: "TreePanelLocal",
      //rootVisible: false, --> 根节点的可见性
      // 根节点
      root: node
    });
    // 树面板(服务器数据源)
    // 创建一个新的TreePanel表单对象
    var treeservice = new Ext.tree.TreePanel({
      title: "TreePanelService",
      root: { text: "根", expanded: true },
      // 树的数据载入组件,通过 url 寻找服务器返回的 json 数据，并且自动转换成 TreeNode
      loader: new Ext.tree.TreeLoader({
        url: "/App_Ashx/Demo/Tree.ashx"
      })
    });
    // 窗体
    var win = new Ext.Window({
      title: "窗口",
      width: 476,
      height: 574,
      // 用户是否可以调整窗口的大小
      resizable: true,
      // 是否设置为模态窗体
      modal: true,
      // 是否显示关闭按钮
      closable: true,
      // 是否显示最大化按钮
      maximizable: true,
      // 是否显示最小化按钮
      minimizable: true,
      // 窗口中 `button` 的对齐方式（`left`/`center`/`right`）默认是 `right`
      buttonAlign: "center",
      items: [treelocal, treeservice],
      buttons: [
        {
          text: "获取选中项",
          handler: function() {
            // 获取选中节点
            selectNode = treelocal.getSelectionModel().getSelectedNode();
            alert(
              "TreePanelLocal：" +
              // 如果节点为空时显示根节点的文本，否则显示选中的节点文本信息
                (selectNode == null ? treelocal.root.text : selectNode.text)
            );
          }
        }
      ]
    });
    win.show();
  });
})();
```



***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***