# 老版权限控制（deprecated）



{% hint style="danger" %}
此部分接口将会被废弃，请使用新版完整基于 RBAC 的角色权限管理。
{% endhint %}

{% page-ref page="rbac.md" %}

## 

你可以在控制台中创建角色、为角色配置权限和为角色指派用户（配置方式请参考：[配置用户角色和权限](https://learn.authing.cn/authing/advanced/roles)），然后使用本页的 API 根据用户 ID  在程序中读取或修改用户角色和权限。

## 获取用户权限和角色

**Authing.queryPermissions\(userId\)**

* **参数:**
  * `{String} userId`
* **使用方法:**
  * ```javascript
    Authing.queryPermissions('USER_ID');
    (async function() {
      const authing = new Authing({
        userPoolId: 'your_userpool_id',
        secret: 'your_userpool_secret'
      });
  
      const groupList = await authing.queryPermissions('USER_ID')
        .catch((error) => { ... });;
    })();
    ```
* **返回数据:**
  * ```javascript
    {
      "totalCount": 2,
      "list": [
        {
          "group": {
            "name": "管理员",
            "permissions": "{\"routes\": \"/api/v1\"}"
          }
        },
        {
          "group": {
            "name": "读者",
            "permissions": ""
          }
        }
      ]
    }
    ```

## 获取应用下所有角色

**Authing.queryRoles\(options\)**

* **参数:**
  * `{Object} options`
    * page: 第几页，选填，默认为 1
    * count: 总数，选填，默认为 10
* **使用方法:**
  * ```javascript
    (async function() {
      const authing = new Authing({
        userPoolId: 'your_userpool_id',
        secret: 'your_userpool_secret'
      });
  
      const rolesList = await authing.queryRoles({
        page: 1,
        count: 10
      })
        .catch((error) => { ... });;
    })();
    ```
* **返回数据:**
  * ```javascript
    {
      "totalCount": 1,
      "list": [
        {
          "_id": "5ca5c3a88a61c7304fb1299a",
          "name": "管理员",
          "client": "5c9c659cb9440b05cb2570e6",
          "descriptions": null,
          "createdAt": "Thu Apr 04 2019 16:43:20 GMT+0800 (CST)",
          "permissions": "{\"routes\": \"/api/v1\"}"
        }
      ]
    }
    ```

## 创建用户角色

**Authing.createRole\(options\)**

* **参数:**
  * `{Object} options`
    * name: 角色名称，必填
    * descriptions: 角色描述，必填
* **使用方法:**
  * ```javascript
    (async function() {
      const authing = new Authing({
        userPoolId: 'your_userpool_id',
        secret: 'your_userpool_secret'
      });
  
      const rolesList = await authing.createRole({
        name: '测试角色',
        descriptions: '测试角色的描述'
      })
        .catch((error) => { ... });;
    })();
    ```
* **返回数据:**
  * ```javascript
    {
      "_id": "5ca5c5dd8a61c7ffbfb129a9",
      "name": "测试角色",
      "client": "5c9c659cb9440b05cb2570e6",
      "descriptions": "测试角色的描述"
    }
    ```

## 修改角色权限

**Authing.updateRolePermissions\(options\)**

* **参数:**
  * `{Object} options`
    * name: 角色名称，必填
    * roleId: 角色 ID，必填
    * permissions: 角色权限，必填。输入自定义的权限字符串，可以是 JSON 或数组；之后可以通过 API 获取此处设置的权限既而实现自己的业务逻辑。
* **使用方法:**
  * ```javascript
    (async function() {
      const authing = new Authing({
        userPoolId: 'your_userpool_id',
        secret: 'your_userpool_secret'
      });
  
      const rolesList = await authing.updateRolePermissions({
        name: '测试角色',
        roleId: '5ca5c5dd8a61c7ffbfb129a9',
        permissions: 'route:all'
      })
        .catch((error) => { ... });;
    })();
    ```
* **返回数据:**
  * ```javascript
    {
      "_id": "5ca5c5dd8a61c7ffbfb129a9",
      "name": "测试角色",
      "client": "5c9c659cb9440b05cb2570e6",
      "descriptions": "测试角色的描述",
      "permissions": "route:all"
    }
    ```

## 指派用户到某角色

**Authing.assignUserToRole\(userId\)**

* **参数:**
  * `{String} userId`
* **使用方法:**
  * ```javascript
    (async function() {
      const authing = new Authing({
        userPoolId: 'your_userpool_id',
        secret: 'your_userpool_secret'
      });
  
      const rolesList = await authing.assignUserToRole('USER_ID')
        .catch((error) => { ... });;
    })();
    ```
* **返回数据:**
  * ```javascript
    {
      "totalCount":3,
      "list":[
        {
          "_id":"5d21bb48ec626c076d82c31a",
          "client":{
            "_id":null,
          },
          "user":{
            "_id":null,
          },
          "createdAt":"Sun Jul 07 2019 17:28:40 GMT+0800 (CST)",
        }
      ],
    }
    ```

## 将用户从某角色中移除

**Authing.removeUserFromRole\(options\)**

* **参数:**
  * `{Object} options`
    * roleId: 角色 ID，必填
    * user: 要指派的用户 ID，必填
* **使用方法:**
  * ```javascript
    (async function() {
      const authing = new Authing({
        userPoolId: 'your_userpool_id',
        secret: 'your_userpool_secret'
      });
  
      const rolesList = await authing.removeUserFromRole({
        user: '5ca332d265520f3d751b0f63',
        roleId: '5ca5c5dd8a61c7ffbfb129a9',
      })
        .catch((error) => { ... });;
    })();
    ```
* **返回数据:**
  * ```javascript
    {
      "_id": null,
      "client": null,
      "group": null,
      "user": null
    }
    ```

## 获取某角色下全部用户

* **参数:**
  * `{Object} options`
    * roleId: 角色 ID，必填
    * page: 第几页，选填，默认为 1
    * count: 总数，选填，默认为 10
* **使用方法:**
  * ```javascript
    (async function() {
      const authing = new Authing({
        userPoolId: 'your_userpool_id',
        secret: 'your_userpool_secret'
      });
  
      const userList = await authing.getUsersByRole({
        roleId: '5da9c92a2a24432643a33969'
      })
        .catch((error) => { ... });;
    })();
    ```
* **返回数据:**
  * ```javascript
    {
        "totalCount": 2,
        "list": [
            {
                "_id": "5dc9695999128a6baf15e44b",
                "group": {
                    "_id": "5da9c92a2a24432643a33969"
                },
                "client": {
                    "_id": "5c95905578fce5000166f853"
                },
                "user": {
                    "_id": "5dc8c6b7ebafee83c95b9c8b",
                    "photo": "https://usercontents.authing.cn/authing-avatar.png",
                    "username": "hrpw0awin7d@test.com",
                    "email": "hrpw0awin7d@test.com"
                },
                "createdAt": "2019-11-11 21:59:53"
            },
            {
                "_id": "5dc9694f99128a988c15e42a",
                "group": {
                    "_id": "5da9c92a2a24432643a33969"
                },
                "client": {
                    "_id": "5c95905578fce5000166f853"
                },
                "user": {
                    "_id": "5dc8cadfebafeedffc5bb08d",
                    "photo": "https://usercontents.authing.cn/authing-avatar.png",
                    "username": "egesarhersh",
                    "email": "aegaerhibg@123.com"
                },
                "createdAt": "2019-11-11 21:59:43"
            }
        ]
    }
    ```

