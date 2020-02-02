# 查询用户权限

了解如何配置用户角色、权限：

{% page-ref page="../sdk-for-node/rbac.md" %}

## 获取用户权限列表

Authing.userPermissionList\(\_id, options\)

* 参数
  * \_id &lt;string&gt; 用户 ID，必填。
* 使用方法

```javascript
const permissionLost = await authing.userPermissionList("USER ID")
```

* 返回数据
  * totalCount 权限总数
  * list 权限列表
  * rawList 权限列表，只包含 name

```javascript
{
    totalCount: 1,
    list: [
      {
        _id: '5e20082f4b6419665991fa57',
        name: "invoice:submit",
        description: '提交发票报销申请。',
        
      }
    ],
    rawList: ['invoice:submit']
}
```



## 获取用户角色列表

Authing.userRoleList\(\_id, options\)

* 参数
  * \_id &lt;string&gt; 用户 ID，必填。
* 使用方法

```javascript
const permissionLost = await authing.userRoleList("USER ID")
```

* 返回数据
  * totalCount 权限总数
  * list 权限列表
  * rawList 权限列表，只包含 name

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
  ],
  rawList: [
    "Invoice Submitter",
    "Vacation Requester"
  ]
}
```

## 获取用户分组列表

Authing.userGroupList\(\_id, options\)

* 参数
  * \_id &lt;string&gt; 用户 ID，必填。
* 使用方法

```javascript
const permissionLost = await authing.userGroupList("USER ID")
```

* 返回数据

```javascript
{
  totalCount: 2,
  list: [
    {
      _id: '5e1fff324b6419665991f6b4',
      name: 'employee',
      description: '正式员工。'
    },
    {
      _id: '5e1edcb34b6419665991f5d9',
      name: 'intern',
      description: '实习生。'
    },
  ],
  rawList: ["employee", "intern"]
}
```

## 如何与业务结合

假设你有一个接口，只有具备特定权限的用户可以访问：

```javascript
app.get('/api/example', async (req, res) => {
    const userId = req.user._id;
    const res = await authing.userPermissionList(userId)
    const { rawList: permissions } = res;
    if(permissions.indexOf('invoice:submit') === -1){
        res.json({
            code: 403,
            message: "没有权限访问"
        })
    }
    
    // ...
})
```





