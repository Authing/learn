---
description: Authing 支持使用 LDAP 协议查看、修改、增加和删除用户信息。
---

# 使用 Authing 的 LDAP 用户目录

Authing 支持使用 LDAP 协议查看、修改、增加和删除用户信息，本页面包含了一些基础信息和使用教程。

## 基本信息

| 信息 | 值 |
| :--- | :--- |


| Hostname | ldap://ldap.authing.cn |
| :--- | :--- |
| Port | 1389 |
| LDAP Distinguished Name | cn=AUTHING\_USERNAME, ou=users, o=AUTHING\_CLINET\_ID, dc=authing, dc=cn |
| Base DN | ou=users, o=AUTHING\_CLINET\_ID, dc=authing, dc=cn |

## 认证方式

访问 Authing LDAP 服务器需要使用 Authing 的应用[密钥 \(Secret\)](http://localhost:8081/dashboard/clients/5cc2a350e056c76eea71db8a/settings)，认证命令如下所示：

```text
$ ldapsearch -H ldap://ldap.authing.cn:1389 -x -D "ou=users,o=AUTHING_CLIENT_ID,dc=authing,dc=cn" -w "AUTHING_CLIEENT_SECRET"  -LLL -b "ou=users,o=AUTHING_CLIENT_ID,dc=authing,dc=cn"
```

若密钥 \(Secret\) 不正确会返回如下信息：

```text
ldap_bind: Invalid credentials (49)	matched DN: ou=users, o=AUTHING_CLIENT_ID, dc=authing, dc=cn	additional info: InvalidCredentialsError
```

## Search

```text
$ ldapsearch -H ldap://ldap.authing.cn:1389 -x -D "ou=users,o=AUTHING_CLIENT_ID,dc=authing,dc=cn" -w "AUTHING_CLIEENT_SECRET"  -LLL -b "ou=users,o=AUTHING_CLIENT_ID,dc=authing,dc=cn"
```

## Add

创建一个名为 user.ldif 的文件然后复制以下内容进去：

```text
dn: cn=authingUserName, ou=users, o=AUTHING_CLIENT_ID, dc=authing, dc=cnobjectClass: userscn: authingUserName
```

然后执行以下命令：

```text
$ ldapadd -H ldap://ldap.authing.cn:1389 -x -D "ou=users,o=AUTHING_CLIENT_ID,dc=authing,dc=cn" -w "AUTHING_CLIEENT_SECRET" -f ./user.ldif
```

## Modify

创建一个名为 modify.ldif 的文件然后复制以下内容进去：

```text
dn: cn=secret, ou=users, o=AUTHING_CLIENT_ID, dc=authing, dc=cnchangetype: modifyreplace: mailmail: test@authing.cn
```

然后执行以下命令：

```text
$ ldapmodify -H ldap://ldap.authing.cn:1389 -x -D "ou=users,o=AUTHING_CLIENT_ID,dc=authing,dc=cn" -w "AUTHING_CLIEENT_SECRET" -f ./modify.ldif
```

## Delete

```text
$ ldapdelete -H ldap://ldap.authing.cn:1389 -x -D "ou=users,o=AUTHING_CLIENT_ID,dc=authing,dc=cn" -w "AUTHING_CLIEENT_SECRET" "cn=authingUserName, ou=users, o=AUTHING_CLIENT_ID, dc=authing,dc=cn"
```

