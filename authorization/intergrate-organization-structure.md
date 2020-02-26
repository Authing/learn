# 接入组织机构管理

借助 Authing，可以快速[实现基于角色的访问控制（RBAC）](https://mp.weixin.qq.com/s/zRiymhIjTqfUlymjkGfEsA)。简单来说，RBAC 指的是通过用户的角色（Role）赋予其相关权限，这实现了细粒度的访问控制，并提供了一个相比直接授予单个用户权限，更简单、可控的管理方式。

  
而在现实生活中，组、角色往往是分层嵌套的，呈树状结构，最常见的就是组织机构，如公司、学校等等。这篇文章，我们会设想一家互联网公司 —— 「非凡科技有限公司」，看看他们是如何使用 Authing 快速完成组织机构建模的。

## 非凡科技有限公司背景介绍

{% hint style="success" %}
我们假设有一家名为「非凡科技有限公司」的机构，约 300 名员工，他们想使用 Authing 对其组织机构进行建模、管理。
{% endhint %}

非凡科技有限公司的公司架构如下：

* 一级部门有产品部、研发部、运营部、综合管理部
* 一级部门下面又有二级部门，如产品部中包含产品经理和设计等。

![](../.gitbook/assets/image%20%28260%29.png)

这是一个典型的树状结构，其中有且仅有一个根节点，一般而言，根节点就是一家公司、一个组织。每个节点对应一个分层的部门。

这里有一点需要注意：在 Authing 中，这样的节点对应的是一个 Group，一个 Group 可以拥有多个角色（Role），一个角色包含一个或多个权限（Permission）；而一个 Group 由若干用户（User）组成，某个 Group 中的用户会继承该 Group 下的所有角色，从而具备相关权限。

{% hint style="info" %}
在 Authing 中，组织机构（Org）是一组树状的 Group 集合，而 Org 的节点（Node）本质上就是一个 Group。
{% endhint %}

了解如何管理  Group 中的 User、如何管理 Group 中的 Role、如何配置 Role 的 Permission，请见：

{% page-ref page="../sdk/sdk-for-node/rbac.md" %}

了解如何查询单个用户的 Group, Role, Permission 列表，请见：

{% page-ref page="../sdk/sdk-for-node/query-permissions.md" %}

## 使用 Authing 完成组织机构管理

{% hint style="info" %}
本文中用到的 createGroup，createRole 等为封装的方法，如下所示。

参见此测试用例：[https://github.com/authing/authing.js/blob/master/tests/org.js](https://github.com/authing/authing.js/blob/master/tests/org.js)
{% endhint %}

本文使用 Node.JS SDK 演示，具体接口请参考：

{% page-ref page="../sdk/sdk-for-node/org-structure.md" %}

非 JavaScript 开发者请使用 API 调试器：

{% embed url="https://authing.cn/graphiql/" %}

```javascript
import Authing from 'authing-js-sdk'

const authing = new Authing({
    userPoolId: 'your_user_pool_id',
    secret: 'your_user_pool_secret',
})

async function createGroup(name, description) {
  name = name || `分组${Math.random().toString(36).slice(2)}`
  description = description || ""
  return await authing.authz.createGroup({
    name,
    description
  })
}

async function createRole(name, description) {
  name = name || `角色${Math.random().toString(36).slice(2)}`
  description = description || ""
  return await authing.authz.createRole({
    name,
    description
  })
}

async function createPermission(name, description) {
  name = name || `权限${Math.random().toString(36).slice(2)}`;
  description = description || ""
  return await authing.authz.createPermission({
    name,
    description
  })
}

async function createPermissionBatch(permissions) {
  let list = []
  for (let name of permissions) {
    list.push(await createPermission(name))
  }
  return list
}

async function createUser() {
  return await authing.register({
    email: Math.random()
      .toString(36)
      .slice(2) + "@authing.cn",
    password: "123456a"
  });
}

```

### 梳理组织机构节点

首先，我们需要列举出该组织结构拥有的所有节点：

* 非凡科技有限公司
* 产品部
* 研发部
* 运营部
* 综合管理部
* 产品经理
* 设计
* 开发
* 测试
* 运维
* 用户运营
* 渠道运营
* HR
* 财务
* 行政

这些节点在 Authing 中都是一个 Group，所以需要先创建这些 Group：

```javascript
const 非凡科技有限公司 = await createGroup("非凡科技有限公司")

const 产品部 = await createGroup('产品部')
const 产品经理 = await createGroup('产品经理')
const 设计 = await createGroup('设计')

const 研发部 = await createGroup('研发部')
const 开发 = await createGroup('开发')
const 测试 = await createGroup('测试')
const 运维 = await createGroup('运维')

const 运营部 = await createGroup('运营部')
const 用户运营 = await createGroup('用户运营')
const 渠道运营 = await createGroup('渠道运营')

const 综合管理部 = await createGroup('综合管理部')
const HR = await createGroup('HR')
const 财务 = await createGroup('财务')
const 行政 = await createGroup('行政')
```

### 创建组织机构

接着创建组织机构（一组树状的 Group），这需要指定根节点对应的 Group:

```javascript
let org = await authing.org.createOrg({
  rootGroupId: 非凡科技有限公司._id
})
```

### 加入产品部及其子部门节点

接着插入产品部的各级节点：

这里需要指定以下参数：

* 组织机构 ID
* 该节点对应的 Group ID
* 该节点的父节点的 Group ID

如加入产品部节点时，groupId 为 Group&lt;产品部&gt; 的 ID，parentGroupId 为 Group&lt;非凡科技有限公司&gt; 的 ID。

```javascript
await authing.org.addNode({
  orgId: org._id,
  groupId: 产品部._id,
  parentGroupId: 非凡科技有限公司._id
})
```

```javascript
await authing.org.addNode({
  orgId: org._id,
  groupId: 产品经理._id,
  parentGroupId: 产品部._id
})
await authing.org.addNode({
  orgId: org._id,
  groupId: 设计._id,
  parentGroupId: 产品部._id
})
```

### 以此类推，加入所有节点

研发部：

```javascript
// 添加研发部
await authing.org.addNode({
  orgId: org._id,
  groupId: 研发部._id,
  parentGroupId: 非凡科技有限公司._id
})
await authing.org.addNode({
  orgId: org._id,
  groupId: 开发._id,
  parentGroupId: 研发部._id
})
await authing.org.addNode({
  orgId: org._id,
  groupId: 测试._id,
  parentGroupId: 研发部._id
})
await authing.org.addNode({
  orgId: org._id,
  groupId: 运维._id,
  parentGroupId: 研发部._id
})

```

运营部：

```javascript
await authing.org.addNode({
  orgId: org._id,
  groupId: 运营部._id,
  parentGroupId: 非凡科技有限公司._id
})
await authing.org.addNode({
  orgId: org._id,
  groupId: 用户运营._id,
  parentGroupId: 运营部._id
})
await authing.org.addNode({
  orgId: org._id,
  groupId: 渠道运营._id,
  parentGroupId: 运营部._id
})
```

综合管理部：

```javascript
// 添加综合管理部
await authing.org.addNode({
  orgId: org._id,
  groupId: 综合管理部._id,
  parentGroupId: 非凡科技有限公司._id
})
await authing.org.addNode({
  orgId: org._id,
  groupId: HR._id,
  parentGroupId: 综合管理部._id
})
await authing.org.addNode({
  orgId: org._id,
  groupId: 财务._id,
  parentGroupId: 综合管理部._id
})
await authing.org.addNode({
  orgId: org._id,
  groupId: 行政._id,
  parentGroupId: 综合管理部._id
})
```

### 查看最新组织机构结构

到现在，我们的非凡科技有限公司组织机构见建模完成了，是时候获取其最新的树状结构了：

```javascript
const { tree } = await authing.org.findById(org._id)
```

tree 数据如下所示：

![](../.gitbook/assets/image%20%2893%29.png)

## 如何向组织机构中添加用户和配置权限

前面说过，Authing 中一个组织结构节点对应一个 Group，对此我们提供了完整的 SDK。

### 为组织机构节点配置权限

相关 API 见：

{% page-ref page="../sdk/sdk-for-node/rbac.md" %}

非凡科技有限公司的所有员工，都具备开具发票、使用公司邮箱的权限。与此对应，在此我们创建两个角色：Invoice Submitter 和 Corp Email User。

其中 Invoice Submitter 具备以下权限：

* invoice:login
* invoice:create
* invoice:query
* invoice:list
* invoice:delete

Corp Email User 具备以下权限：

* corp-email:login
* corp-email:send
* corp-email:receive
* corp-email:list
* corp-email:detail

这可以通过以下代码完成：

```javascript
const InvoiceSubmitter = await createRole('Invoice Submitter')
let permissions = await createPermissionBatch(['invoice:login', 'invoice:create', 'invoice:query', 'invoice:list', 'invoice:delete'])
await authing.authz.addPermissionToRoleBatch({
  roleId: InvoiceSubmitter._id,
  permissionIdList: permissions.map(x => x._id)
})

const CorpEmailUser = await createRole('Corp Email Use')
permissions = await createPermissionBatch(['corp-email:login', 'corp-email:send', 'corp-email:receive', 'corp-email:list', 'corp-email:detail'])
await authing.authz.addPermissionToRoleBatch({
  roleId: CorpEmailUser._id,
  permissionIdList: permissions.map(x => x._id)
})

```

接着让 Group 非凡科技有限公司具备 Invoice Submitter 和 Corp Email User 两个角色：

```javascript
await authing.authz.addRoleToGroup({
  roleId: InvoiceSubmitter._id,
  groupId: 非凡科技有限公司._id
})
await authing.authz.addRoleToGroup({
  roleId: InvoiceSubmitter._id,
  groupId: 非凡科技有限公司._id
})
```

### 向组织机构节点添加用户

相关 API 见：

{% page-ref page="../sdk/sdk-for-node/rbac.md" %}

某个 Group 内的用户会继承该 Group 内所有角色的权限（如果有重叠，将会取并集）。

下面我们往用户池中注册新用户，然后将其加入非凡科技有限公司 Group 中：

```javascript
const user = await createUser()
await authing.authz.addUserToGroup({
  groupId: 非凡科技有限公司._id,
  userId: user._id
})
```

### 查询用户具备的权限

相关 API  见：

{% page-ref page="../sdk/sdk-for-node/query-permissions.md" %}

```javascript
const { rawList: permissionList } = await authing.userPermissionList(user._id)
```

permissionList 如下：可见，此用户已经继承了 Invoice Submitter 和 Corp Email User 两个角色的所有权限：

```javascript
[
   'invoice:login',
   'invoice:create',
   'invoice:query',
   'invoice:list',
   'invoice:delete',
   'corp-email:login',
   'corp-email:send',
   'corp-email:receive',
   'corp-email:list',
   'corp-email:detail',
 ]
```

开发者拿到用户权限列表之后，可以在业务代码层判断用户是否具备某一特定权限，如：

```javascript
if "corp-email:login" not in user.permissionList:
    return "Permission Denied"
```

## 总结

本文我们以「非凡科技有限公司」为例，介绍了如何将一棵组织机构树转换成一组嵌套、有层次的 Group。通过 Authing 提供的分组角色权限管理 API，可以为 Group 配置角色、指派成员，使得该 Group 中的用户继承所需的权限，从而完成组织机构建模与权限控制。

