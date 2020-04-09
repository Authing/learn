# 移动应用 SSO

移动应用 SSO（Single Sign On，单点登录） 和传统 [Web 端 SSO](../../quickstart/implement-sso-with-authing.md) 类似，指的是在多个应用系统中，用户只需要登录一次就可以访问所有相互信任的应用系统。

 Authing 目前共支持两种形式的移动端单点登录方式：

1. 自动检测同一设备上关联应用的登录态
2. 唤起关联 App 以交换用户信息

### 自动检测方式

自动检测方式，和美团系 App 类似，可以实现同一设备上所有相互信任系统只要有其中一个处于登录状态，就能检测出相关用户，提示用户是否使用该账号登录，从而实现单点登录。

如下图所示：

![](../../.gitbook/assets/image%20%28580%29.png)

你还可以查看这个视频的演示：

[https://cdn.authing.cn/videos/Authing%20App%20SSO%20Demo.mp4](https://cdn.authing.cn/videos/Authing%20App%20SSO%20Demo.mp4)

具体接入方式请见：

{% page-ref page="track-session.md" %}

### 唤起  App 方式

唤起 App 方式，指的是在应用 A 唤起应用 B，用户在应用 B 内同意授权登录，之后跳转会应用 A，应用 A 通过某种方式获取用户信息。这种方式无法做到自动检测其他 App 的登录态，建议使用自动检测方式。

具体接入方式请见：

{% page-ref page="launch-app.md" %}



 

