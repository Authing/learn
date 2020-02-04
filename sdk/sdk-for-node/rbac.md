# 权限控制

此 模块的 API 可以分为两类：

一类是权限管理、配置接口：

* 分组（Group）的增删改查接口
  * 支持批量、分页
* 角色（Role）的增删改查接口
  * 支持批量、分页
* 权限（Permission）的增删改查接口
  * 支持批量、分页
* 分组内用户管理
* 分组内角色管理
* 角色内用户管理
* 角色内权限管理

  
另一类是查询特定用户权限接口：

* 查询特定用户所有权限列表
* 查询特定用户所有角色列表
* 查询特定用户所有分组列表

除了主动查询接口外，我们还支持将权限列表写入用户的 Token，开发者可以在本地使用应用密钥解密得到权限列表。最终的权限列表示例如下：

```javascript
{    
    "permissionList": ["email:login", "email:read", "email:write", "email:send"]
}
```

其中 `​email:login​` 完全为用户自定义，**我们推荐使用** ​**object:action**​ **的格式进行命名**。

  
开发者拿到用户权限列表之后，可以在业务代码层判断用户是否具备某一特定权限，如：

```python
if not "email:login" in user.permissionList:    
    raise PermissionDeniedError
```

## 初始化

使用以下接口时，请先完成初始化工作，请参考：

{% page-ref page="./" %}

## 异常处理

所有失败的操作，都需要使用 try catch 捕获异常。操作失败有两种情况：

* 网络请求出错，常见情况包括：传递参数不合法，这会被 GraphQL 过滤层直接拦截；网络无法连接。
* 业务逻辑出错，常见情况包括：密码不正确，删除某资源传递的 id 不存在等。

为了方便开发者快速定位出错原因，我们提供一个 formatError 函数：

```javascript
function formatError(error) {
  if (typeof error === 'string') return error;
  if (error.request) {
    // 网络请求错误
    if(error.response)
      return JSON.stringify(error.response.data);
    else
      return JSON.stringify(error.request);
  } else if (typeof error.message === 'object') {
    // 业务逻辑错误
    if (error.message.message) {
      return JSON.stringify(error.message.message);
    } else {
      return JSON.stringify(error.message);
    }
  } else {
    return error;
  }
}
```

示例如下：

```javascript
  try {
    const res = await authing.authz.deleteGroup("不存在的 groupId")
    console.log(res)
  } catch (error) {
    console.log(formatError(error))
  }
```

## 分组增删改查

### 创建分组

Authing.authz.createGroup\(input\)

* 参数
  * input &lt;Object&gt;
    * name &lt;string&gt; 名称，必填。
    * description &lt;string&gt;  描述，选填。
*  使用方法

```javascript
const group = await authing.authz.createGroup({
  name: 'admin',
  description: "系统管理员"
})
```

* 返回数据

```javascript
{
  _id: '5e1edbd74b6419665991f51e',
  name: '管理员835bdf92gth',
  description: '描述信息',
  createdAt: '2020-01-16T20:47:04+08:00',
  updatedAt: '2020-01-16T20:47:04+08:00'
}
```

### 修改分组

Authing.authz.updateGroup\(input\)

* 参数
  * input &lt;Object&gt;
    * \_id &lt;string&gt; 分组 ID，必填。
    * name &lt;string&gt; 名称，选填。
    * description &lt;string&gt;  描述，选填。
* 使用方法

```javascript
group = await authing.authz.updateGroup({
  _id: "5e1edcb14b6419665991f595",
  description: '新的描述'
})
```

* 返回数据

```javascript
{
  _id: '5e1edcb14b6419665991f595',
  name: 'admin',
  description: '新的描述',
  createdAt: '2020-01-16T20:47:04+08:00',
  updatedAt: '2020-01-16T20:47:04+08:00'
}
```

{% hint style="info" %}
本页中的所有修改接口均遵循以下逻辑：

* \_id 参数必填
* 出现在 input 中的字段值将替换原有值
* 没有出现在 input 中的字段值保持不变
* 传入 input 的多余字段将会被舍弃
{% endhint %}

### 查询分组

Authing.authz.group\(\_id\)

* 参数
  * \_id &lt;string&gt;  分组 ID，必填。
* 使用方法

```javascript
const group = await authing.authz.group("5e1edcb14b6419665991f595")
```

* 返回数据

```javascript
{
  _id: '5e1edcb14b6419665991f595',
  name: 'admin',
  description: '新的描述',
  createdAt: '2020-01-16T20:47:04+08:00',
  updatedAt: '2020-01-16T20:47:04+08:00'
}
```

### 查询用户池分组列表

> 此方法支持分页、排序。

Authing.authz.groupList\(options\)

* 参数
  * options &lt;Object&gt; 可选
    * sortBy &lt;string&gt; 排序方式，可选。默认为 CREATEDAT\_DESC ，表示按照创建时间降序，也就是最新创建的排在最前面。全部的可选值为：
      * CREATEDAT\_DESC：按照创建时间降序。
      * CREATEDAT\_ASC ：按照创建时间升序。
      * UPDATEDAT\_DESC：按照修改时间降序。
      * UPDATEDAT\_ASC：按照修改时间排序。
    * page &lt;int&gt;  页码，可选。默认为 0，zero-based。
    * count &lt;int&gt;  没页数目，可选。默认为 10。
* 使用方法

```javascript
const groupList = await authing.authz.groupList()

// 自定义排序方式 / 分页
const {totalCount, list} = await authing.authz.groupList({
    sortBy: "CREATEDAT_DESC",
    page: 1,
    count: 10,
})
```

* 返回数据
  * totalCount  总数
  * list 当前页列表

```javascript
{
  totalCount: 2,
  list: [
    {
      _id: '5e1fff324b6419665991f6b4',
      name: 'employee',
      description: '正式员工。',
      createdAt: '2020-01-16T20:47:04+08:00',
      updatedAt: '2020-01-16T20:47:04+08:00'
    },
    {
      _id: '5e1edcb34b6419665991f5d9',
      name: 'intern',
      description: '实习生。',
      createdAt: '2020-01-16T20:47:04+08:00',
      updatedAt: '2020-01-16T20:47:04+08:00'
    },
  ]
}
```

### 删除分组

Authing.authz.deleteGroup\(\_id\)

* 参数
  * \_id 分组 ID，必填。
* 使用方法

```javascript
const res = await authing.authz.deleteGroup("5e20082f4b6419665991fa57")
```

* 返回数据

操作成功示例

```javascript
{ 
    message: '操作成功',
    code: 200
}
```

如果操作失败，需要使用 try catch 捕捉异常，详情见 **异常处理** 部分。

{% hint style="danger" %}
删除分组会将其与角色、用户之间的关系连带一起删除（角色、用户本身不会删除），不可撤回，请谨慎操作。

且此操作为原子化操作，要么成功，要么全部 rollback。
{% endhint %}

### 批量删除分组

Authing.authz.deleteGroupBatch\(idList\)

* 参数
  * idList: &lt;Array&gt; 分组 ID 列表。
* 使用方法

```javascript
const res = await authing.authz.deleteGroupBatch([
    "5e1fff324b6419665991f6b4",
    "5e1edcb34b6419665991f5d9"
])
```

* 返回参数

操作成功示例：

```javascript
{ 
    message: '操作成功',
    code: 200
}
```

{% hint style="info" %}
批量操作，当且仅当所有操作成功时才会返回成功，如果其中任意一个步骤失败，其他所有操作将会 rollback，回退到之前的状态。操作失败需要用 try/catch 捕获异常，详情见**异常处理**部分。
{% endhint %}

{% hint style="danger" %}
此操作不可撤回，请谨慎操作。
{% endhint %}

## 角色增删改查

### 创建角色

Authing.authz.createRole\(input\)

* 参数
  * input &lt;Object&gt;
    * name: 名称，必填。
    * description: 描述，必填。
* 使用方法

```javascript
const role = await authing.authz.createRole({
  name: 'Invoice Submitter',
  description: '能使用发票工具创建并读取发票工具记录'
})
```

* 返回数据

```javascript
{
  _id: '5e20082f4b6419665991fa57',
  name: 'Invoice Submitter',
  description: '能使用发票工具创建并读取发票工具记录',
  createdAt: '2020-01-16T20:47:04+08:00',
  updatedAt: '2020-01-16T20:47:04+08:00'
}
```

{% hint style="info" %}
分组\(Group\)和角色\(Role\)有什么区别？

* 角色是一组权限的集合。
* 分组侧重于管理用户，一个分组通常拥有多个角色，分组内的用户会继承分组内所有角色的所有权限。
{% endhint %}

{% hint style="info" %}
常见的 Group 和 Role 示例：

* Group
  * admin: 系统管理员，通常包含系统维护者。
  * employee: 正式雇员。
  * employer: 面试官。
  * hr
  * intern: 实习生
  * ops\_engineer: 运维工程师
* Role
  * Invoice Submitter: 具备发票报销的相关权限。
  * Vacation Requester: 具备申请假期的相关权限。
  * Corporation Email User: 具备使用公司邮箱的的相关权限。
  * Production Server Operator: 具备线上服务器的操作权限。
  * HR App User: 具备使用 HR 系统的相关权限。

举例来说：可以这样建立 Role 和 Group 之间的关系：

* intern  具备 Corporation Email User 这个角色，但是不具备 Vacation Requester 和 Invoice Submitter 这两个角色。
* employee  拥有发票报销和申请假期角色，但是不具备线上服务器的操作权限。
* ops\_engineer 拥有发票报销、申请假期、线上服务器的角色。
{% endhint %}

{% hint style="success" %}
推荐使用 Group organize 用户，用 Role organize 权限，不要直接赋予单个用户某个权限。如果是某个用户临时需要具备某个角色，可以临时授予，结束之后再收回。
{% endhint %}

### 修改角色

Authing.authz.updateRole\(input\)

* 参数
  * input &lt;Object&gt;
    * \_id: 角色 ID，必填。
    * name: 角色名称，选填。
    * description：角色描述，选填。
* 使用方法

```javascript
const role = await authing.authz.updateRole({
  _id: "5e20082f4b6419665991fa57",
  name: "Invoice Submitter",
  description: '能使用发票工具创建并读取发票工具记录。'
})
```

* 返回数据

```javascript
{
  _id: '5e20082f4b6419665991fa57',
  name: 'Invoice Submitter',
  description: '能使用发票工具创建并读取发票工具记录。',
  createdAt: '2020-01-16T20:47:04+08:00',
  updatedAt: '2020-01-16T20:47:04+08:00'
}
```

### 查询角色

Authing.authz.role\(\_id\)

* 参数
  * \_id &lt;string&gt;  角色 ID，必填。
* 使用方法

```javascript
const role = await authing.authz.role("5e20082f4b6419665991fa57")
```

* 返回数据

```javascript
{
  _id: '5e20082f4b6419665991fa57',
  name: 'Invoice Submitter',
  description: '能使用发票工具创建并读取发票工具记录。'
  createdAt: '2020-01-16T20:47:04+08:00',
  updatedAt: '2020-01-16T20:47:04+08:00'
}
```

### 查询用户池角色列表

> 此方法支持分页、排序。

Authing.authz.roleList\(options\)

* 参数
  * options &lt;Object&gt; 可选
    * sortBy &lt;string&gt; 排序方式，可选。默认为 CREATEDAT\_DESC ，表示按照创建时间降序，也就是最新创建的排在最前面。全部的可选值为：
      * CREATEDAT\_DESC：按照创建时间降序。
      * CREATEDAT\_ASC ：按照创建时间升序。
      * UPDATEDAT\_DESC：按照修改时间降序。
      * UPDATEDAT\_ASC：按照修改时间排序。
    * page &lt;int&gt;  页码，可选。默认为 0，zero-based。
    * count &lt;int&gt;  没页数目，可选。默认为 10。
* 使用方法

```javascript
const roleList = await authing.authz.roleList()

// 自定义排序方式 / 分页
const {totalCount, list} = await authing.authz.roleList({
      sortBy: 'CREATEDAT_DESC',
      page: 2,
      count: 10
})
```

* 返回数据
  * totalCount  总数
  * list 当前页列表

```javascript
{
  totalCount: 2,
  list: [
    {
      _id: '5e20082f4b6419665991fa57',
      name: 'Invoice Submitter',
      description: '能使用发票工具创建并读取发票工具记录。'
    },
    {
      _id: '5e20082f4b6419665991fa58',
      name: 'Vacation Requester',
      description: '能使用假期申请工具申请假期。'
    },
  ]
}
```

### 删除角色

Authing.authz.deleteRole\(\_id\)

* 参数
  * \_id 角色 ID，必填。
* 使用方法

```javascript
const res = await authing.authz.deleteRole("Role ID")
```

* 返回数据

操作成功示例

```javascript
{ 
    message: '操作成功',
    code: 200, 
}
```

如果操作失败，需要使用 try catch 捕捉异常，详情见 **异常处理** 部分。

{% hint style="danger" %}
删除角色会将其与权限、用户之间的关系连带一起删除（权限、用户本身不会删除），不可撤回，请谨慎操作。

且此操作为原子化操作，要么成功，要么全部 rollback。
{% endhint %}

### 批量删除角色

Authing.authz.deleteRoleBatch\(idList\)

* 参数
  * idList: &lt;Array&gt; 分组 ID 列表。
* 使用方法

```javascript
const res = await authing.authz.deleteRoleBatch([
    "5e1fff324b6419665991f6b4",
    "5e1edcb34b6419665991f5d9"
])
```

* 返回参数

操作成功示例：

```javascript
{ 
    message: '操作成功',
    code: 200
}
```

{% hint style="info" %}
批量操作，当且仅当所有操作成功时才会返回成功，如果其中任意一个步骤失败，其他所有操作将会 rollback，回退到之前的状态。操作失败需要用 try/catch 捕获异常，详情见**异常处理**部分。
{% endhint %}

{% hint style="danger" %}
此操作不可撤回，请谨慎操作。
{% endhint %}

## 权限增删改查

### 创建权限

Authing.authz.createPermission\(input\)

* 参数
  * input &lt;Object&gt;
    * name &lt;string&gt; 名称，必填。
    * description &lt;string&gt;  描述，选填。
*  使用方法

```javascript
const group = await authing.authz.createPermission({
  name: 'invoice:submit',
  description: "提交发票报销申请"
})
```

* 返回数据

```javascript
{
  _id: '5e1edbd74b6419665991f51e',
  name: 'invoice:submit',
  description: '提交发票报销申请'
}
```

{% hint style="success" %}
推荐使用 **subject:action** 这类形式命名权限。如：

* invoice:submit 提交发票报销申请
* invoice:revoke 撤回发票报销申请
* invoice:update 修改发起申请详情
* invoice:list 查看发起申请历史记录
* invoice:read 查看发票申请详情
{% endhint %}

### 修改权限

Authing.authz.updatePermission\(input\)

* 参数
  * input &lt;Object&gt;
    * \_id: 角色 ID，必填。
    * name: 角色名称，选填。
    * description：角色描述，选填。
* 使用方法

```javascript
const role = await authing.authz.updatePermission({
  _id: "5e20082f4b6419665991fa57",
  name: "invoice:submit",
  description: '提交发票报销申请。'
})
```

* 返回数据

```javascript
{
  _id: '5e20082f4b6419665991fa57',
  name: "invoice:submit",
  description: '提交发票报销申请。'
  createdAt: '2020-01-16T20:47:04+08:00',
  updatedAt: '2020-01-16T20:47:04+08:00'
}
```

### 查询权限

Authing.authz.permission\(\_id\)

* 参数
  * \_id &lt;string&gt;  权限 ID，必填。
* 使用方法

```javascript
const group = await authing.authz.permission("5e20082f4b6419665991fa57")
```

* 返回数据

```javascript
{
  _id: '5e20082f4b6419665991fa57',
  name: "invoice:submit",
  description: '提交发票报销申请。',
  createdAt: '2020-01-16T20:47:04+08:00',
  updatedAt: '2020-01-16T20:47:04+08:00'
}
```

### 查询用户池权限列表

> 此方法支持分页、排序。

Authing.authz.permissionList\(options\)

* 参数
  * options &lt;Object&gt; 可选
    * sortBy &lt;string&gt; 排序方式，可选。默认为 CREATEDAT\_DESC ，表示按照创建时间降序，也就是最新创建的排在最前面。全部的可选值为：
      * CREATEDAT\_DESC：按照创建时间降序。
      * CREATEDAT\_ASC ：按照创建时间升序。
      * UPDATEDAT\_DESC：按照修改时间降序。
      * UPDATEDAT\_ASC：按照修改时间排序。
    * page &lt;int&gt;  页码，可选。默认为 0，zero-based。
    * count &lt;int&gt;  没页数目，可选。默认为 10。
* 使用方法

```javascript
const permissionList = await authing.authz.permissionList()

const permissionList = await authing.authz.permissionList({
  sortBy: 'CREATEDAT_DESC',
  page: 2,
  count: 10
})
```

* 返回数据
  * totalCount  总数
  * list 当前页列表

```javascript
{
  totalCount: 1,
  list: [
    {
      _id: '5e20082f4b6419665991fa57',
      name: "invoice:submit",
      description: '提交发票报销申请。',
      createdAt: '2020-01-16T20:47:04+08:00',
      updatedAt: '2020-01-16T20:47:04+08:00'
    }
  ]
}
```

### 删除权限

Authing.authz.deletePermission\(\_id\)

* 参数
  * \_id 权限 ID，必填。
* 使用方法

```javascript
const res = await authing.authz.deletePermission("Permission ID")
```

* 返回数据

操作成功示例

```javascript
{ 
    message: '操作成功',
    code: 200
}
```

如果操作失败，需要使用 try catch 捕捉异常，详情见 **异常处理** 部分。

{% hint style="danger" %}
删除角色会将其与权限、用户之间的关系连带一起删除（权限、用户本身不会删除），不可撤回，请谨慎操作。

且此操作为原子化操作，要么成功，要么全部 rollback。
{% endhint %}

### 批量删除权限

Authing.authz.deletePermissionBatch\(idList\)

* 参数
  * idList: &lt;Array&gt; 分组 ID 列表。
* 使用方法

```javascript
const res = await authing.authz.deletePermissionBatch([
    "5e1fff324b6419665991f6b4",
    "5e1edcb34b6419665991f5d9"
])
```

* 返回参数

操作成功示例：

```javascript
{ 
    message: '操作成功',
    code: 200
}
```

{% hint style="info" %}
批量操作，当且仅当所有操作成功时才会返回成功，如果其中任意一个步骤失败，其他所有操作将会 rollback，回退到之前的状态。操作失败需要用 try/catch 捕获异常，详情见**异常处理**部分。
{% endhint %}

{% hint style="danger" %}
此操作不可撤回，请谨慎操作。
{% endhint %}

## 分组内角色管理

{% hint style="success" %}
推荐使用 Group 管理用户，用 Role 管理权限，不要直接赋予单个用户某个权限。如果是某个用户临时需要具备某个角色，可以临时授予，结束之后再收回。
{% endhint %}

### 查询分组角色列表

Authing.authz.groupRoleList\(\_id\)

* 参数
  * \_id &lt;string&gt; 分组 ID 
* 使用方法

```javascript
const {totalCount, list} = await authing.authz.groupRoleList()
```

* 返回数据

```javascript
{
    list: [
      {
        _id: '5e351f8815e7ca5a3577b6f7',
        createdAt: '2020-02-01T14:49:44+08:00',
        description: '描述',
        name: '角色itbv3w6jrgk',
        updatedAt: '2020-02-01T14:49:44+08:00',
      },
    ],
    totalCount: 1,
  }
```

### 分组添加角色

Authing.authz.addRoleToGroup\(input, options\)

* 参数：
  * input &lt;Object&gt; 必填
    * groupId:  &lt;string&gt; 分组 ID，必填。
    * roleId: &lt;string&gt; 角色 ID，必填。
  * options &lt;Object&gt; 选填
    * fetchRoles &lt;boolean&gt; 是否在结果中返回最新的角色列表。选填，默认为 false。
* 使用方法

```javascript
// 默认，不返回最新的角色列表
const res = await authing.authz.addRoleToGroup({
  roleId: "xxx",
  groupId: "xxx"
})

const res = await authing.authz.addRoleToGroup({
  roleId: "xxx",
  groupId: "xxx"
}, {
  fetchRoles: true
})
```

* 返回数据

操作成功示例：

```javascript
{ 
    message: '操作成功',
    code: 200
}

// 返回最新角色列表
{
  code: 200,
  message: '操作成功',
  data: { 
    totalCount: 1, 
    list: [
      {
        createdAt: '2020-01-16T20:47:04+08:00',
        updatedAt: '2020-01-16T20:47:04+08:00'
      }
    ] 
  }
}
```

如果操作失败，需要使用 try catch 捕捉异常，详情见 **异常处理** 部分。

### 分组删除角色

authing.authz.removeRoleFromGroup\(input, options\)

* 参数
  * input &lt;Object&gt;
    * groupId 分组 ID,必填。
    * roleId 角色 ID, 必填。
  * options &lt;Object&gt; 选填
    * fetchRoles &lt;boolean&gt; 是否在结果中返回最新的角色列表。选填，默认为 false。
* 使用方法

```javascript
await authing.authz.removeRoleFromGroup({
  roleId: "xxx",
  groupId: "xxx"
})

const res = await authing.authz.removeRoleFromGroup({
  roleId: "xxx",
  groupId: "xxx"
}, {
  fetchRoles: true
})
```

* 返回数据

操作成功示例：

```javascript
{ 
    message: '操作成功',
    code: 200
}

// 返回最新角色列表
{
  code: 200,
  message: '操作成功',
  data: { 
    totalCount: 1, 
    list: [
      {
        _id: '5e2148ddce7f3e37be7c86d4',
        createdAt: '2020-01-17T13:40:45+08:00',
        description: '描述',
        name: '角色km4ia4a7a4o',
        updatedAt: '2020-01-17T13:40:45+08:00',
      }
    ] 
  }
}
```

如果操作失败，需要使用 try catch 捕捉异常，详情见 **异常处理** 部分。

### 分组批量添加角色

Authing.authz.addRoleToGroupBatch\(input, options\)

* 参数：
  * input &lt;Object&gt; 必填
    * groupId: 分组 ID，必填。
    * roleIdList: 角色 ID 列表，必填。
  * options &lt;Object&gt; 选填
    * fetchRoles &lt;boolean&gt; 是否在结果中返回最新的角色列表。选填，默认为 false。
* 使用方法

```javascript
const res = await authing.authz.addRoleToGroupBatch({
  roleIdList: [
    "id1",
    "id2"
  ],
  groupId: "id"
})

const res = await authing.authz.addRoleToGroupBatch({
  roleIdList: [
    "id1",
    "id2"
  ],
  groupId: "id"
}， {
  fetchRoles: true
})

```

* 返回数据

操作成功示例：

```javascript
{ 
    message: '操作成功',
    code: 200
}

// 返回最新的角色列表
{ 
    message: '操作成功',
    code: 200,
    data: {
      list: [
        {
          _id: '5e2148ddce7f3e37be7c86d4',
          createdAt: '2020-01-17T13:40:45+08:00',
          description: '描述',
          name: '角色km4ia4a7a4o',
          updatedAt: '2020-01-17T13:40:45+08:00',
        },
        {
          _id: '5e2148ddce7f3e37be7c86d2',
          createdAt: '2020-01-17T13:40:45+08:00',
          description: '描述',
          name: '角色fwhlsn81uku',
          updatedAt: '2020-01-17T13:40:45+08:00',
        },
        {
          _id: '5e2148ddce7f3e37be7c86ca',
          createdAt: '2020-01-17T13:40:45+08:00',
          description: '描述',
          name: '角色7eoxpb0km7y',
          updatedAt: '2020-01-17T13:40:45+08:00',
        },
        {
          _id: '5e2148ddce7f3e37be7c86c9',
          createdAt: '2020-01-17T13:40:45+08:00',
          description: '描述',
          name: '角色gqgp9vzfey',
          updatedAt: '2020-01-17T13:40:45+08:00',
        },
      ],
      totalCount: 4,
  }
}
```

{% hint style="warning" %}
批量操作，当且仅当所有操作成功时才会返回成功，如果其中任意一个步骤失败，其他所有操作将会 rollback，回退到之前的状态。操作失败需要用 try/catch 捕获异常，详情见**异常处理**部分。
{% endhint %}

### 分组批量删除角色

Authing.authz.removeRoleFromGroupBatch\(input, options\)

* 参数
  * input  &lt;Object&gt; 必填
    * groupId: 分组 ID，必填。

      roleIdList: 角色 ID 列表，必填。
  * options  &lt;object&gt; 选填 
    * fetchRoles &lt;boolean&gt;  是否在结果中返回最新的角色列表。选填，默认为 false。
* 使用方法

```javascript
const res = await authing.authz.removeRoleFromGroupBatch({
  roleIdList: [
    "id1",
    "id2"
  ],
  groupId: "id"
})

const res = await authing.authz.removeRoleFromGroupBatch({
  roleIdList: [
    "id1",
    "id2"
  ],
  groupId: "id"
}, {
  fetchRoles: true
})
```

* 返回参数

操作成功示例：

```javascript
{ 
    message: '操作成功',
    code: 200
}

// 返回最新角色列表
{
  code: 200,
  message: '操作成功',
  data: { 
    totalCount: 1, 
    list: [
      {
        _id: '5e2148ddce7f3e37be7c86d4',
        createdAt: '2020-01-17T13:40:45+08:00',
        description: '描述',
        name: '角色km4ia4a7a4o',
        updatedAt: '2020-01-17T13:40:45+08:00',
      }
    ] 
  }
}
```

{% hint style="danger" %}
批量操作，当且仅当所有操作成功时才会返回成功，如果其中任意一个步骤失败，其他所有操作将会 rollback，回退到之前的状态。操作失败需要用 try/catch 捕获异常，详情见**异常处理**部分。
{% endhint %}

{% hint style="danger" %}
此操作不可撤回，请谨慎操作。
{% endhint %}

## 分组内用户管理

### 查询分组内用户列表

> 此方法支持分页、排序。

Authing.authz.groupUserList\(\_id, options\)

* 参数
  * \_id: 分组ID
  * options &lt;Object&gt; 可选
    * sortBy &lt;string&gt; 排序方式，可选。默认为 CREATEDAT\_DESC ，表示按照创建时间降序，也就是最新创建的排在最前面。全部的可选值为：
      * CREATEDAT\_DESC：按照创建时间降序。
      * CREATEDAT\_ASC ：按照创建时间升序。
      * UPDATEDAT\_DESC：按照修改时间降序。
      * UPDATEDAT\_ASC：按照修改时间排序。
    * page &lt;int&gt;  页码，可选。默认为 0，zero-based。
    * count &lt;int&gt;  没页数目，可选。默认为 10。
* 使用方法

```javascript
const {totalCount, list} = await authing.authz.groupUserList("xxxx")

// 或者
const {totalCount, list}  = await authing.authz.groupUserList("xxx", {
  sortBy: 'CREATEDAT_DESC',
  page: 2,
  count: 10
})
```

* 返回数据
  * totalCount  总数
  * list 当前页列表

```javascript
{
  list: [
    {
      _id: '5e35239215e7ca5a3577b8af',
      blocked: false,
      browser: '',
      company: '',
      email: 'o2sozsya3mm@authing.cn',
      emailVerified: false,
      isDeleted: false,
      lastIP: null,
      lastLogin: '2020-02-01T15:06:58+08:00',
      loginsCount: 0,
      nickname: '',
      oauth: '',
      phone: '',
      photo: 'https://usercontents.authing.cn/authing-avatar.png',
      registerInClient: '5e1be38ab1599657b6477022',
      registerMethod: 'default:username-password',
      signedUp: '2020-02-01T15:06:58+08:00',
      token: null,
      tokenExpiredAt: 'Invalid date',
      unionid: null,
      userLocation: null,
      userLoginHistory: null,
      username: 'o2sozsya3mm@authing.cn',
    },
  ],
  totalCount: 15,
}
```

### 分组添加用户

Authing.authz.addUserToGroup\(input, options\)

* 参数：
  * input &lt;Object&gt;
    * groupId: 分组 ID，必填。
    * userId: 用户 ID，必填。
  * options  &lt;object&gt; 选填 
    * fetchUsers &lt;boolean&gt;  是否在结果中返回最新的用户列表。选填，默认为 false。
* 使用方法

```javascript
const res = await authing.authz.addUserToGroup({
  userId: "",
  groupId: ""
})
```

* 返回数据

操作成功示例：

```javascript
{ 
    message: '操作成功',
    code: 200
}
```

如果操作失败，需要使用 try catch 捕捉异常，详情见 **异常处理** 部分。

### 分组删除用户

Authing.authz.removeUserFromGroup\(input, options\)

* 参数：
  * input: &lt;Object&gt;
    * groupId: &lt;string&gt; 分组 ID，必填。
    * userId: &lt;string&gt;  用户 ID，必填。
  * options  &lt;object&gt; 选填 
    * fetchUsers &lt;boolean&gt;  是否在结果中返回最新的用户列表。选填，默认为 false。
* 使用方法

```javascript
const res = await authing.authz.removeUserFromGroup({
  userId: "",
  groupId: ""
})
```

* 返回数据

操作成功示例：

```javascript
{ 
    message: '操作成功',
    code: 200
}
```

如果操作失败，需要使用 try catch 捕捉异常，详情见 **异常处理** 部分。

### 分组批量添加用户

Authing.authz.addUserToGroupBatch\(input, options\)

* 参数：
  * input: &lt;Object&gt;
    * groupId: 分组 ID，必填。
    * userIdList: 用户 ID，必填。
  * options  &lt;object&gt; 选填 
    * fetchUsers &lt;boolean&gt;  是否在结果中返回最新的用户列表。选填，默认为 false。
* 使用方法

```javascript
const res = await authing.authz.addUserToGroup({
  userIdList: ["", ""],
  groupId: ""
})
```

* 返回数据

操作成功示例：

```javascript
{ 
    message: '操作成功',
    code: 200
}
```

如果操作失败，需要使用 try catch 捕捉异常，详情见 **异常处理** 部分。

{% hint style="danger" %}
批量操作，当且仅当所有操作成功时才会返回成功，如果其中任意一个步骤失败，其他所有操作将会 rollback，回退到之前的状态。操作失败需要用 try/catch 捕获异常，详情见**异常处理**部分。
{% endhint %}

### 分组批量删除用户

Authing.authz.removeUserFromGroupBatch\(input, options\)

* 参数：
  * input: &lt;Object&gt;
    * groupId: 分组 ID，必填。
    * userIdList: 用户 ID 列表，必填。
  * options  &lt;object&gt; 选填 
    * fetchUsers &lt;boolean&gt;  是否在结果中返回最新的用户列表。选填，默认为 false。
* 使用方法

```javascript
const res = await authing.authz.removeUserFromGroupBatch({
  userIdList: ["", ""],
  groupId: ""
})
```

* 返回数据

操作成功示例：

```javascript
{ 
    message: '操作成功',
    code: 200
}
```

如果操作失败，需要使用 try catch 捕捉异常，详情见 **异常处理** 部分。

{% hint style="info" %}
批量操作，当且仅当所有操作成功时才会返回成功，如果其中任意一个步骤失败，其他所有操作将会 rollback，回退到之前的状态。操作失败需要用 try/catch 捕获异常，详情见**异常处理**部分。
{% endhint %}

{% hint style="danger" %}
此操作不可撤回，请谨慎操作。
{% endhint %}

## 角色内用户管理

### 查询具备某角色的用户列表

> 此方法支持分页、排序。

Authing.authz.roleUserList\(\_id, options\)

* 参数
  * \_id: 角色ID
  * options &lt;Object&gt; 可选
    * sortBy &lt;string&gt; 排序方式，可选。默认为 CREATEDAT\_DESC ，表示按照创建时间降序，也就是最新创建的排在最前面。全部的可选值为：
      * CREATEDAT\_DESC：按照创建时间降序。
      * CREATEDAT\_ASC ：按照创建时间升序。
      * UPDATEDAT\_DESC：按照修改时间降序。
      * UPDATEDAT\_ASC：按照修改时间排序。
    * page &lt;int&gt;  页码，可选。默认为 0，zero-based。
    * count &lt;int&gt;  没页数目，可选。默认为 10。
* 使用方法

```javascript
const {totalCount, list} = await authing.authz.roleUserList("xxxx")

// 或者
const {totalCount, list}  = await authing.authz.roleUserList("xxx", {
  sortBy: 'CREATEDAT_DESC',
  page: 2,
  count: 10
})
```

* 返回数据
  * totalCount  总数
  * list 当前页列表

```javascript
{
  list: [
    {
      _id: '5e35239215e7ca5a3577b8af',
      blocked: false,
      browser: '',
      company: '',
      email: 'o2sozsya3mm@authing.cn',
      emailVerified: false,
      isDeleted: false,
      lastIP: null,
      lastLogin: '2020-02-01T15:06:58+08:00',
      loginsCount: 0,
      nickname: '',
      oauth: '',
      phone: '',
      photo: 'https://usercontents.authing.cn/authing-avatar.png',
      registerInClient: '5e1be38ab1599657b6477022',
      registerMethod: 'default:username-password',
      signedUp: '2020-02-01T15:06:58+08:00',
      token: null,
      tokenExpiredAt: 'Invalid date',
      unionid: null,
      userLocation: null,
      userLoginHistory: null,
      username: 'o2sozsya3mm@authing.cn',
    },
  ],
  totalCount: 15,
}
```

### 授权用户某角色

Authing.authz.assignRoleToUser\(input, options\)

* 参数：
  * options &lt;Object&gt; 必填
    * userId:  &lt;string&gt; 用户 ID，必填。
    * roleId: &lt;string&gt; 角色 ID，必填。
  * input &lt;Object&gt; 选填
    * fetchUsers &lt;boolean&gt;  是否在结果中返回角色中包含的最新用户列表。选填，默认为 false。
* 使用方法

```javascript
const res = await authing.authz.assignRoleToUser({
  roleId: "",
  userId: ""
})
```

* 返回数据

操作成功示例：

```javascript
{ 
    message: '操作成功',
    code: 200
}
```

如果操作失败，需要使用 try catch 捕捉异常，详情见 **异常处理** 部分。

{% hint style="warning" %}
一个用户最多同时具有 50 个角色。
{% endhint %}

### 批量授权用户某角色

Authing.authz.assignRoleToUserBatch\(input, options\)

* 参数：
  * input &lt;Object&gt; 必填
    * userId:  &lt;string&gt; 用户 ID，必填。
    * roleId: &lt;string&gt; 角色 ID，必填。
  * options &lt;Object&gt; 选填
    * fetchUsers &lt;boolean&gt;  是否在结果中返回角色中包含的最新用户列表。选填，默认为 false。
* 使用方法

```javascript
const res = await authing.authz.assignRoleToUserBatch({
  roleId: "",
  userIdList: []
})

// 返回最新列表
const res = await authing.authz.assignRoleToUserBatch({
  roleId: "",
  userIdList: []
}, {
  fecthUsers: true
)
```

* 返回数据

操作成功示例：

```javascript
{ 
    message: '操作成功',
    code: 200
}
```

如果操作失败，需要使用 try catch 捕捉异常，详情见 **异常处理** 部分。

{% hint style="warning" %}
批量操作，当且仅当所有操作成功时才会返回成功，如果其中任意一个步骤失败，其他所有操作将会 rollback，回退到之前的状态。操作失败需要用 try/catch 捕获异常，详情见**异常处理**部分。
{% endhint %}

### 撤权用户某角色

Authing.authz.revokeRoleFromUser\(input, options\)

* 参数：
  * input &lt;Object&gt; 必填
    * userId:  &lt;string&gt; 用户 ID，必填。
    * roleId: &lt;string&gt; 角色 ID，必填。
  * options &lt;Object&gt; 选填
    * fetchUsers &lt;boolean&gt;  是否在结果中返回角色中包含的最新用户列表。选填，默认为 false。
* 使用方法

```javascript
const res = await authing.authz.revokeRoleFromUser({
  roleId: "",
  userId: ""
})
```

* 返回数据

操作成功示例：

```javascript
{ 
    message: '操作成功',
    code: 200
}
```

如果操作失败，需要使用 try catch 捕捉异常，详情见 **异常处理** 部分。

### 批量撤权用户某角色

Authing.authz.revokeRoleFromUser\(input, options\)

* 参数：
  * input &lt;Object&gt; 必填
    * userId:  &lt;string&gt; 用户 ID，必填。
    * roleId: &lt;string&gt; 角色 ID，必填。
  * options &lt;Object&gt; 选填
    * fetchUsers &lt;boolean&gt;  是否在结果中返回角色中包含的最新用户列表。选填，默认为 false。
* 使用方法

```javascript
const res = await authing.authz.revokeRoleFromUser({
  roleId: "",
  userIdList: []
})
```

* 返回数据

操作成功示例：

```javascript
{ 
    message: '操作成功',
    code: 200
}
```

如果操作失败，需要使用 try catch 捕捉异常，详情见 **异常处理** 部分。

{% hint style="warning" %}
批量操作，当且仅当所有操作成功时才会返回成功，如果其中任意一个步骤失败，其他所有操作将会 rollback，回退到之前的状态。操作失败需要用 try/catch 捕获异常，详情见**异常处理**部分。
{% endhint %}

## 角色内权限管理

### 查询某角色具备的所有权限

> 此方法支持分页、排序。

Authing.authz.rolePermissionList\(\_id\)

* 参数
  * \_id: &lt;string&gt; 角色ID
* 使用方法

```javascript
const {totalCount, list} = await authing.authz.rolePermissionList("xxxx")

// 或者
const {totalCount, list}  = await authing.authz.rolePermissionList("xxx"})
```

* 返回数据
  * totalCount  总数
  * list 当前页列表

```javascript
 {
    list: [
      {
        _id: '5e35259315e7ca5a3577b9d9',
        createdAt: '2020-02-01T15:15:31+08:00',
        description: '描述',
        name: '权限cassasb4vz',
        updatedAt: '2020-02-01T15:15:31+08:00',
      },
    ],
    totalCount: 1,
  }
```

### 角色添加权限

Authing.authz.addPermissionToRole\(input, options\)

* 参数：
  * input &lt;Object&gt;
    * permissionId:  &lt;string&gt; 权限 ID，必填。
    * roleId: &lt;string&gt; 角色 ID，必填。
  * options  &lt;object&gt; 选填 
    * fetchPermissions &lt;boolean&gt;  是否在结果中返回最新的权限列表。选填，默认为 false。
* 使用方法

```javascript
const res = await authing.authz.addPermissionToRole({
  roleId: "",
  permissionId: ""
})
```

* 返回数据

操作成功示例：

```javascript
{ 
    message: '操作成功',
    code: 200
}
```

如果操作失败，需要使用 try catch 捕捉异常，详情见 **异常处理** 部分。

### 角色批量添加权限

Authing.authz.addPermissionToRoleBatch\(input, options\)

* 参数：
  * input &lt;Object&gt;
    * permissionIdList:  &lt;Array&gt; 权限 ID 列表，必填。
    * roleId: &lt;string&gt; 角色 ID，必填。
  * options  &lt;object&gt; 选填 
    * fetchPermissions &lt;boolean&gt;  是否在结果中返回最新的权限列表。选填，默认为 false。
* 使用方法

```javascript
const res = await authing.authz.addPermissionToRole({
  roleId: "",
  permissionIdList: ["",""]
})
```

* 返回数据

操作成功示例：

```javascript
{ 
    message: '操作成功',
    code: 200
}
```

如果操作失败，需要使用 try catch 捕捉异常，详情见 **异常处理** 部分。

{% hint style="warning" %}
批量操作，当且仅当所有操作成功时才会返回成功，如果其中任意一个步骤失败，其他所有操作将会 rollback，回退到之前的状态。操作失败需要用 try/catch 捕获异常，详情见**异常处理**部分。
{% endhint %}

### 角色删除权限

Authing.authz.removePermissionFromRole\(input, options\)

* 参数：
  * input &lt;Object&gt;
    * permissionId:  &lt;string&gt; 权限 ID，必填。
    * roleId: &lt;string&gt; 角色 ID，必填。
  * options  &lt;object&gt; 选填 
    * fetchPermissions &lt;boolean&gt;  是否在结果中返回最新的权限列表。选填，默认为 false。
* 使用方法

```javascript
const res = await authing.authz.removePermissionFromRole({
  roleId: "",
  permissionId: ""
})
```

* 返回数据

操作成功示例：

```javascript
{ 
    message: '操作成功',
    code: 200
}
```

如果操作失败，需要使用 try catch 捕捉异常，详情见 **异常处理** 部分。

### 角色批量删除权限

Authing.authz.removePermissionFromRoleBatch\(input, options\)

* 参数：
  * input &lt;Object&gt;
    * permissionId:  &lt;string&gt; 权限 ID，必填。
    * roleId: &lt;string&gt; 角色 ID，必填。
  * options  &lt;object&gt; 选填 
    * fetchPermissions &lt;boolean&gt;  是否在结果中返回最新的权限列表。选填，默认为 false。
* 使用方法

```javascript
const res = await authing.authz.removePermissionFromRoleBatch({
  roleId: "",
  permissionIdList: ["",""]
})
```

* 返回数据

操作成功示例：

```javascript
{ 
    message: '操作成功',
    code: 200
}
```

如果操作失败，需要使用 try catch 捕捉异常，详情见 **异常处理** 部分。

{% hint style="warning" %}
批量操作，当且仅当所有操作成功时才会返回成功，如果其中任意一个步骤失败，其他所有操作将会 rollback，回退到之前的状态。操作失败需要用 try/catch 捕获异常，详情见**异常处理**部分。
{% endhint %}

## 错误码

角色权限管理的相关错误码以 39xx 开头。

如何获取错误码? 如下所示：

```javascript
try {
  const group = await authing.authz.group("NOGTEXIST")
} catch (error) {
  const errcode = error.message.code
  assert(errcode === 3901)
}
```

| 错误码 | 说明 |
| :--- | :--- |
| 2020 | 尚未登录，无访问权限 |
| 3901 | Group 不存在 |
| 3902 | 无权操作此 Group |
| 3903 | Role 不存在 |
| 3904 | 无权操作此 Role |
| 3905 | Permission 不存在 |
| 3906 | 无权操作此 Permission |
| 3907 | 删除 Group 失败 |
| 3910 | Role ${role.name} 已在 Group ${group.name} 中了 |
| 3911 | Role ${role.name} 不在 Group ${group.name} 中 |
| 3912 | User ${user.\_id} 已在 Group ${group.name} 中了 |
| 3913 | User ${user.\_id} 不在 Group ${group.name} 中 |
| 3914 | 删除 Permission 失败 |
| 3915 | 删除 Role 失败 |
| 3916 | Permission ${permission.name} 已在 Role ${role.name} 中了 |
| 3917 | Permission ${permission.name} 不在 Role ${role.name} 中 |
| 3918 | User ${user.\_id} 已具备 Role ${role.name} |
| 3919 | User ${user.\_id} 不具备 Role ${role.name} |
|  |  |

