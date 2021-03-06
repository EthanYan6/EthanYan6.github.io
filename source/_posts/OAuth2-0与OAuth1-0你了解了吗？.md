---
title: OAuth2.0与OAuth1.0你了解了吗？
date: 2020-05-07 18:41:18
tags: oauth
categories: oauth
---

<center>Author：闫玉良</center>

使用了 `OAuth2.0` ，但是你晓得为什么使用 `OAuth2.0` 吗？快来看看吧。

<!--more-->

***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***

## 1.OAuth简介

`OAuth` 协议简单的来说就是第三方应用在不知道我方用户账号密码的情况下，通过我们的授权，进行登录操作。它减少了用户注册的次数，方便用户快捷登录，提升用户体验度，更保障了用户的信息不被泄露。毕竟 `qq/微博` 大部分人都使用，而第三方应用却很少有人使用，用户既可以使用常用的登录方式登录，又不需要担心  `qq/微博`  泄露我们的信息给第三方应用。

## 2.OAuth2.0选择缘由

### 2.1 OAuth1.0

![OAuth1.0过程解析](https://img-blog.csdnimg.cn/20190521200756553.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2l2b2xjYW5v,size_16,color_FFFFFF,t_70)

#### 2.1.1 访问过程

1) 客户端向资源服务器请求未授权 `Request Token`

2) 服务器返回未授权`Request Token`和 `secret`，具体返回的参数为：`oauth_token` 和 `oauth_token_secret`

3) 客户端携带上一步的 `oauth_token` 以及 `oauth_callback` 等参数请求服务器，调起授权页面

4) 用户授权

5) 资源服务器将用户重定向回客户端，并带回授权过的 `Request Token` 也就是 `oauth_token`

6) 获取到授权 `Request Token`，再向资源服务器换取票据  `accessToken` 

7) 通过票据 `accessToken` 访问用户在资源服务器存储的受保护的资源

#### 2.1.2 优点

 `OAuth1.0` 协议每个 `Token` 都会进行加密

####  2.1.3 缺点

1) 用的是 `http` 协议

2) 容易在申请 `RequestToken` 过程中，被攻击者调包，然后伪造回调地址，拿到用户的 `accessToken` 然后为所欲为。

### 2.2 OAuth2.0

![OAuth2.0](https://images0.cnblogs.com/blog2015/545673/201505/221134251825386.png)

#### 2.2.1 访问过程

在 `OAuth2.0` 协议中常用的授权方法有四种：授权码模式、简化模式、密码模式、客户端模式。通过授权码模式进行说明：

1) 引导用户到授权服务器，请求用户授权，用户授权后返回授权码(`Authorization Code`)

2) 客户端由授权码到授权服务器换取访问令牌(`Access Token`)

> 如果需要，开发者可以通过 `refresh_token` 刷新授权 `access_token`，避免过期

3) 用访问令牌去访问得到授权的资源

#### 2.2.2 优点

1) 使用 `https` ，更加安全

2) 所有的 `token` 不再有对应的 `secret` 存在，签名过程简洁

3) 能更好地支持不是基于浏览器的应用

4) 有刷新令牌的机制

#### 2.2.3 缺点

1) `OAuth2.0` 不兼容老版本

2) `OAuth2.0` 的访问令牌，也就是 `access_token` 有有效期

### 2.3 对比

1)  `OAuth2.0` 使用 `https` 的方式更加安全； `OAuth1.0` 虽然所有的 `token` 都会进行加密，但因为使用 `http` 方式而有被劫持攻击风险。

2)  `OAuth2.0` 可以通过多种方式获取访问令牌，考虑到了客户端存在的各种形态，包容性好；而  `OAuth1.0` 只有一种方式

3) `OAuth2.0` 较 `OAuth1.0` 相比，整个授权验证流程更简单更安全（省去了复杂的签名过程）

## 3.其他

1.授权码模式中，为什么不直接获取令牌，而是通过授权码，岂不是多此一举？

答：授权码是为了防止中间者攻击。比如第三方使用 `http` 协议，直接传递令牌，很容易被劫持，有了授权码之后，情况便大不相同。授权码一般有效期十分钟且只能被使用一次，攻击者即使获取到授权码，换到了令牌，当第三方应用也通过授权码获取令牌时，授权服务器就会因为授权码被使用两次而让令牌失效，从而保证安全。

2.为什么授权模式为了避免 `CSRF` 攻击，可以在 `URL` 请求后面加个参数 `state`，这是什么原理？

答：`state` 参数能标记这是来自哪一个网站，且 `state` 参数是攻击者拿不到的，因此可以避免 `CSRF` 攻击。



***更多精彩文章请关注公众号『Pythonnote』或者『全栈技术精选』***