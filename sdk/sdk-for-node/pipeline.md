# Pipeline 函数

## 初始化

使用以下接口时，请先完成初始化工作，请参考：

{% page-ref page="./" %}

## Pipeline 函数类型

目前共支持三种类型的 pipeline 函数：

| 名称 | 说明 |
| :--- | :--- |
| Pre-Register  Pipeline | 注册前 Pipeline，会在每次用户正式进入注册逻辑前触发，开发者可用此实现注册邮箱白名单、注册 IP 白名单等功能。 |
| Post-Register Pipeline | 注册后Pipeline， 会在每次用户完成注册逻辑 **（但还未保存至数据库）** 之后触发，开发者可用此实现往数据库写入自定义 metadata 、新用户注册 webhook 通知等功能。  |
| Post-Authentication Pipeline | 认证后 Pipeline 会在每次用户完成认证之后触发，开发者可用此实现往 token 加入自定义字段等功能。 |

## 创建 Pipeline 函数 <a id="create-pipeline"></a>

{% hint style="success" %}
一个函数也叫 Rule（规则），所以 SDK 命名方式如 addRule、updateRule 等。
{% endhint %}

Authing.pipeline.createRule\(input\)

* 参数
  * input &lt;object&gt;
    * name: &lt;string&gt; 名称、必填。
    * type: &lt;string&gt; 类型，必填。**可选值见 Pipeline 函数类型**。
    * code: &lt;string&gt; 代码，必填。见 [Pipeline 函数开发指南](../../extensibility/pipeline/pipeline-function-api-doc.md)。
    * description: &lt;string&gt; 描述信息，可选。
* 使用方法

```javascript
const rule = await authing.pipeline.createRule({
  name: "注册白名单",
  code: "------函数代码------",
  type: "PRE_REGISTER"
})
```

* 返回数据
  * \_id: Rule 函数 ID
  * enabled: 是否开启。

```javascript
{
    _id: "5e35841c691196a1ccb5b6f7",
    name: "注册白名单",
    description: "",
    type: "PRE_REGISTER",
    enabled: true,
    faasUrl: "云函数链接",
    code: "代码",
    createdAt: '2020-02-16T20:47:04+08:00',
    updatedAt: '2020-02-16T20:47:04+08:00'
}
```

## 修改 Pipeline 函数 <a id="update-pipeline"></a>

{% hint style="warning" %}
一个用户池最多创建 50 个 Pipeline 函数。
{% endhint %}

Authing.pipeline.updateRule\(input\)

* 参数
  * input &lt;object&gt;
    * \_id: &lt;string&gt;  Rule 函数 ID，必填。
    * name: &lt;string&gt; 名称，选填。
    * type: &lt;string&gt; 类型，选填。**可选值见 Pipeline 函数类型**。
    * code: &lt;string&gt; 代码，选填。见 [Pipeline 函数开发指南](../../extensibility/pipeline/pipeline-function-api-doc.md)
    * description: &lt;string&gt; 描述信息，选填。
    * enabled: &lt;string&gt; 是否开启，选填。
* 使用方法 

```javascript
// 停用此函数
const rule = await authing.pipeline.updateRule({
  _id: "5e35841c691196a1ccb5b6f7",
  enabled: false
})
```

* 返回数据

```javascript
{
    _id: "5e35841c691196a1ccb5b6f7",
    name: "注册白名单",
    description: "",
    type: "PRE_REGISTER",
    enabled: false,
    faasUrl: "云函数链接",
    code: "代码",
    createdAt: '2020-02-16T20:47:04+08:00',
    updatedAt: '2020-02-16T20:47:04+08:00'
}
```

## 根据  id 查询 Pipeline 函数 <a id="pipeline-by-id"></a>

Authing.pipeline.ruleById\(\_id\)

* 参数
  * \_id &lt;string&gt; 必填。
* 使用方法

```javascript
const rule = await authing.pipeline.ruleById("5e35841c691196a1ccb5b6f7")
```

* 返回数据

```javascript
{
    _id: "5e35841c691196a1ccb5b6f7",
    name: "注册白名单",
    description: "",
    type: "PRE_REGISTER",
    enabled: false,
    faasUrl: "云函数链接",
    code: "代码",
    createdAt: '2020-02-16T20:47:04+08:00',
    updatedAt: '2020-02-16T20:47:04+08:00'
}
```

## 删除 Pipeline 函数 <a id="delete-pipeline"></a>

Authing.pipeline.deleteById\(\_id\)

* 参数
  * \_id &lt;string&gt; 函数 ID, 必填。
* 使用方法

```javascript
const res = await authing.pipeline.deleteById("5e35841c691196a1ccb5b6f7")
```

* 返回数据
  * code: 为 200 时表示成功，其他 code 含义见下文**错误代码列表**。

```javascript
{
    code: 200,
    message: "操作成功！"
}
```

## 查询用户池 Pipeline 函数列表 <a id="pipeline-list"></a>

Authing.pipeline.all\(\)

* 参数: 无
* 使用方法

```javascript
const rules = await authing.pipeline.all()
```

* 返回数据
  * totalCount: 总数
  * list: 函数列表

```javascript
{
    totalCount: 1,
    list: [
        {
            _id: "5e35841c691196a1ccb5b6f7",
            name: "注册白名单",
            description: "",
            type: "PRE_REGISTER",
            enabled: false,
            faasUrl: "云函数链接",
            code: "代码",
            createdAt: '2020-02-16T20:47:04+08:00',
            updatedAt: '2020-02-16T20:47:04+08:00'
        }
    ]
}
```

## 设置环境变量 <a id="add-env"></a>

{% hint style="success" %}
使用方法请见：[如何在 Pipeline 函数中使用环境？](../../extensibility/pipeline/env.md)
{% endhint %}

Authing.pipeline.setEnv\(\)

* 参数
  * key: &lt;string&gt; 必填。
  * value: &lt;string&gt; 必填。
* 使用方法

```javascript
const env = await authing.pipeline.setEnv("KEY1","VALUE1")
```

* 返回数据：返回最新的所有环境变量列表。

```javascript
{
    totalCount: 1,
    list: [
        {
            key: "KEY1",
            value: "VALUE1"
        }
    ]
}
```

## 删除环境变量 <a id="remove-env"></a>

Authing.pipeline.removeEnv\(key\)

* 参数
  * key &lt;string&gt; 必填。
* 使用方法

```javascript
const env = await authing.pipeline.setEnv("KEY1")
```

* 返回数据：返回最新的所有环境变量列表。

```javascript
{
    totalCount: 0,
    list: []
}
```

## 查询用户池环境变量列表 <a id="env"></a>

{% hint style="success" %}
环境变量的 scope 为整个用户池，用户池内的所有 Pipeline 函数共用所有环境变量。
{% endhint %}

Authing.pipeline.env\(\)

* 参数：无。
* 使用方法

```javascript
const env = await authing.pipeline.env()
```

* 返回数据

```javascript
{
    totalCount: 1,
    list: [
        {
            key: "KEY1",
            value: "VALUE1"
        }
    ]
}
```

