# 用户登录凭证（code）最佳实践

1. app 初次启动的时候判断登录态，如未登录，调用 `wx.login()` 获取 `code` 并存入 `stroage`。

```javascript
// app.js
onLaunch: function() {
  console.log('App Launch')

  // 最佳实践，初次 launch 的时候获取 code 并保存到 localStorage 中
  wx.checkSession({
      success(res) {
          console.log(res)
      },
      fail() {
          wx.login({
              success(res) {
                  const code = res.code;
                  wx.setStorageSync("code", code)
              }
          })
      }
  })
},
```

2. 每次页面 `onLoad` 时判断登录态，如果登录失效，重新登录获取 `code` 并替换原来存在 `stroage` 中的 `code`：

```javascript
onLoad: function() {
    const self = this
    wx.checkSession({
        // 若丢失了登录态，通过 wx.login 重新获取
        fail() {
            wx.login({
                succes(res) {
                    const code = res.code;
                    wx.setStorageSync("code", code)
                }
            })
        }
    })
},
```

3. 之后调用 Authing SDK 微信相关接口的时候，使用下面的方法获取 `token`，这样就能确保此 `token` 是最新的。

```javascript
const code = wx.getStorageSync("code")
```

