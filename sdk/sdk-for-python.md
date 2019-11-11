# SDK for Python

Authing Python SDK 目前只支持 Python3+。

GitHub 地址：[https://github.com/Authing/authing-py-sdk](https://github.com/Authing/authing-py-sdk)。

## 安装

**pip**

当构建大规模应用时，我们推荐使用 `pip` 进行安装， 它可以与一些模块打包工具很好地配合使用。 注意，Authing 目前仅能从 pip3 以上安装。

```text
# latest stable
$ pip install authing
```

## 开始使用

首先在目录下新建一个名为 `pub.pem` 的文件，并将以下内容复制到文件中：

```text
-----BEGIN PUBLIC KEY-----
MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQC4xKeUgQ+Aoz7TLfAfs9+paePb
5KIofVthEopwrXFkp8OCeocaTHt9ICjTT2QeJh6cZaDaArfZ873GPUn00eOIZ7Ae
+TiA2BKHbCvloW3w5Lnqm70iSsUi5Fmu9/2+68GZRH9L7Mlh8cFksCicW2Y2W2uM
GKl64GDcIq3au+aqJQIDAQAB
-----END PUBLIC KEY-----
```

然后初始化 Authing：

```python
from authing.authing import Authing

clientId = 'your_client_id'
secret = 'your_app_secret'

authing = Authing(clientId, secret)

# 如果 authing 验证 clientId 和 secret 失败，将会抛出一个错误。所以在初始化构造函数的时候，可以使用 try...catch 保证程序不会挂掉。

user = authing.login(
    'test@testmail.com',
    'testpassword'
)

if user.get('errors'):
    # 出错
else:
    # 未出错
```

[如何获取 Client ID 和 Client Secret ？](../others/faq.md#ru-he-huo-qu-client-id-he-client-secret)

## 错误处理

SDK 中的接口返回数据若出错会存在 "errors" 字段，因此可以用如下代码检查是否出错：

```python
result = authing.xxx() # 执行 authing 的某方法

if result.get('errors'):
    # 出错，如 
    """
    {'code': 500, 'message': 'Cast to ObjectId failed for value "5aec1ea610ecb800018db176xx" at path "_id" for model "User"', 'data': None, 'errors': True}
    """
else:
    # 未发生错误，直接使用数据即可，如：
    """
    {'_id': '5aec1ea610ecb800018db176', 'email': 'xieyang@dodora.cn', 'isDeleted': False}
    """
```

如果在运行途中报错，请查看[错误代码列表](https://docs.authing.cn/#/quick_start/error_code)。

## 自定义请求链接

如果你私有部署了 Authing，可以通过以下方式初始化 URL：

```python
from authing.authing import Authing

clientId = 'your_client_id'
secret = 'your_app_secret'

authing = Authing(clientId, secret, {
    "oauth": 'https://oauth.your_url.com/graphql',
    "users": 'https://users.your_url.com/graphql'
})
```

## 接口文档

### 登录

#### 使用邮箱密码登录

```python
userInfo = authing.login(email='EMAIL', password='Password')
```

#### 使用用户名密码登录

```python
userInfo = authing.login(username='USERNAME', password='Password')
```

#### 使用短信验证码登陆

```python
userInfo = authing.loginByPhoneCode(
    phone="PHONE",
    phoneCode=PHONE_CODE
)
```

### 发送短信验证码

```python
success, msg = authing.getVerificationCode(phone)
if success:
    # 发送成功
    pass
else:
    # 发送失败
    print(msg)
```

### 注册

```python
userInfo = authing.register('EMAIL', 'Password')
```

### 查询用户数据

```python
userInfo = authing.user({'id': 'USER_ID'})
```

### 查询用户列表

```python
usersList = authing.list(page=1, count=10)
```

### 检查登录状态

```python
# Token 可通过登录之后返回的 userInfo 中的 token 字段获取
result = authing.checkLoginStatus('TOKEN')
```

### 修改用户资料

```python
result = authing.update({
    'KEY': 'VALUE'
})
```

其中可选 Key 如下所示：

* \_id `{String} 必填`
  * email `{String}，选填`
  * emailVerified: `{Boolean}，选填，邮箱是否经过验证`
  * username: `{String}，选填`
  * nickname: `{String}，选填`
  * company: `{String}，选填`
  * phone: `{String}，选填`
  * oauth: `{String}，选填，oauth 信息`
  * photo: `{String || file object}，选填，用户头像`
  * browser: `{String}，选填，用户注册时所用的浏览器`
  * password: `{String}，选填，用户密码`
  * oldPassword: `{String}（`当有 password 时，旧密码参数必需填写）
  * token: `{String}，选填`
  * tokenExpiredAt: `{String}，选填，token 过期时间`
  * loginsCount: `{Number}，选填，登录次数`
  * lastLogin: `{String}，选填，最后登录时间`
  * lastIP: `{String}，选填，最后登录 IP`
  * signedUp: `{String}，选填，注册时间`
  * blocked: `{Boolean}，选填，是否被锁定`
  * isDeleted: `{Boolean}，选填，是否被删除`

### 删除用户

```python
result = authing.remove('USER_ID')
```

