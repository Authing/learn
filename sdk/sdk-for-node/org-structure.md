# 组织机构管理

## 如何接入

{% page-ref page="../../authorization/intergrate-organization-structure.md" %}

## 初始化

使用以下接口时，请先完成初始化工作，请参考：

{% page-ref page="./" %}

## 创建组织机构

Authing.org.createOrg\(input\)

* 参数：
  * input: &lt;Object&gt; 
    * rootGroupId：&lt;string&gt; 必填，根节点群组 ID
* 使用方法

```javascript
const org = await authing.org.createOrg({
    rootGroupId: "xxxxx"
})
```

* 返回数据
  * \_id: 组织结构 ID
  * nodes: 组织机构节点列表
    * \_id: Group ID
    * children: Array&lt;String&gt; 子节点 Group ID
    * root: &lt;boolean&gt; 是否为根节点
  * tree: 组织结构树状结构
    * \_id: Group ID

```javascript
 {
    _id: '5e355dd1af15ce8b5cc39386',
    nodes: [
      {
        _id: '5e355dd1af15ce8b5cc39385',
        children: [],
        createdAt: '2020-02-01T19:15:29+08:00',
        description: '描述信息',
        name: '分组58kb1ly6je3',
        root: true,
        updatedAt: '2020-02-01T19:15:29+08:00',
      },
    ],
    tree: {
      _id: '5e355dd1af15ce8b5cc39385',
      children: [],
      createdAt: '2020-02-01T19:15:29+08:00',
      description: '描述信息',
      name: '分组58kb1ly6je3',
      root: true,
      updatedAt: '2020-02-01T19:15:29+08:00',
    },
  }
```

## 添加节点

{% hint style="success" %}
一个组织机构是一棵树，其每个节点对应一个 Group。
{% endhint %}

Authing.org.addNode\(input\)

* 参数
  * input &lt;Object&gt;
    * orgId: 必填，组织机构 ID
    * groupId: 必填，该节点对应 Group ID
    * parentGroupId: 必填，父节点对应 Group ID
* 使用方法

```javascript
const org = await authing.org.addNode({
  orgId: "组织机构 ID",
  parentGroupId: "父节点对应 Group ID",
  groupId: "该节点对应 Group ID"
})
```

* 返回数据：同创建组织机构接口。

## 删除节点

Authing.org.removeNode\(input\)

* 参数
  * input &lt;Object&gt;
    * orgId: 必填，组织机构 ID
    * groupId: 必填，节点对应的 group ID
* 使用方法

```javascript
let org = await authing.org.removeNode({
    orgId: "xxx",
    groupId: "xxxx"
})
```

* 返回参数：同创建组织机构接口。

## 删除组织机构

Authing.org.dropById\(\_id\)

* 参数
  * \_id: 组织结构ID
* 使用方法

```javascript
const res = await authing.org.dropById(org._id)
```

* 返回数据

```javascript
{
    code: 200,
    message: "操作成功！"
}
```

## 查询组织机构 - 通过 ID

Authing.org.findById\(\_id\)

* 参数
  * \_id: 组织机构 ID
* 使用方法

```javascript
const org = await authing.org.findById(org._id)
```

* 返回参数：同创建组织机构接口。

## 查询用户池所有组织机构

Authing.org.all\(\)

* 参数：无
* 使用方法

```javascript
const { totalCount, list: orgs } = await authing.org.all()
```

* 返回数据
  * totalCount: 组织机构总数。
  * list: 组织机构列表，其中的元素为一个组织结构，具体见创建组织机构方法。

## 判断某节点是否为根节点

Authing.org.isRootNode\(input\)

* 参数
  * input: &lt;Object&gt;
    * orgId: 组织机构 ID
    * groupId: Group ID
* 使用方法

```javascript
 const isRoot = await authing.org.isRootNode({
  groupId: "xxx",
  orgId: "xxx"
 })
```

* 返回数据：布尔值。

## 查询某组织机构的根节点

Authing.org.rootNode\(\_id\)

* 参数
  * \_id: 组织机构 ID
* 使用方法

```javascript
const group = await authing.org.rootNode(org._id)
```

* 返回数据：根节点对应的 Group

```javascript
{
    _id: '5e356424004e528e9c834f8a',
    createdAt: '2020-02-01T19:42:28+08:00',
    description: '非凡技术有限公司',
    name: '非凡技术有限公司',
    updatedAt: '2020-02-01T19:42:28+08:00',
 }
```

## 查询某节点的子节点列表

Authing.org.childrenNodes\(input\)

* 参数
  * input: &lt;Object&gt;
    * orgId: 组织机构 ID
    * groupId: Group ID
* 使用方法

```javascript
const children = await authing.org.childrenNodes({
    orgId: "xxx",
    groupId: "xxx"
})
```

* 返回数据, 列表元素含义：
  * depth: 距该节点的深度，1 表示为直接子节点。
  * group: 子节点对应的 Group

```javascript
 [
    {
      depth: 1,
      group: {
        _id: '5e356242af15ce8b5cc393bd',
        createdAt: '2020-02-01T19:34:26+08:00',
        description: '描述信息',
        name: '分组sxvtd3unrxe',
        updatedAt: '2020-02-01T19:34:26+08:00',
      },
    },
    {
      depth: 1,
      group: {
        _id: '5e356243af15ce8b5cc393c0',
        createdAt: '2020-02-01T19:34:27+08:00',
        description: '描述信息',
        name: '分组04h9vdiee0vd',
        updatedAt: '2020-02-01T19:34:27+08:00',
      },
    },
  ]
```

