# 自定义用户字段

{% hint style="success" %}
用户自定义 Metadata 是除了 Authing 基础用户字段之外，开发者可以给用户添加的额外字段，属于 Authing 扩展能力的一部分。
{% endhint %}

## 初始化 <a id="init-sdk"></a>

使用以下接口时，请先完成初始化工作，请参考：

{% page-ref page="./" %}

{% hint style="success" %}
你可以在[ Authing Pipeline 函数](../../extensibility/pipeline/)中添加用户 Metadata。
{% endhint %}

## 获取用户 Metadata <a id="add-metadata"></a>

Authing.metadata\(\_id\)

* 参数
  * \_id &lt;string&gt; 用户 ID，必填。
* 使用方法

```javascript
const metadata = await authing.metadata(_id)
```

* 返回数据
  * totalCount  Metadata 列表长度
  * list 

```javascript
{
  list: [
    {
      key: 'KEY',
      value: 'VALUE',
    },
  ],
  totalCount: 1,
}
```

## 添加 Metadata <a id="add-metadata"></a>

Authing.setMetadata\(input\)

* 参数
  * input &lt;object&gt;
    * \_id: &lt;string&gt; 用户 ID，必填。
    * key: &lt;string&gt;  必填。
    * value: &lt;string&gt; 必填。
* 使用方法

```javascript
const metadata = await authing.setMetadata({
    _id: user._id,
    key: "KEY",
    value: "VALUE"
})
```

* 返回数据

```javascript
{
  list: [
    {
      key: 'KEY',
      value: 'VALUE',
    },
  ],
  totalCount: 1,
}
```

## 删除 Metadata <a id="remove-metadata"></a>

Authing.removeMetadata\(input\)

* 参数
  * input
    * \_id: &lt;string&gt; 用户 ID，必填。
    * key: &lt;string&gt; 必填。
* 使用方法

```javascript
const metadata = await authing.removeMetadata({
    _id: user._id,
    key: "KEY"
})
```

* 返回数据

```javascript
{
  list: [
    {
      key: 'KEY',
      value: 'VALUE',
    },
  ],
  totalCount: 1,
}
```

