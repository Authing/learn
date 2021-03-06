# SDK for PHP

Authing SDK for PHP 支持 PHP 5.6+。

Github 地址：[https://github.com/Authing/authing-php-sdk](https://github.com/Authing/authing-php-sdk)。

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

## 接口文档

```php
<?php
require __DIR__.'/../vendor/autoload.php';
use Authing\Client;
try {
    $data = [
        'clientId' => 'your id',
        'secret'   => 'your secret',
    ];
    $client = new Client($data);
    /**
     * 登录接口
     */
//    $client->login([
//                       'email' => '376155014@qq.com',
//                       'password' => '654321',
//                   ]);
    /**
     * oauth 列表
     */
    //$client->ReadOAuthList();
    /**
     * 查看指定用户
     */
    //$client->user(['id' => '5ae816666fc94c0001569d9e'] );
    /**
     * 查看全部的用户
     */
    //$client->users(['page' => 1,'count'=> 2] );
    /**
     * 查看用户是否已经登录
     */
    //$client->checkLoginStatus();
    /**
     * 删除用户
     */
    //$client->removeUsers(['ids' => ['5ae04d373055230001023c6e']]);
    /**
     * 注册用户 支持多个参数
     */
    //$client->register(['email' => '155014@qq.com', 'password' => '123456']);
    /**
     * 发送确认邮箱邮件
     */
    //$client->sendVerifyEmail(['email' => '376155014@qq.com']);
    /**
     * 发送修改密码邮件
     */
    //$client->sendResetPasswordEmail(['email' => '376155014@qq.com']);
    /**
     * 确认验证码是否正确
     */
    //$client->verifyResetPasswordVerifyCode(['email' => '376155014@qq.com','verifyCode'=>8685]);
    /**
     * 修改密码
     */
    //$client->changePassword(['email' => '376155014@qq.com','verifyCode'=>8685,'password'=>'123456']);
    /**
     * 修改用户信息
     */
    //$client->updateUser(['id'=>'5ae816666fc94c0001569d9e','nickname'=>'123132','password'=>'123456']);
} catch (\Exception $e) {
    print_r($e->getMessage());
}
```



