---
title: Extjs-lesson5
date: 2020-06-27 15:28:18
tags: extjs
categories: extjs
---

<center>Author：闫玉良</center>

`Ext.js` 系列课程笔记「表单子项二」

<!--more-->

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***



## 9.下拉菜单二级联动

### 9.1 展示

![Combobox_Two](https://gitee.com/Ethanyan/pic_data/raw/07b7355a50ae630e96605fa2869ccc2fcb319e9a/%E4%BA%8C%E7%BA%A7%E8%81%94%E5%8A%A8.png)

### 9.2 代码

```js
//创建市数据源
var combocitystore = new Ext.data.Store({
  //设定读取的代理，添加后端地址
  proxy: new Ext.data.HttpProxy({ url: "/demo/city" }),
  //设定读取的格式
  //读取json返回值 - 根节点为data，对象列为id和name
  //服务器返回数据：{"data": [{"id": 1, "name": "北京"},{"id": 2, "name":"上海"}]}
  reader: new Ext.data.JsonReader({ root: "data" }, [
    { name: "id" },
    { name: "name" }
  ])
});
//创建区数据源
var comboareastore = new Ext.data.Store({
  //设定读取的代理，添加后端地址
  proxy: new Ext.data.HttpProxy({ url: "/demo/area" }),
  //读取json返回值 - 根节点为data，对象列为id和name
  //服务器返回数据示例：{"data": [{"id": 1, "name": "东城区"},{"id": 2, "name": "西城区"},{"id": 2, "name": "海淀区"}]}
  reader: new Ext.data.JsonReader({ root: "data" }, [
    { name: "id" },
    { name: "name" }
  ])
});
//创建市Combobox
var comboboxcity = new Ext.form.ComboBox({
  id: "comboboxcity",
  //框前面的文字（标签）
  fieldLabel: "市",
  //宽度
  width: 120,
  //数据源为市数据源
  store: combocitystore,
  //显示的列
  displayField: "name",
  //对应的值列
  valueField: "id",
  //请设置为”all”,否则默认为”query”的情况下，你选择某个值后，再此下拉时，只出现匹配选项
  triggerAction: "all",
  //默认显示提示文字
  emptyText: "请选择...",
  //不允许为空
  allowBlank: false,
  //校验为空时的提示文字
  blankText: "请选择市",
  //不可编辑
  editable: false,
  mode: "local", //该属性和以下方法为了兼容ie8
  listeners: {
    render: function() {
      combocitystore.load();
    }
  }
});

//创建区Combobox
var comboareacity = new Ext.form.ComboBox({
  //框前面的文字（标签）
  fieldLabel: "区",
  //宽度
  width: 120,
  //数据源为区数据源
  store: comboareastore,
  //显示的列
  displayField: "name",
  //对应的值列
  valueField: "id",
  //请设置为”all”,否则默认为”query”的情况下，你选择某个值后，再此下拉时，只出现匹配选项
  triggerAction: "all",
  //默认显示提示文字
  emptyText: "请选择...",
  //不允许为空
  allowBlank: false,
  //校验为空时的提示文字
  blankText: "请选择区",
  //不可编辑
  editable: false
});
//联动的实现
comboboxcity.on("select", function() {
  //注意等号左边comboareastore是区的数据源,当市变化时，给区的数据源加上个向service端发送的参数
  comboareastore.baseParams.id = comboboxcity.getValue();
  //把区的下拉列表设置为空，由于非空验证，Ext会提示用户“请选择区”
  comboareacity.setValue("");
  //区的数据源重新加载
  comboareastore.load();
});
```



## 10.FileUploadField

### 10.1 展示

![File](https://gitee.com/Ethanyan/pic_data/raw/cbb3848f687e75d1ebf3a5598a1dac5ea09adb21/fileupload.png)

### 10.2 代码

```js
Ext.onReady(function() {
  //初始化提示信息
  Ext.QuickTips.init();
	//定义函数，弹窗
  var msg = function(title, msg) {
    Ext.Msg.show({
      //消息框的标题栏文字
      title: title,
      //提示信息
      msg: msg,
      //宽度
      minWidth: 200,
      //模态窗体：打开此窗体后，不能对其他窗体进行操作
      modal: true,
      //显示图标
      icon: Ext.Msg.INFO,
      //显示 ok 按钮
      buttons: Ext.Msg.OK
    });
  };

  // 文件上传框
  var fibasic = new Ext.form.FileUploadField({
    //传到html中的标签中：<div id="fi-basic"></div>
    renderTo: "fi-basic",
    //宽
    width: 400
  });
	//创建一个按钮
  new Ext.Button({
    //按钮显示文本
    text: "Get File Path",
    //传到HTML中的标签中：<div id="fi-basic-btn"></div>
    renderTo: "fi-basic-btn",
    //处理函数
    handler: function() {
      //获取 id 为 fibasic 的值
      var v = fibasic.getValue();
      //使用 msg 函数，如果文件上传框中有值，那么显示文件的路径；如果没有值，显示 None
      msg("Selected File", v && v != "" ? v : "None");
    }
  });
});

```



***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***