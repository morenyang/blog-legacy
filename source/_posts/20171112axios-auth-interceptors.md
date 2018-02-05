---
title: Axios权限拦截
date: 2017-11-12
tags:
---

在使用流行的前端框架时，前后台的数据交互常使用token或其他一些自定义headers来标识用户身份，因此在发送请求的过程中，我们就需要用到一些拦截器来帮助我们完成一些工作，例如添加自定义的headers、取消请求等。

### 添加自定义headers

这个操作我们在拦截器中进行就可以了

```js
// 先预定义好一个axios实例r
const r = axios.create({
    // your config
});
// 给r配置拦截器
r.interceptors.request.use(
  config => {
    // 自定义一些配置
    config.headers['x-user-flag'] = 'your-flag'
    // 从store中获取数据
    const authState = store.getState().auth;
    if (!!authState.loginState) {
      config.headers['openid'] = authState.openid;
    } 
    return config;
  },
  error => Promise.reject(error)
);
```

### 判断没有权限时拦截请求

这个操作同样要在拦截器中定义，但是我们要先创建一个CancelToken
```js
const CancelToken = axios.CancelToken;
const source = CancelToken.source();
r.interceptors.request.use(
  config => {
    const authState = store.getState().auth;
    if (!!authState.loginState) {
      config.headers['openid'] = authState.openid;
    } else {
      source.cancel('No Permission');
      // some other actions
    }
    return config;
  },
  error => Promise.reject(error)
);
```

### 拦截响应

拦截响应和上述方法类似，新建一个response的拦截器即可

```js
r.interceptors.use(
  res => {
    // some actions
    return res;
  },
  error => Promise.reject(error)
);
```

#### 参考文档

[https://github.com/axios/axios#interceptors](https://github.com/axios/axios#interceptors)
[https://github.com/axios/axios#cancellation](https://github.com/axios/axios#cancellation)