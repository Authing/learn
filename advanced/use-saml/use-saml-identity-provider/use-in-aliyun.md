# 在阿里云访问管理中使用

Authing SAML Identity Provider 为企业提供了单点登录能力。在阿里云通过设置SSO来实现从企业本地账号系统登录到阿里云，从而实现员工身份在云上和云下的统一管理。

阿里云采用了基于SAML 2.0的身份联盟标准来实现身份系统的互通。

阿里云支持「用户 SSO」和「角色 SSO」，下面分这两种模式进行说明。

## 用户 SSO

### 在 Authing 配置 IdP

先在 Authing 新建一个 SAML Identity Provider 应用。

进入应用控制台 -&gt; 第三方登录 -&gt; SAML IdP 选项卡，点击右侧的「创建 SAML IdP」。

在弹出的窗口中填入应用信息，填写一个二级域名作为 SAML IdP 的云上地址，点击「确定」。

在 AssertionConsumeServiceURL 中输入阿里云的 SAML 断言接收地址

`https://signin.aliyun.com/saml/SSO`

进入阿里云访问控制 -&gt; 左侧菜单 -&gt; 设置 -&gt; 高级设置

下方提示的「SAML服务提供方元数据URL」右侧，点击链接，下载 SAML SP Metadata，并上传。

![&#x4E0B;&#x8F7D;&#x963F;&#x91CC;&#x4E91;&#x7684; SAML &#x5143;&#x6570;&#x636E;&#x6587;&#x6863;](../../../.gitbook/assets/image%20%28395%29.png)

![&#x8F93;&#x5165;&#x963F;&#x91CC;&#x4E91;&#x7684; ACS &#x5E76;&#x4E0A;&#x4F20;&#x963F;&#x91CC;&#x4E91;&#x7684; SAML Metadata](../../../.gitbook/assets/image%20%28348%29.png)

在 SAML Response 签名处，上传或输入一对秘钥，并正确选择秘钥算法。

其余配置使用默认即可。

点击「确定」。

在使用方法页面点击「下载」IdP Metadata。

### 在阿里云进行配置

使用你的阿里云账号[登录](https://aliyun.com)阿里云的控制台。

在你的用户头像上悬停鼠标，出现下拉菜单，点击「访问控制」。

![](../../../.gitbook/assets/image%20%2878%29.png)

进入左侧菜单 -&gt; 人员管理 -&gt; 用户，点击「新建用户」。

![](../../../.gitbook/assets/image%20%284%29.png)

输入用户名称、显示名称，勾选控制台密码登录并点击「确认」。本教程中输入的登录名称是 authing，显示名称也是 authing。

![](../../../.gitbook/assets/image%20%28103%29.png)

点击左侧菜单 -&gt; 人员管理 -&gt; 用户，在右侧页面的列表中可以看到刚刚添加的用户，记录下用户登录名称（xxx@xxx.onaliyun.com），随后会用到。点击对应用户条目右侧的「添加权限」。

![](../../../.gitbook/assets/image%20%28307%29.png)

在权限列表中选择需要赋予该账户的权限，本教程选择「AdministratorAccess」最高权限。点击「确定」。

![](../../../.gitbook/assets/image%20%2850%29.png)

进入左侧菜单 -&gt; 设置，右侧页面点击「高级设置」选项卡，在下方 SSO 登录设置处点击「编辑 SSO 登录设置」。

SSO 功能状态选择「开启」。点击「上传文件」，上传刚刚在 Authing 下载的 SAML IdP Metadata。点击「确认」。

![](../../../.gitbook/assets/image%20%28246%29.png)

### 在 Authing 配置用户

阿里云要求用户的电子邮件域必须和用户的登录名称中「@」后的内容一样，因此，在创建用户时，email 需要和阿里云中的用户登录名称保持一致，形如 authing@xxx.onaliyun.com 这种形式。

进入应用控制台 -&gt; 用户中心，点击「创建用户」红色按钮，填入相关信息，**注意**邮箱要填入阿里云中的用户登录名称。点击「创建」。

![](../../../.gitbook/assets/image%20%28179%29.png)

### 使用 Authing IdP 登录阿里云

访问[阿里云 RAM 账户登录页](https://signin.aliyun.com)，输入刚才在阿里云新建的用户的登录名称，形如：authing@xxx.onaliyun.com。

![](../../../.gitbook/assets/image%20%28166%29.png)

点击「下一步」，会跳转到 Authing IdP 登录认证页面。

![](../../../.gitbook/assets/image%20%28352%29.png)

可以选择任意方式进行登录，但是该用户的邮箱字段必须和阿里云中配置的一样。这里我们可以直接输入用户名，或者邮箱进行登录。

![&#x8FDB;&#x5165;&#x786E;&#x6743;&#x9875;](../../../.gitbook/assets/image%20%28144%29.png)

登录成功后回跳转到阿里云的控制台。

![](../../../.gitbook/assets/image%20%28156%29.png)



## 角色 SSO

