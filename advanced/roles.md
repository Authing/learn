# 配置用户权限

Authing 控制台中支持快速配置用户权限，操作流程如下。

若想使用 API 操作用户权限，请点击[这里](../sdk/sdk-for-node/update-user-permissions.md#huo-qu-yong-hu-quan-xian-he-jiao-se)查看。

## 创建角色

配置用户角色需要先创建用户组，操作流程如下：

首先进入到任意应用，依次点击 **用户中心 -&gt; 权限控制 -&gt;  权限控制 -&gt; 创建角色。**

![](../.gitbook/assets/image%20%28280%29.png)

然后输入**角色名称**后点击**创建**。

![](../.gitbook/assets/image%20%28458%29.png)

## 创建权限

创建完角色后，可以增加不同的权限，点击列表中最左边的「权限管理」

![](../.gitbook/assets/image%20%28259%29.png)

在弹出的窗口中可以设置用户权限，如下图所示：

![](../.gitbook/assets/image%20%28441%29.png)

权限这里可以任意字符串或 JSON，这块由开发者定义，之后可以使用 [API](../sdk/sdk-for-node/update-user-permissions.md#huo-qu-yong-hu-quan-xian-he-jiao-se) 在程序中根据用户 ID 读取此处权限，并完成业务逻辑。



## 添加权限到用户角色

在创建完权限以后需要把权限添加到用户角色。点击「角色管理」点击**角色名称**，即可对角色进行管理。

![](../.gitbook/assets/image%20%2857%29.png)

 选择「配置权限」-&gt; 「添加权限」

![](../.gitbook/assets/image%20%2816%29.png)

在弹出的选框中将名字前面的选择框中打勾，然后选择决定，即可将权限添加至角色。

![](../.gitbook/assets/image%20%28437%29.png)

## 指派成员

创建了用户组后可以为角色设置成员。

![](../.gitbook/assets/image%20%28209%29.png)

在弹出的窗口的搜索框中根据**用户名**搜索用户，然后回车即可确认指派。

![](../.gitbook/assets/image%20%28337%29.png)

指派后，在下面选择用户即可完成删除。

![](../.gitbook/assets/image%20%28167%29.png)

## 接下来你可能还需要

{% page-ref page="../sdk/sdk-for-node/update-user-permissions.md" %}

