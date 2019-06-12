# SDK for PHP

Authing SDK for PHP 支持 PHP 5.6+。

## 安装

### **composer**

当构建大规模应用时，我们推荐使用 `composer` 进行安装， 它可以与一些模块打包工具很好地配合使用。

```text
# latest stable
$ composer require authing/authing-php-sdk
```

## 开始使用

```text
use Authing\Client;

try {
    $data = [
        'clientId' => 'your id',
        'secret'   => 'your secret',
    ];
    
    $client = new Client($data);

    $client->login([
                       'email' => '376155014@qq.com',
                       'password' => '654321',
                   ]);
} catch (\Exception $e) {
    // 出错了
    print_r($e->getMessage());
}
```

[如何获取 Client ID 和 Client Secret ？](https://docs.authing.cn/#/quick_start/howto)

## 错误处理

统一使用 try...catch 处理错误

如果在运行途中报错，请查看[错误代码列表](https://docs.authing.cn/#/quick_start/error_code)。



