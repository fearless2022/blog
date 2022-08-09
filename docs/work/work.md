## 记录微信小程序登录跳转第三方 oauth2 H5 授权后，再跳转第三方小程序问题

### 解决方式

* 微信小程序登录时，请求服务端返回第三方 oauth2 H5 授权网页链接。（小程序端可以直接调用，主要想做到可配置）
* 在第三方完成授权后，跳转到第三方小程序
* 配置oauth2重定向地址为服务端 GET 请求地址，该请求会调用微信接口获取 [URL scheme](https://developers.weixin.qq.com/miniprogram/dev/framework/open-ability/url-scheme.html)，然后服务端再重定向到 URL scheme 链接，在微信体系内会直接拉起小程序
* 如果使用 [URL Link](https://developers.weixin.qq.com/miniprogram/dev/framework/open-ability/url-link.html) 会出现[中间页 H5 没有打开小程序按钮的问题](https://developers.weixin.qq.com/community/enterprisewechat/doc/000eec06f9cdc07ebb8d0ef185bc00)