# 自定义请求连接

`Authing` 构造函数包含一个名为 `host` 对象，可接收自定义的请求链接（适合私有部署 Authing 的用户使用），使用方法如下：

```javascript
const auth = new Authing({
	userPoolId: 'your_userpool_id',
	secret: 'your_userpool_secret'
	host: {
		user: 'https://users.authing.cn/graphql',
		oauth: 'https://oauth.authing.cn/graphql'
	}
});
```

`host` 对象中 `user` 和 `oauth` 的默认值是 Authing 官方的请求链接：

* [https://users.authing.cn/graphql](https://users.authing.cn/graphql)
* [https://oauth.authing.cn/graphql](https://oauth.authing.cn/graphql)

