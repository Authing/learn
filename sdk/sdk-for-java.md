# SDK for Java

Authing SDK for JAVA 编写于 JDK 1.8

## 安装

**maven**

计划最近开始支持maven安装

## 开始使用

```text
Client client=new Client(clientId,secret);
        Option option=Option.builder()
                .putOption("xxxx",value)
                .putOption("xxxx",value)
                .putOption("xxxx",value)
                .build();
        String response= client.xxxxxx(option);
```

[如何获取 Client ID 和 Client Secret ？](https://docs.authing.cn/#/quick_start/howto)

## 错误处理

统一使用try...catch处理错误

如果在运行过程中出现了错误，请查看[错误代码列表](https://docs.authing.cn/#/quick_start/error_code)。

