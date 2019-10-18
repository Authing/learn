# 管理 MFA 口令

## 查询 MFA 状态

**Authing.queryMFA\(options\)**

允许用户本人或用户池所有者查看 MFA 的信息。

**传参的两种方式：**

1. 通过**用户 id** 和**用户池 id** 参数来查询一个用户的 MFA 信息，此时 **userId** 和 **userPoolId** 两个参数必填。
2. 也可以通过 **MFA 主体的 id** 来查询 MFA 的信息，此时只需传入 \_id 参数，userId 和 userPoolId 参数可以不传。

* **参数:**
  * `{Object} options`
    * \_id `{String}，MFA 主体的 id`
    * userId `{String}，用户 id`
* **使用方法:**
  * ```javascript
    (async function() {
      const authing = new Authing({
        userPoolId: 'your_userpool_id'
      });
      try {
        const login = await authing.login({
          email: 'test@test.com',
          password: '123456'
        })
        await authing.queryMFA({
          userId: '用户 id',
        })
      } catch (err) {
       console.log(err)
      }
    })();
    ```
* **返回数据:**
  * ```javascript
    {
        "_id": "5d6789aea9ee8e9a77b73521",
        "userId": "5d678970a9ee8e8f75b7350f",
        "userPoolId": "5cbd6716aaaa70cb9a58d86f",
        "shareKey": "NRMFAWTPMZ5FOQZWKNJFS5BXOZXGOV3Z",
        "enable": true
    }
    ```

## 更改 MFA 状态

**Authing.changeMFA\(options\)**

只允许用户本人修改自己的 MFA 口令，在使用该方法前需要先使用用户账户登录，或使用用户的 token 进行初始化。

**传参的两种方式：**

1. 通过**用户 id** 和**用户池 id** 参数来指定一个用户的 MFA 状态，此时 **userId** 和 **userPoolId** 两个参数必填。
2. 也可以通过 **MFA 主体的 id** 来修改 MFA 的状态，此时只需传入 \_id 参数，userId 和 userPoolId 参数可以不传。

* **参数:**
  * `{Object} options`
    * \_id `{String}，MFA 主体的 id`
    * userId `{String}，用户 id`
    * enable `{Boolean}，MFA 开启或关闭`
    * refreshKey `{Boolean}，是否刷新 MFA secret`

**开启用户的 MFA**

* **使用方法:**
  * ```javascript
    (async function() {
      const authing = new Authing({
        userPoolId: 'your_userpool_id'
      });
      try {
        // 需要先以用户的身份登录
        await authing.login({
          email: 'test@authing.cn',
          password: 'password'
        });
        // 或者使用用户的 token 初始化
        // authing.initUserClient('用户的 JWT token');
        await authing.changeMFA({
          userId: '用户 id',
          enable: true,
        })
      } catch (err) {
       console.log(err)
      }
    })();
    ```
* **返回数据:**
  * ```javascript
    {
        "_id": "5d6789aea9ee8e9a77b73521",
        "userId": "5d678970a9ee8e8f75b7350f",
        "userPoolId": "5cbd6716aaaa70cb9a58d86f",
        "shareKey": "NRMFAWTPMZ5FOQZWKNJFS5BXOZXGOV3Z",
        "enable": true
    }
    ```

**刷新用户的 MFA secret**

* **使用方法:**
  * ```javascript
    (async function() {
      const authing = new Authing({
        userPoolId: 'your_userpool_id'
      });
      try {
        // 需要先以用户的身份登录
        await authing.login({
          email: 'test@authing.cn',
          password: 'password'
        });
        // 或者使用用户的 token 初始化
        // authing.initUserClient('用户的 JWT token');
        await authing.changeMFA({
          userId: '用户 id',
          enable: true,
          refeshKey: true
        })
      } catch (err) {
       console.log(err)
      }
    })();
    ```
* **返回数据:**
  * ```javascript
    {
        "_id": "5d6789aea9ee8e9a77b73521", // MFA 主体的 id
        "userId": "5d678970a9ee8e8f75b7350f",
        "userPoolId": "5cbd6716aaaa70cb9a58d86f",
        "shareKey": "JVRWQOLHOJJVOZSOGN4G4OKWNVHXQMSQ", // 新的密钥
        "enable": true
    }
    ```



