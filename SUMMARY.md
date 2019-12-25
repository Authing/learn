# Table of contents

* [欢迎](README.md)
* [Authing 概述](overviews.md)

## 快速开始 <a id="quickstart"></a>

* [第一个应用](quickstart/hello-world.md)
* [实现单点登录](quickstart/implement-sso-with-authing.md)
* [基础概念](quickstart/basic.md)
* [控制台概览](quickstart/dashboard.md)
* [部署模型和价格](quickstart/deployment.md)
* [最佳开发实践](quickstart/best-practise.md)

## 进阶指南 <a id="advanced"></a>

* [理解认证和授权](advanced/authentication/README.md)
  * [用户名 - 密码认证](advanced/authentication/username-password.md)
  * [邮箱 - 密码认证](advanced/authentication/email-password.md)
  * [手机 - 验证码认证](advanced/authentication/phone-code.md)
  * [JWT Token 释义及使用](advanced/authentication/jwt-token.md)
* [验证 JWT Token](advanced/verify-jwt-token.md)
* [配置用户权限](advanced/roles.md)
* [接入小程序扫码登录](advanced/wxapp-qrcode/README.md)
  * [接入私有化小程序](advanced/wxapp-qrcode/private-wxapp.md)
* [接入社会化登录](advanced/social-login/README.md)
  * [接入移动端支付宝登录](advanced/social-login/jie-ru-yi-dong-duan-zhi-fu-bao-deng-lu.md)
  * [接入微信手机网页授权登录](advanced/social-login/wechatmp-login.md)
  * [在云上配置社会化登录](advanced/social-login/config-social-login-in-cloud.md)
* [接入 OAuth 2.0](advanced/oauth2/README.md)
  * [创建 OAuth 应用](advanced/oauth2/create-oauth2.md)
  * [使用 OAuth 授权](advanced/oauth2/oauth-authorization.md)
* [接入 OpenID Connect](advanced/oidc/README.md)
  * [创建 OIDC 应用](advanced/oidc/create-oidc.md)
  * [使用 OIDC 授权](advanced/oidc/oidc-authorization.md)
  * [理解 OIDC 流程](advanced/oidc/understand-oidc.md)
  * [OIDC 常见问题](advanced/oidc/oidc-params.md)
* [配置 LDAP 服务](advanced/ldap.md)
* [使用 Authing 的 LDAP 用户目录](advanced/ldap-idp.md)
* [接入 SAML](advanced/use-saml/README.md)
  * [创建 SAML Identity Provider 应用](advanced/use-saml/create-saml-identity-provider.md)
  * [创建 SAML Service Provider 应用](advanced/use-saml/create-saml-service-provider.md)
  * [理解 SAML 流程](advanced/use-saml/li-jie-saml-liu-cheng.md)
  * [同时使用 Authing 作为 SP 和 IdP](advanced/use-saml/configure-authing-as-sp-and-idp.md)
  * [使用 SAML Identity Provider](advanced/use-saml/use-saml-identity-provider/README.md)
    * [在阿里云访问管理中使用](advanced/use-saml/use-saml-identity-provider/use-in-aliyun.md)
    * [在腾讯云访问管理中使用](advanced/use-saml/use-saml-identity-provider/use-in-tencent-cloud.md)
    * [在 Auth0 中使用](advanced/use-saml/use-saml-identity-provider/use-in-auth0.md)
  * [使用 SAML Service Provider](advanced/use-saml/use-saml-service-provider/README.md)
    * [与 Auth0 SAML IdP 对接](advanced/use-saml/use-saml-service-provider/use-with-auth0.md)
    * [与 SSOCircle SAML IdP 对接](advanced/use-saml/use-saml-service-provider/use-with-ssocircle.md)
* [使用 Webhook](advanced/use-webhook.md)
* [迁移老数据进 Authing](advanced/ru-he-qian-yi-lao-shu-ju-jin-authing.md)
* [错误代码](advanced/error-code.md)

## 开发资源 <a id="sdk"></a>

* [开发资源](sdk/libraries.md)
* [API（GraphQL）](sdk/open-graphql.md)
* [Guard for Web](sdk/guard/README.md)
  * [自定义 Guard 的 UI 样式](sdk/guard/custom-guard-ui.md)
* [Guard for Mobile](sdk/guard-for-mobile/README.md)
  * [Guard for React Native](sdk/guard-for-mobile/guard-for-react-native.md)
* [Guard VS 自定义 UI](sdk/loginform-vs-custom-ui.md)
* [SDK for JavaScript](sdk/sdk-for-javascript/README.md)
  * [绑定社会化登录](sdk/sdk-for-javascript/bind-social-accounts.md)
  * [管理 MFA 口令](sdk/sdk-for-javascript/guan-li-mfa-kou-ling.md)
  * [自定义请求链接](sdk/sdk-for-javascript/zi-ding-yi-qing-qiu-lian-jie.md)
* [SDK for Node.js](sdk/sdk-for-node/README.md)
  * [读取修改用户权限](sdk/sdk-for-node/update-user-permissions.md)
  * [绑定社会化登录](sdk/sdk-for-node/bind-social-login.md)
  * [管理 MFA 口令](sdk/sdk-for-node/manage-mfa.md)
  * [自定义请求连接](sdk/sdk-for-node/custom-host.md)
* [SDK for React Native](sdk/sdk-for-react-native.md)
* [SDK for 微信小程序](sdk/authing-sdk-for-wxapp.md)
* [SDK for Java](sdk/sdk-for-java.md)
* [SDK for Objective-C](sdk/sdk-for-objective-c.md)
* [SDK for Python](sdk/sdk-for-python.md)
* [SDK for Go](sdk/sdk-for-go.md)
* [SDK for PHP](sdk/sdk-for-php.md)
* [函数计算（FaaS）](sdk/faas.md)

## 通信 <a id="messages"></a>

* [邮件](messages/email.md)
* [SMS](messages/sms.md)

## MFA

* [配置 MFA 安全口令](mfa/configure-mfa.md)
* [接入 MFA](mfa/integrate-mfa.md)

## 安全 <a id="security"></a>

* [配置 Web 安全域](security/cors.md)
* [配置用户池密码强度](security/config-user-pool-password-level.md)
* [配置密码加密函数](security/config-pwd-encrypt-function.md)

## 其他 <a id="others"></a>

* [常见问题](others/faq.md)
* [集成案例](others/cases/README.md)
  * [使用 Authing 补充 AWS 中国区 Cognito User Pool](others/cases/shi-yong-authing-bu-chong-aws-zhong-guo-qu-cognito-user-pool.md)
* [社交互联数据](others/solid/README.md)
  * [Authing 与社交互联数据](others/solid/authing-solid.md)
* [为 Authing 贡献 SDK](others/wei-authing-gong-xian-sdk/README.md)
  * [了解 Authing 的模块](others/wei-authing-gong-xian-sdk/le-jie-authing-de-mo-kuai.md)

