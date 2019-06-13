# 配置用户权限

Authing 控制台中支持快速配置用户权限，操作流程如下。

若想使用 API 操作用户权限，请点击[这里](/user_service/get_user_role)查看。

## 创建角色

配置用户角色需要先创建用户组，操作流程如下：

首先进入到任意应用，依次点击 **用户中心** -&gt; **用户角色** -&gt; **创建角色**。

![&#x521B;&#x5EFA;&#x89D2;&#x8272;](../.gitbook/assets/image%20%28153%29.png)

然后输入**角色名称**后点击**创建**。

![](../.gitbook/assets/image%20%2823%29.png)

## 添加权限

创建完角色后可以为角色增加不同的权限，点击列表中最左边的**详情**按钮。

![](../.gitbook/assets/image%20%28163%29.png)

在弹出的窗口中可以设置用户权限，如下图所示：

![](../.gitbook/assets/image%20%2898%29.png)

权限这里可以任意字符串或 JSON，这块由开发者定义，之后可以使用 [API](/user_service/get_user_role.md) 在程序中根据用户 ID 读取此处权限，并完成业务逻辑。

## 指派成员

创建了用户组后可以为角色设置成员。

![](../.gitbook/assets/image%20%2818%29.png)

在弹出的窗口的搜索框中根据**用户名**搜索用户，然后回车即可确认指派。

![](../.gitbook/assets/image%20%2854%29.png)

指派后，点击列表右侧的按钮可以移除用户。

![](../.gitbook/assets/image%20%28156%29.png)

## 接下来你可能还需要

{% page-ref page="../sdk/authing-sdk-for-web/web-sdk-for-roles.md" %}

