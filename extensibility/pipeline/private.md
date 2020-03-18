# 私有化部署

如果你要私有化部署 Authing（[详情看这里](../../quickstart/deployment.md)），需要在配置文件中提供以下信息：

```yaml
faas:
    ACCOUNT_ID: ""
    REGION: ""
    ACCESS_KEY_ID: ""
    ACCESS_KEY_SECRET: ""
    TIMEOUT: 100
    RETRIES: 3
```

* ACCOUNT\_ID: 阿里云账号 ID， 在 **阿里云控制台右上角** 选择 **个人头像，**点击**安全设置，**在此页面可以获取到账号 ID。[详情请点击此](https://help.aliyun.com/document_detail/52984.html?spm=a2c4g.11186623.2.49.49772a364IfiEO#getAccountID)。

![](../../.gitbook/assets/image%20%28104%29.png)

![](../../.gitbook/assets/image%20%28137%29.png)

![](../../.gitbook/assets/image%20%28291%29.png)

* REGION: 地区，如 cn-beijing, cn-hangzhou。
* ACCESS\_KEY\_ID: 在 **阿里云控制台右上角** 选择 **个人头像**，点击 **AccessKey 管理**，在此页面可获得。

![](../../.gitbook/assets/image%20%28111%29.png)

* ACCESS\_KEY\_SECRET: 在 **阿里云控制台右上角** 选择 **个人头像**，**点击 AccessKey 管理**，在此页面可获得。
* TIMEOUT: 上传 pipeline 函数超时时间。
* RETRIES: 上传 pipeline 函数最多重试次数。



