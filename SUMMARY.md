# Table of contents

* [欢迎](README.md)
* [Authing 概述](overviews.md)

## 快速开始 <a id="quickstart"></a>

* [注册 Authing 开发者账号](quickstart/create-authing-account.md)
* [第一个应用](quickstart/hello-world.md)
* [实现单点登录](quickstart/implement-sso-with-authing.md)
* [基础概念](quickstart/basic.md)
* [控制台概览](quickstart/dashboard.md)
* [部署模型和价格](quickstart/deployment.md)
* [最佳开发实践](quickstart/best-practise.md)
* [我该如何选择 Authing 的技术](quickstart/how-i-choose-authing-tech.md)

## 进阶指南 <a id="advanced"></a>

* [理解认证和授权](advanced/authentication/README.md)
  * [用户名 - 密码认证](advanced/authentication/username-password.md)
  * [邮箱 - 密码认证](advanced/authentication/email-password.md)
  * [手机 - 验证码认证](advanced/authentication/phone-code.md)
  * [JWT Token 释义及使用](advanced/authentication/jwt-token.md)
* [验证 Token](advanced/verify-jwt-token.md)
* [配置用户权限](advanced/roles.md)
* [迁移老数据进 Authing](advanced/ru-he-qian-yi-lao-shu-ju-jin-authing.md)
* [错误代码](advanced/error-code.md)

## 认证 <a id="authentication"></a>

* [接入 OAuth 2.0](authentication/oauth2/README.md)
  * [创建 OAuth 应用](authentication/oauth2/create-oauth2.md)
  * [使用 OAuth 授权](authentication/oauth2/oauth-authorization.md)
* [接入 OpenID Connect](authentication/oidc/README.md)
  * [创建 OIDC 应用](authentication/oidc/create-oidc.md)
  * [使用 OIDC 授权](authentication/oidc/oidc-authorization.md)
  * [理解 OIDC 流程](authentication/oidc/understand-oidc.md)
  * [OIDC 常见问题](authentication/oidc/oidc-params.md)
* [接入 SAML](authentication/use-saml/README.md)
  * [创建 SAML Identity Provider 应用](authentication/use-saml/create-saml-identity-provider.md)
  * [创建 SAML Service Provider 应用](authentication/use-saml/create-saml-service-provider.md)
  * [理解 SAML 流程](authentication/use-saml/principle.md)
  * [同时使用 Authing 作为 SP 和 IdP](authentication/use-saml/configure-authing-as-sp-and-idp.md)
  * [使用 SAML Identity Provider](authentication/use-saml/use-saml-identity-provider/README.md)
    * [在阿里云访问管理中使用](authentication/use-saml/use-saml-identity-provider/use-in-aliyun.md)
    * [在腾讯云访问管理中使用](authentication/use-saml/use-saml-identity-provider/use-in-tencent-cloud.md)
    * [在 Auth0 中使用](authentication/use-saml/use-saml-identity-provider/use-in-auth0.md)
  * [使用 SAML Service Provider](authentication/use-saml/use-saml-service-provider/README.md)
    * [与 Auth0 SAML IdP 对接](authentication/use-saml/use-saml-service-provider/use-with-auth0.md)
    * [与 SSOCircle SAML IdP 对接](authentication/use-saml/use-saml-service-provider/use-with-ssocircle.md)
* [接入 LDAP](authentication/ldap/README.md)
  * [配置 LDAP 服务](authentication/ldap/ldap.md)
  * [使用 Authing 的 LDAP 用户目录](authentication/ldap/ldap-idp.md)
* [接入 Active Directory](authentication/active-directory.md)

## 社会化登录 <a id="social-login"></a>

* [接入 Web 端社会化登录](social-login/web/README.md)
  * [GitHub 登录](social-login/web/github.md)
  * [微信 PC 扫码登录](social-login/web/wechat-pc.md)
  * [微信 APP 内网页登录](social-login/web/wechat-mp.md)
  * [企业微信（第三方应用）网页授权登录](social-login/web/wechatwork-web.md)
  * [企业微信（第三方应用）扫码登录](social-login/web/wechatwork-qrconnect.md)
  * [企业微信（企业内部）扫码登录](social-login/web/wechatwork-corp-qrconnect.md)
  * [新浪微博登录](social-login/web/weibo.md)
  * [钉钉登录](social-login/web/dingtalk.md)
  * [QQ 登录](social-login/web/qq.md)
  * [配置登录授权 Scope](social-login/web/social-login-scopes.md)
* [接入移动端社会化登录](social-login/mobile/README.md)
  * [微信登录](social-login/mobile/wechat.md)
  * [支付宝登录](social-login/mobile/alipay.md)
* [接入小程序登录](social-login/miniprogram/README.md)
  * [小程序登录](social-login/miniprogram/miniprogram/README.md)
    * [用户登录凭证（code）最佳实践](social-login/miniprogram/miniprogram/code-best-practice.md)
  * [APP 拉起小程序登录](social-login/miniprogram/app2wxapp.md)
* [打通微信账号体系](social-login/wechat-account.md)

## 扫码登录 <a id="scan-qrcode"></a>

* [小程序扫码登录](scan-qrcode/wxapp-qrcode/README.md)
  * [接入私有化小程序](scan-qrcode/wxapp-qrcode/private-wxapp.md)
* [APP 扫码登录](scan-qrcode/app-qrcode/README.md)
  * [快速接入](scan-qrcode/app-qrcode/quick-start.md)
  * [Web 端 SDK](scan-qrcode/app-qrcode/web-side-sdk.md)
  * [完整接口列表](scan-qrcode/app-qrcode/full-api-list.md)
  * [自定义配置项](scan-qrcode/app-qrcode/customize.md)
  * [扫码登录原理](scan-qrcode/app-qrcode/how-it-works.md)

## 移动应用登录 <a id="mobile"></a>

* [移动应用 SSO](mobile/mobile-app-sso/README.md)
  * [自动检测登录](mobile/mobile-app-sso/track-session.md)
  * [唤起 App 登录](mobile/mobile-app-sso/launch-app.md)
* [移动应用身份提供商](mobile/yi-dong-ying-yong-shen-fen-ti-gong-shang.md)

## 授权 <a id="authorization"></a>

* [Authorization 基础概念](authorization/authorization/README.md)
  * [什么是基于角色的管理\(RBAC\)](authorization/authorization/rbac.md)
  * [什么是基于属性的管理\(ABAC\)](authorization/authorization/abac.md)
  * [什么是零信任网络](authorization/authorization/zero-trust-network.md)
* [Authorization at Authing](authorization/authorization-at-authing.md)
* [接入 RBAC](authorization/intergrate-rbac.md)
* [接入 ABAC](authorization/intergrate-abac.md)
* [接入组织机构管理](authorization/intergrate-organization-structure.md)

## 扩展能力 <a id="extensibility"></a>

* [自定义认证流程（Pipeline）](extensibility/pipeline/README.md)
  * [创建你的第一个 Pipeline 函数](extensibility/pipeline/write-your-first-pipeline-function.md)
  * [Pipeline 函数开发指南](extensibility/pipeline/pipeline-function-api-doc.md)
  * [user 对象](extensibility/pipeline/user-object.md)
  * [context 对象](extensibility/pipeline/context-object.md)
  * [使用环境变量](extensibility/pipeline/env.md)
  * [可用的 Node Modules](extensibility/pipeline/available-node-modules.md)
  * [如何调试](extensibility/pipeline/how-to-debug.md)
  * [Node SDK](extensibility/pipeline/node-sdk.md)
  * [GraphQL API](extensibility/pipeline/graphql-api.md)
  * [常见问题](extensibility/pipeline/qa.md)
  * [私有化部署](extensibility/pipeline/private.md)
* [使用 Webhook](extensibility/use-webhook.md)
* [自定义用户字段](extensibility/user-metadata.md)

## 开发资源 <a id="sdk"></a>

* [开发资源](sdk/libraries.md)
* [API（GraphQL）](sdk/open-graphql.md)
* [Guard for Web](sdk/guard/README.md)
  * [自定义 Guard 的 UI 样式](sdk/guard/custom-guard-ui.md)
* [Guard for Mobile](sdk/guard-for-mobile/README.md)
  * [Guard for React Native](sdk/guard-for-mobile/guard-for-react-native.md)
* [Guard VS 自定义 UI](sdk/loginform-vs-custom-ui.md)
* [SDK for 单点登录](sdk/sdk-for-sso.md)
* [SDK for JavaScript](sdk/sdk-for-javascript/README.md)
  * [查询用户权限](sdk/sdk-for-javascript/query-permissions.md)
  * [绑定社会化登录](sdk/sdk-for-javascript/bind-social-accounts.md)
  * [自定义用户字段](sdk/sdk-for-javascript/metadata.md)
  * [管理 MFA 口令](sdk/sdk-for-javascript/guan-li-mfa-kou-ling.md)
  * [自定义请求链接](sdk/sdk-for-javascript/zi-ding-yi-qing-qiu-lian-jie.md)
* [SDK for Node.js](sdk/sdk-for-node/README.md)
  * [权限控制](sdk/sdk-for-node/rbac.md)
  * [查询用户权限](sdk/sdk-for-node/query-permissions.md)
  * [组织机构管理](sdk/sdk-for-node/org-structure.md)
  * [老版权限控制（deprecated）](sdk/sdk-for-node/update-user-permissions.md)
  * [Pipeline 函数](sdk/sdk-for-node/pipeline.md)
  * [自定义用户字段](sdk/sdk-for-node/user-metadata.md)
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

* [配置 MFA 安全口令](mfa/configure-mfa/README.md)
  * [使用 Microsoft Authenticator 添加 MFA 令牌](mfa/configure-mfa/use-microsoft-authenticator-to-add-mfa-token.md)
* [接入 MFA](mfa/integrate-mfa.md)

## 安全 <a id="security"></a>

* [配置 Web 安全域](security/cors.md)
* [配置用户池密码强度](security/config-user-pool-password-level.md)
* [配置密码加密函数](security/config-pwd-encrypt-function.md)

## 其他 <a id="others"></a>

* [常见问题](others/faq.md)
* [集成案例](others/cases/README.md)
  * [Authing 与 AWS 集成案例](others/cases/authing-aws.md)
  * [函数计算与 AaaS 集成案例](others/cases/faas-aaas.md)
  * [Odoo 集成案例](others/cases/odoo-ji-cheng-an-li.md)
  * [社会化登录集成案例](others/cases/she-hui-hua-deng-lu-ji-cheng-an-li.md)
* [社交互联数据](others/solid.md)
* [为 Authing 贡献 SDK](others/wei-authing-gong-xian-sdk/README.md)
  * [了解 Authing 的模块](others/wei-authing-gong-xian-sdk/le-jie-authing-de-mo-kuai.md)

