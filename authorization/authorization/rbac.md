# 什么是基于角色的管理\(RBAC\)

## 什么是 RBAC

基于角色的访问控制（Role-based access control，简称 RBAC），指的是通过用户的角色（Role）赋予其相关权限，这实现了细粒度的访问控制，并提供了一个相比直接授予单个用户权限，更简单、可控的管理方式。

  
当使用 RBAC 时，通过分析系统用户的实际情况，基于共同的职责和需求，将他们分配给不同的角色。然后可以授予每个用户一个或多个角色，每个角色具有一个或多个权限，这种 **用户-角色**、**角色-权限** 间的关系，让我们可以不用再单独管理单个用户，用户从具备的角色里面继承所需的权限，从而使得用户赋权这件事变得更加简单。

  
举一个公司内所有在职员工具备登录公司邮箱的权限的场景，如果应用 RBAC，就可以赋予所有在职员工 ​employee​ 角色，​employee​ 角色具备 ​email:login​ 权限，如此所有员工就具备了登录公司邮箱的权限。如果有员工离职，只需要将其移出 ​employee​ 角色，而不需单独收回权限。本质上，一个角色（Role）就是一组权限（Permission）的集合。使用角色添加、删除、调整权限，相比单独赋予单个用户权限更加简单。当你的用户基数不断增长时，角色会变得尤为有用。

在规划访问控制策略时，最佳实践是给予用户完成工作必须的最小权限。

## 使用 RBAC 的优势

* 系统性、可重复性的权限指派
* 更方便得用户权限审计，快速定位问题
* 快速地添加、修改角色，甚至可以调用 API 实现
* 减少授予用户权限时发生错误的可能性
* 引入 第三方用户/新用户/未登录用户 时，赋予他们预先配置好的角色，比如 ​guest​

## 分组

除了直接赋予用户某个角色，作为 RBAC 的最佳实践，我们还可以通过分组管理用户，将一个分组和一组角色绑定，在此分组内的所有用户就会继承这些角色，并自动具备了这些角色包含的权限。这些概念之间的关系为：Permission &lt;-&gt; Roles &lt;-&gt; Groups &lt;-&gt; Users如下图所示：

* 分组：Employee, Contractor
* 角色：Vacation Requester, Invoice Submitter, Express Submitter
* 权限：Read vacation requests, Create vacation requests 等

用分组管理用户、分组包含一组权限，这也是我们推荐使用的方式。

![](../../.gitbook/assets/image%20%28386%29.png)

> 注：如果一个用户具备的多个角色中权限有重叠，他最终的权限列表将会是这些角色权限的并集。

### 分组 vs 权限

{% hint style="info" %}
分组\(Group\)和角色\(Role\)有什么区别？

* 角色是一组权限的集合。
* 分组侧重于管理用户，一个分组通常拥有多个角色，分组内的用户会继承分组内所有角色的所有权限。
{% endhint %}

{% hint style="info" %}
常见的 Group 和 Role 示例：

* Group
  * admin: 系统管理员，通常包含系统维护者。
  * employee: 正式雇员。
  * employer: 面试官。
  * hr
  * intern: 实习生
  * ops\_engineer: 运维工程师
* Role
  * Invoice Submitter: 具备发票报销的相关权限。
  * Vacation Requester: 具备申请假期的相关权限。
  * Corporation Email User: 具备使用公司邮箱的的相关权限。
  * Production Server Operator: 具备线上服务器的操作权限。
  * HR App User: 具备使用 HR 系统的相关权限。

举例来说：可以这样建立 Role 和 Group 之间的关系：

* intern  具备 Corporation Email User 这个角色，但是不具备 Vacation Requester 和 Invoice Submitter 这两个角色。
* employee  拥有发票报销和申请假期角色，但是不具备线上服务器的操作权限。
* ops\_engineer 拥有发票报销、申请假期、线上服务器的角色。
{% endhint %}

{% hint style="success" %}
推荐使用 Group organize 用户，用 Role organize 权限，不要直接赋予单个用户某个权限。如果是某个用户临时需要具备某个角色，可以临时授予，结束之后再收回。
{% endhint %}

如果你还是对 group 和 role 之间的区别不是很清晰，推荐几个资源：

* [https://stackoverflow.com/questions/7770728/group-vs-role-any-real-difference](https://stackoverflow.com/questions/7770728/group-vs-role-any-real-difference)



