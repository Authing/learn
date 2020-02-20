---
description: 'context 对象中保存了当前认证流程的上下文，包括认证手段、身份提供方，以及请求 IP, 地址等。'
---

# context 对象

## 属性 <a id="variables"></a>

| 属性名 | 类型 | 说明 |
| :--- | :--- | :--- |
| protocol | string | 认证协议。具体说明见下文。 |
| connection | string | 身份提供方。具体说明见下文。 |
| ldapConfiguration | object | ldap 配置，在为 ldap 认证的情况下带有。具体说明见下文。 |
| samlConfiguration | object | saml 配置，在为 saml 认证的情况下带有。具体说明见下文。 |
| data | object | GraphQL POST 请求数据。如在注册前 Pipeline 中，开发者可以获取邮箱、手机号、注册方式等信息。**详细字段请见下文**。 |
| headers | object | 原始请求头信息。 |
| userPool | object | 用户池详细信息。处于安全考虑，这里不包含 secret 和  token。具体说明见下文。 |
| ip | string | 请求者 IP。 |
| geo | object | 包含了请求者的地址信息以及经纬度坐标。具体说明见下文。 |

## protocol

认证协议。

| 可能值 | 说明 |
| :--- | :--- |
| basic | 普通认证模式，基于用户名密码、手机号验证码。 |
| social | 社会化登录认证默认，当使用 Authing 集成的社会化登录手段登录时为此值。 |
| ldap | 使用 LDAP 进行认证。有关如何接入 LDAP 请见 [配置 LDAP 服务](../../advanced/ldap.md)。 |
| saml | 使用 SAML 进行认证。有关如何接入 SAML 请见 [接入 SAML](../../advanced/use-saml/)。 |

## connection

身份提供方。

<table>
  <thead>
    <tr>
      <th style="text-align:left">&#x53EF;&#x80FD;&#x503C;</th>
      <th style="text-align:left">&#x8BF4;&#x660E;</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">authing</td>
      <td style="text-align:left">&#x5982;&#x4F7F;&#x7528;&#x7528;&#x6237;&#x540D;&#x5BC6;&#x7801;&#x3001;&#x624B;&#x673A;&#x53F7;&#x9A8C;&#x8BC1;&#x7801;&#xFF0C;&#x57FA;&#x4E8E;
        Authing &#x7684;&#x6570;&#x636E;&#x5E93;&#x8FDB;&#x884C;&#x9A8C;&#x8BC1;&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">ldap:&lt;LDAP&#x670D;&#x52A1;ID&gt;</td>
      <td style="text-align:left">&#x4F7F;&#x7528; lDAP &#x534F;&#x8BAE;&#x8FDB;&#x884C;&#x767B;&#x5F55;&#x3002;
        &#x4E00;&#x4E2A; Authing LDAP &#x670D;&#x52A1;&#x5BF9;&#x5E94;&#x4E00;&#x4E2A;
        Authing &#x7528;&#x6237;&#x76EE;&#x5F55;&#x6216;&#x7B2C;&#x4E09;&#x65B9;&#x7528;&#x6237;&#x76EE;&#x5F55;&#xFF0C;&#x8BE6;&#x60C5;&#x8BF7;&#x89C1;
        <a
        href="../../advanced/ldap.md">&#x914D;&#x7F6E; LDAP&#x670D;&#x52A1;</a>&#x548C; <a href="../../advanced/ldap-idp.md">&#x4F7F;&#x7528; Authing &#x7684; LDAP &#x7528;&#x6237;&#x76EE;&#x5F55;</a>&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">saml:&lt;SAML Idp &#x670D;&#x52A1; ID&gt;</td>
      <td style="text-align:left">&#x4F7F;&#x7528; SAML &#x534F;&#x8BAE;&#x8FDB;&#x884C;&#x767B;&#x5F55;&#x3002;&#x6709;&#x5173;&#x5982;&#x4F55;&#x63A5;&#x5165;
        SAML &#x8BF7;&#x89C1; <a href="../../advanced/use-saml/">&#x63A5;&#x5165; SAML</a>&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">social:github</td>
      <td style="text-align:left">&#x4F7F;&#x7528; GitHub &#x767B;&#x5F55;&#x3002;&#x8BE6;&#x60C5;&#x89C1;
        <a
        href="../../advanced/social-login/">&#x63A5;&#x5165;&#x793E;&#x4F1A;&#x5316;&#x767B;&#x5F55;</a>&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p></p>
        <p>social:wechat-pc</p>
      </td>
      <td style="text-align:left">&#x5FAE;&#x4FE1; PC &#x626B;&#x7801;&#x767B;&#x5F55;&#x3002;&#x8BE6;&#x60C5;&#x89C1;
        <a
        href="../../advanced/social-login/">&#x63A5;&#x5165;&#x793E;&#x4F1A;&#x5316;&#x767B;&#x5F55;</a>&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">social:wechat-miniprogram</td>
      <td style="text-align:left">&#x4F7F;&#x7528;&#x5FAE;&#x4FE1;&#x5C0F;&#x7A0B;&#x5E8F;&#x767B;&#x5F55;&#x3002;&#x8BE6;&#x60C5;&#x89C1;
        <a
        href="../../advanced/social-login/jie-ru-xiao-cheng-xu-ke-hu-duan-deng-lu.md">&#x63A5;&#x5165;&#x5FAE;&#x4FE1;&#x5C0F;&#x7A0B;&#x5E8F;&#x767B;&#x5F55;</a>&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">social:wechat-app</td>
      <td style="text-align:left">&#x4F7F;&#x7528;&#x5FAE;&#x4FE1;&#x79FB;&#x52A8;&#x7AEF; APP &#x767B;&#x5F55;&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">social:wechat-officialaccount-web</td>
      <td style="text-align:left">&#x4F7F;&#x7528;&#x5FAE;&#x4FE1;&#x516C;&#x4F17;&#x53F7;&#x7F51;&#x9875;&#x6388;&#x6743;&#x767B;&#x5F55;&#x3002;&#x8BE6;&#x60C5;&#x89C1;
        <a
        href="../../advanced/social-login/wechatmp-login.md">&#x63A5;&#x5165;&#x5FAE;&#x4FE1;&#x516C;&#x4F17;&#x53F7;&#x7F51;&#x9875;&#x6388;&#x6743;&#x767B;&#x5F55;</a>&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">social:qq-web</td>
      <td style="text-align:left">&#x4F7F;&#x7528; QQ &#x7F51;&#x9875;&#x7248;&#x767B;&#x5F55;&#x3002;&#x8BE6;&#x60C5;&#x89C1;
        <a
        href="../../advanced/social-login/">&#x63A5;&#x5165;&#x793E;&#x4F1A;&#x5316;&#x767B;&#x5F55;</a>&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">social:weibo-web</td>
      <td style="text-align:left">&#x4F7F;&#x7528;&#x5FAE;&#x535A;&#x7F51;&#x9875;&#x7248;&#x767B;&#x5F55;&#x3002;&#x8BE6;&#x60C5;&#x89C1;
        <a
        href="../../advanced/social-login/">&#x63A5;&#x5165;&#x793E;&#x4F1A;&#x5316;&#x767B;&#x5F55;</a>&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">social:dingtalk-web</td>
      <td style="text-align:left">&#x4F7F;&#x7528;&#x9489;&#x9489;&#x7F51;&#x9875;&#x7248;&#x767B;&#x5F55;&#x3002;&#x8BE6;&#x60C5;&#x89C1;
        <a
        href="../../advanced/social-login/">&#x63A5;&#x5165;&#x793E;&#x4F1A;&#x5316;&#x767B;&#x5F55;</a>&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">social:alipay-mobile</td>
      <td style="text-align:left">&#x4F7F;&#x7528;&#x652F;&#x4ED8;&#x5B9D; APP &#x767B;&#x5F55;&#x3002;&#x8BE6;&#x60C5;&#x89C1;
        <a
        href="../../advanced/social-login/jie-ru-yi-dong-duan-zhi-fu-bao-deng-lu.md">&#x63A5;&#x5165;&#x652F;&#x4ED8;&#x5B9D;&#x79FB;&#x52A8;&#x7AEF;&#x767B;&#x5F55;</a>&#x3002;</td>
    </tr>
  </tbody>
</table>## data 对象 <a id="data-object"></a>

注册前、注册后 Pipeline 中 data 对象部分请求字段如下：详情请见 [Authing GraphQL 调试器](https://authing.cn/graphiql/) **用户鉴权** - **注册**接口。

{% hint style="warning" %}
email, phone, unionid 等字段不一定同时存在，使用前请先判断其是否存在！如 

```javascript
const email = context.data.userInfo.email
if(email) {
    // 表示是用邮箱注册
    // 可以进行邮箱注册白名单的逻辑
    
    if(!email.endsWith('example.com')){
        return callback(new Error('Access Denied!'))
    }
}
```
{% endhint %}

| 字段名 | 类型 | 说明 |
| :--- | :--- | :--- |
| userInfo | object |  用户注册所填的数据，如邮箱、手机号、浏览器 UA 等。 |
| userInfo.registerInClient | string |  注册用户池 |
| userInfo.registerMethod | string | 注册方式。若开发者自己使用  SDK 开发使用此接口，最好提供注册方式。如未提供，Authing 将从其他是否提供 `unionid`，`email`, `phone`， `username` 四个字段推测注册方式（优先级递减，比如同时提供 `unionid` 和 `email`，会被视为 `unionid` 方式注册）。 |
| userInfo.unionid | string | 社会化登录的 unionid。 |
| userInfo.email | string | 邮箱。 |
| userInfo.phone | string | 手机号。 |
| userInfo.photo | string | 用户头像。 |

认证后 Pipeline 中 data 对象部分请求字段如下：详情请见 [Authing GraphQL 调试器](https://authing.cn/graphiql/) **用户鉴权** - **登录**接口。

{% hint style="warning" %}
这些字段不一定全部存在，使用前请先判断是否存在！
{% endhint %}

| 字段名 | 类型 | 说明 |
| :--- | :--- | :--- |
| registerInClient | string | 用户池 ID |
| phone | string | 手机号 |
| phoneCode | string | 手机号验证码 |
| unionid | string | 社会化登录  unionid |
| openid | string | 社会化登录 openid |
| email | string | 邮箱 |
| lastIP | string | 登录  IP |
| browser | string | 浏览器 |

## userPool 对象 <a id="userpool-object"></a>

| 字段名 | 类型 | 说明 |
| :--- | :--- | :--- |
| \_id | string | 用户池 ID |
| name | string | 用户池名称 |
| logo  | string | 用户池图标 |
| registerDisabled | boolean | 是否关闭注册 |
| userPoolTypeList | array | 用户池类型列表。每个类型内容如下： |

```javascript
{
      "_id": "5e193ceb71edb858dc54b8ef",
      "name": 'Web',
      "description": 'Web Application',
      "example": 'Web SDK',
      "image": ''
}
```

## geo 属性 <a id="geo"></a>

示例数据：

```javascript
{
	"address": "湖南省长沙市",
	"address_detail": {
		"city": "长沙市",
		"city_code": "430100",
		"district": "",
		"province": "湖南省",
		"street": "",
		"street_number": ""
	},
	"point": {
		"x": "112.6534116",
		"y": "27.96920845"
	}
}
```



