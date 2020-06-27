---
title: Extjs-lesson4
date: 2020-06-27 11:12:04
tags: extjs
categories: extjs
---

<center>Author：闫玉良</center>

`Ext.js` 系列课程笔记「表单子项」

<!--more-->

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***

## 1.TextField

### 1.1 展示

![TextField](https://gitee.com/Ethanyan/pic_data/raw/master/TextField.png)

### 1.2 代码

> 窗体 `Window` 以及表单 `FormPanel` 的代码省略，以后的代码将只展示必要部分。

```js
// 初始化提示信息，让下面的框框显示提示动作
Ext.QuickTips.init();
// 提示的方式在框框右边缘，参数的值有："qtip","title","under","side",id(元素id)
// side方式用的较多，右边出现红色感叹号，鼠标上去出现错误提示。
Ext.form.Field.prototype.msgTarget = "side";
// 用户名
// 创建一个新的TextField文本框对象
var txtusername = new Ext.form.TextField({
  // 宽
  width: 140,
  // 不允许文本框为空
  allowBlank: false,
  // 文本框的最大长度为 20 个字符；超过 20 个字符仍然可以输入，但会有警告信息
  maxLength: 20,
  // 该 TextField 的名称
  name: "username",
  // 文本框前面显示的文字
  fieldLabel: "用户名称",
  // 当非空校验没有通过时的提示信息
  blankText: "请输入用户名",
  // 当最大长度校验没有通过时的提示信息
  maxLengthText: "用户名不能超过20个字符"
});
// 密码input
// 创建一个新的TextField文本框对象
var txtpassword = new Ext.form.TextField({
  width: 140,
  allowBlank: false,
  maxLength: 20,
  // 字段类型，默认是 text，填写为 password 时密码会加密显示
  inputType: "password",
  name: "password",
  fieldLabel: "密码",
  blankText: "请输入密码",
  maxLengthText: "密码不能超过20个字符"
});
```



## 2.Button

### 2.1 展示

![Button](https://gitee.com/Ethanyan/pic_data/raw/master/Button.png)

### 2.2 代码

```js
//提交按钮处理方法
var btnsubmitclick = function() {
  Ext.MessageBox.alert("提示", "你点了确定按钮!");
};
//重置按钮"点击时"处理方法
var btnresetclick = function() {
  Ext.MessageBox.alert("提示", "你点了重置按钮!");
};
//重置按钮"鼠标悬停"处理方法
var btnresetmouseover = function() {
  Ext.MessageBox.alert("提示", "你鼠标悬停在重置按钮之上!");
};
//提交按钮
//创建一个新的Button按钮对象
var btnsubmit = new Ext.Button({
  text: "提交",
  //当用户点击的时候[即js中的onclick事件]执行方法btnsubmitclick。
  handler: btnsubmitclick
});
//重置按钮
//创建一个新的Button按钮对象
var btnreset = new Ext.Button({
  text: "重置",
  //事件监听，按顺序执行。填写格式：事件名: 处理函数
  listeners: {
    //鼠标悬停时执行方法btnresetmouseover
    mouseover: btnresetmouseover,
    //当用户点击的时候[即js中的onclick事件]执行方法btnresetclick
    click: btnresetclick
  }
});
```

> `handler` 是一个特殊的 `listeners` ，执行的是首发事件。

## 3.NumberField

### 3.1 展示

![NumberField](https://gitee.com/Ethanyan/pic_data/raw/master/NumberField.png)

### 3.2 代码

```js
//创建一个NumberField数字文本框对象
var numberfield = new Ext.form.NumberField({
  //文本框前面的显示文字（标签）
  fieldLabel: "身高",
  //宽度
  width: 80,
  //设置小数点后面的位数，当位数超过时系统会自动截断
  decimalPrecision: 1,
  //设置数字文本框最小值
  minValue: 0.01,
  //设置数字文本框最大值
  maxValue: 200,
  //这个是自己添加的属性，属性添加可以参看重写文本框部分代码
  unitText: " cm",
  //不允许为空
  allowBlank: false,
  //如果校验为空时的提示文字
  blankText: "请输入身高"
});
```

#### 3.2.1 重写文本框

```js
//重写Ext.form.TextField这个类
Ext.override(Ext.form.TextField, {
  unitText: '',
  //重写 onRender 方法
  onRender: function (ct, position) {
    Ext.form.TextField.superclass.onRender.call(this, ct, position);
    // 如果单位字符串已定义 则在后方增加单位对象
    if (this.unitText != '') {
      this.unitEl = ct.createChild({
        tag: 'div',
        html: this.unitText
      });
      // 在样式表中加了样式"x-form-unit"。
      this.unitEl.addClass('x-form-unit');
      // 增加单位名称的同时 按单位名称大小减少文本框的长度 初步考虑了中英文混排 未考虑为负的情况
      this.width = this.width - (this.unitText.replace(/[^\x00-\xff]/g, "xx").length * 6 + 2);
      // 同时修改错误提示图标的位置
      this.alignErrorIcon = function () {
        this.errorIcon.alignTo(this.unitEl, 'tl-tr', [2, 0]);
      };
    }
  }
});
```



## 4.Hidden

> 毕竟是隐藏字段，顾名思义不可见，因此无展示图例

### 4.1 代码

```js
//新建一个Hidden隐藏字段对象
var hiddenfield = new Ext.form.Hidden({
  //名称，后端收到的信息为 userid: 1
  name: "userid",
  //值
  value: "1"
});
```



## 5.DateField

### 5.1 展示

![DateField](https://gitee.com/Ethanyan/pic_data/raw/master/NumberField.png)

### 5.2 代码

```js
//新建一个 DateField 日期字段对象
var datefield = new Ext.form.DateField({
  //文本框前方显示文字（标签）
  fieldLabel: "出生日期",
  //日期显示格式
  format: "Y-m-d",
  //是否可编辑
  editable: false,
  //是否可以为空
  allowBlank: false,
  //校验为空时提示信息
  blankText: "请选择日期"
});
```



## 6.RadioGroup

### 6.1 展示

![单选组](https://gitee.com/Ethanyan/pic_data/raw/master/checkbox.png)

### 6.2 代码

```js
//创建一个RadioGroup对象
var radiogroup = new Ext.form.RadioGroup({
  //单选框前面的显示文字信息
  fieldLabel: "性别",
  //宽度
  width: 100,
  items: [
    {
      //这个属性是设置单选的关键
      //只有同一组 name 的单选按钮才能单选
      name: "sex",
      //单选框的值
      inputValue: "0",
      //单选框的文字说明
      boxLabel: "男",
      //默认勾选男，哪个选项默认勾选，就在哪个选项中添加此属性
      checked: true
    },
    {
      //与上方 name 设置一致
      name: "sex",
      //单选框的值
      inputValue: "1",
      //单选框的文字说明
      boxLabel: "女"
    }
  ]
});
//获取单选组的值
radiogroup.on("change", function(rdgroup, checked) {
  //获取单选框的选中值
  alert(checked.getRawValue());
});
```



## 7.CheckBoxGroup

### 7.1 展示

![复选组](https://gitee.com/Ethanyan/pic_data/raw/master/checkbox.png)

### 7.2 代码

```js
//创建一个复选组的对象
var checkboxgroup = new Ext.form.CheckboxGroup({
  //复选组前文字提示信息
  fieldLabel: "兴趣爱好",
  //宽度
  width: 170,
  //元素
  items: [
    {
      //选择框的文字说明
      boxLabel: "抽烟",
      //选择框的值
      inputValue: "0"
    },
    {
      //选择框的文字说明
      boxLabel: "喝酒",
      //选择框的值
      inputValue: "1"
    },
    {
      //选择框的文字说明
      boxLabel: "烫头",
      //选择框的值
      inputValue: "2"
    }
  ]
});
//获取复选组的值
checkboxgroup.on("change", function(cbgroup, checked) {
  //因为是多选，所以需要循环取出所有勾选的值
  for (var i = 0; i < checked.length; i++) {
    alert(checked[i].getRawValue());
  }
});
```



## 8.Combobox

### 8.1 展示

![Combobox](https://gitee.com/Ethanyan/pic_data/raw/master/%E4%B8%8B%E6%8B%89%E6%A1%86.png)

### 8.2 代码

> `Ext.js2.3` 中没有 `ArrayStore` ，我们使用 `Store` 替代

```js
//创建数据源[数组数据源]
var combostore = new Ext.data.Store({
  // 数据本身，数组
  data: [
    [1, "团员"],
    [2, "党员"],
    [3, "其他"]
  ],
  // 从上面数组中读取数据时，字段与数据一一对应解释为 Extjs 使用的数据
  // 参数为 id 列，以及其他各个字段的名称
  reader: new Ext.data.ArrayReader({ id: "id" }, ["id", "name"])
});
//创建Combobox
var combobox = new Ext.form.ComboBox({
  //下拉框前的提示文字信息
  fieldLabel: "政治面貌",
  //数据源；此属性必填
  store: combostore,
  //显示的列，对应数据源的 name 列；此属性必填
  displayField: "name",
  //对应数据源中 id 列的值；此属性必填
  valueField: "id",
  //请设置为”all”,否则默认为”query”的情况下，你选择某个值后，再此下拉时，只出现匹配选项。而 all 则每次都显示全部
  triggerAction: "all",
  //未填时显示的提示信息
  emptyText: "请选择...",
  //不允许为空
  allowBlank: false,
  //校验为空时提示信息
  blankText: "请选择政治面貌",
  //不可编辑
  editable: false,
  //指定数据源为本地数据源，如果是本地创建的数据源，该属性也是必须的
  //设置为'remote'表示数据源来自于服务器
  mode: "local"
});
//Combobox获取值
//选择时alert出下拉列表的值
combobox.on("select", function() {
  alert(combobox.getValue());
});
```



***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***