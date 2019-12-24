# 迁移老数据进 Authing

## 第一步：修改用户池配置

开始导入用户前，请进入**基础配置** -&gt; **安全设置**，关闭「**频繁注册限制**」、「**登录失败次数限制**」，导入完毕后再开启。如果导入的用户邮箱已经验证过，建议开启「**邮箱无需经过验证**」。

## 第二步：编写密码函数计算

如果你的用户数据表中密码字段是明文，可以跳过此步骤；如果是密文，需要进入**基础配置** -&gt; **密码管理** -&gt; **自定义密码加密方法**开启选项并编写用于加密和验证密码的函数。

## 第三步：导出你的用户数据

请将你的用户数据导出为 JSON 格式，内容为一个数组，每个元素是一个对象，其中一个元素对应一条用户的信息，例如：

```text
[
  {
    "uid": "1",
    "nickname": "zhang",
    "account_id": "zhang",
    "mail": "",
    "password": "$2b$12$nCa3WDbsc3tvM57ifzjwrOAGGuNK7EPV0R17WKcW6f13NZvX97yLe",
    "phone": "13512341234"
  },
  {
    "uid": "2",
    "nickname": "wang",
    "account_id": "wang",
    "mail": "",
    "password": "$2b$12$HGloOlfz1HzD0v/r5m1r7OCMcx6X85eC5.At3Ckxe.Jn/u/Za/yy2",
    "phone": "13712341234"
  },
  {
    "uid": "3",
    "nickname": "zhao",
    "account_id": "zhao",
    "mail": "",
    "password": "$2b$12$ia1oUZZFbEUpLvuqUsKideQq9lVkf2kq9vFaTvp7dlfeCx8UlTmDu",
    "phone": "13512341234"
  }
]
```

## 第四步：导入用户数据到 Authing

如果你没有 NodeJS 环境，需要先[安装 NodeJS](http://nodejs.cn/download/)。

创建一个 index.js 文件。

将以下 js 脚本粘贴到 index.js：

```text
const fs = require("fs");
const path = require("path");

const Authing = require("authing-js-sdk");
const secret = "xxxxxxxxxxxxxxxxxxx";
const clientId = "xxxxxxxxxxxxxxxxxxx";

// 如果文件较大建议分批次读入
// 请将用户信息与本文件保存在同一个目录，文件内容为用户数据的数组 JSON，一个元素为一个用户的信息对象
let users = fs.readFileSync(path.resolve('users.json'), { encoding: "utf8" });

async function main() {
  const authing = await new Authing({
    clientId,
    secret
  });
  for (let i = 0; i < users.length; i++) {
    try {
      let yourUser = users[i];
      // 在此完成字段对齐
      let registerResult = await authing.register({
        /**
         * 开启这个开关，password 字段会直接写入 Authing 数据库，Authing 不会再次加密此字段
         * 如果你的密码不是明文存储，你应该保持开启，并编写密码函数计算
         */
        keepPassword: true,
        username: yourUser.username,
        nickname: yourUser.nickname,
        password: yourUser.password,
        email: yourUser.email,
        photo: yourUser.picture,
        phone: yourUser.telephone,
        // 微信、github、qq 等社会化登录时提供的第三方 unionid
        unionid: yourUser.githubId,
        // 存储原始数据，以备使用
        oauth: JSON.stringify(yourUser),
        // 如果有微信、微博等注册方式应该也要填一下，这里对具体业务绑定性挺强的
        // registerMethod 可选值为 default:username-password social:qq social:dingtalk social:weibo oauth:github oauth:wechat oauth:wxapp
        registerMethod: user.githubId
          ? "oauth:github"
          : "default:username-password"
      });
      if (!registerResult._id) {
        throw Error("注册失败");
      }
    } catch (err) {
      console.log(err);
      // 将导入失败的用户写入文件
      fs.writeFileSync(path.resolve('users_failed.json'), JSON.stringify(yourUser) + "\n", {
        flag: "a"
      });
    }
  }
}

main();

```

复制完成后请**对字段进行对齐**，再执行

```text
$ npm install authing-js-sdk
$ node index.js
```

代码可在 Github 查看：[ Star](https://github.com/Authing/users-migration)

遇到问题？[联系我们](https://gitter.im/authing-chat/community)，Feel free to talk.

