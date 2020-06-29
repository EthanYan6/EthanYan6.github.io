---
title: Extjs-lesson7
tags: extjs
categories: extjs
date: 2020-06-29 21:05:04
---

<center>Author：闫玉良</center>

`Ext.js` 系列课程笔记「Ajax」

<!--more-->

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***

## 1.Ajax

学习一个框架最重要的便是数据交互部分，拒绝一切花里胡哨，直接上示例

### 1.1 展示

![Ajax](https://gitee.com/Ethanyan/pic_data/raw/0b7cb0607e41016236252be5e77d7eb6a616a6ac/ajax.png)

### 1.2 代码

前端 `js` 代码

```js
Ext.onReady(function () {
    //创建panel
    var panel = new Ext.Panel({
        title: 'Ajax与数据显示',
        width: 476,
        height: 374,
        resizable: true,
        frame: true
    });
    //创建数据显示模板
    var template = new Ext.XTemplate(
                '<table  id="template">',
                   '<tr><td>编号:</td><td>{id}</td></tr>',
                   '<tr><td>姓名:</td><td>{name}</td></tr>',
                   '<tr><td>生日:</td><td>{brithday}</td></tr>',
                   '<tr><td>身高:</td><td>{height}</td></tr>',
                   '<tr><td>性别:</td><td>{sex}</td></tr>',
                   '<tr><td valign="top">描述:</td><td>{discribe}</td></tr>',
                '</table>'
            );
    //获取数据
    Ext.Ajax.request({
        url: '/demo/people/',
        method: 'post',
        params: { id: 1 },
        success: function (response, options) {
            for (i in options) {
                alert('options参数名称:' + i);
                alert('options参数[' + i + ']的值：' + options[i]);
            }
            var responseJson = Ext.util.JSON.decode(response.responseText);
            template.compile();
            template.overwrite(panel.body, responseJson);
        },
        failure: function () {
            alert('系统出错，请联系管理人员！');
        }
    });
    //创建窗体
    var win = new Ext.Window({
        title: '窗口',
        id: 'window',
        width: 476,
        height: 374,
        resizable: true,
        modal: true,
        closable: true,
        maximizable: true,
        minimizable: true,
        items: [panel]
    });
    win.show();
});
```

后端 `python` 代码：

```python
from django.http import JsonResponse
from django.shortcuts import render
from django.shortcuts import render_to_response

# url:/demo/people/
def getPeople(request):
    id = request.POST.get('id')
    if id == 1:
        return JsonResponse({"id": 1, "name": '小闫同学', "brithday": "2001-05-21", "height": 180, "sex": '0', "discribe": '小闫同学是一名全栈开发工程师<br>欢迎关注公众号：全栈技术精选。'})

# url:/demo/index
def index(request):
    return render_to_response('test_ajax/index.html')
```



***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***