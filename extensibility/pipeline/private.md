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

* ACCOUNT\_ID: 阿里云账号 ID，可在阿里云控制台右上角 -&gt; 基本资料 -&gt; 安全设置 -&gt; 账号 ID。[详情请点击此](https://help.aliyun.com/document_detail/52984.html?spm=a2c4g.11186623.2.49.49772a364IfiEO#getAccountID)。
* REGION: 地区，如 cn-beijing, cn-hangzhou。
* ACCESS\_KEY\_ID: 在阿里云控制台的右上角 -&gt; 小人头像 -&gt; accesskeys 即可获取；
* ACCESS\_KEY\_SECRET: 在阿里云控制台的右上角 -&gt; 小人头像 -&gt; accesskeys 即可获取；
* TIMEOUT: 上传 pipeline 函数超时时间。
* RETRIES: 上传 pipeline 函数最多重试次数。



