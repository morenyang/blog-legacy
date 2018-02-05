---
title: 使用Element UI的上传组件将图片上传至七牛云
date: 2017-05-15
tags:
---

首先说一句，七牛云提供个官方Javascript客户端SDK真的是让我看得一头雾水。没办法，在网上找了别人的文章，又去比较了一下官方Demo的请求和Element组件上传时的请求，才把上传给琢磨清除。

### 开始之前

- 先去注册一个七牛云的账号
- 创建一个对象存储的空间，并把空间名字记下来
- 在对象存储的空间，把右边的测试域名记下来
- 在七牛个人中心 - 秘钥管理那里把你的AK和SK记下来
- 去找[文档](https://developer.qiniu.com/kodo/manual/1671/region-endpoint)，把七牛提供的存储空间对应区域的上传域名记下来

可以通过一幅图来帮助我们了解上传的整个数据流

![](https://odum9helk.qnssl.com/Fmy1Y_s9I4oCPYuMGDrvYxCRv2FM)

## 服务端配置
我们用的是Java写的后端，嗯我知道你现在想要文档了，[给你](https://developer.qiniu.com/kodo/sdk/1239/java)。

其实也没多难，API文档上面写得很清楚，复制粘贴就可以了。首先我们用Maven把依赖装好，然后新建一个FileUploadController，把改写的写上去即可。

这里我们的上传策略是，不从前端获取key，文件名和路径前缀全部由后台来决定。参考代码：

```java
// AK和SK
private static final String AccessKey = "HelloWorld";
private static final String SecretKey = "HelloWorld";
// 存储空间的名字
private static final String Bucket = "BucketName";
// 存储空间的访问域名
private static final String BucketDomain = "//bucketName.bkt.clouddn.com";
// 七牛提供的上传域名
private static final String UploadAPI = "//upload-domain.qiniu.com";
@GetMapping("/getUpToken")
public Map<String, Object> getToken() throws Exception {
    Map<String, Object> map = new HashMap<>();
    boolean status = false;
    try {
        Auth auth = Auth.create(AccessKey, SecretKey);
        
        // 定义token过期时间
        long expireSeconds = 3600;
        
        // 定义上传策略
        StringMap policy = new StringMap();
        policy.put("saveKey", "img/$(etag)$(ext)");
        
        String upToken = auth.uploadToken(Bucket, null, expireSeconds, policy);
        map.put("uptoken", upToken);
        map.put("bucketDomain", BucketDomain);
        map.put("uploadAPI", UploadAPI);
        status = true;
    } catch (Exception e) {
        e.printStackTrace();
    }
    map.put(APP_STATUS, status);
    return map;
}
```

## 前端代码

这里我们把上传的域名、存储空间的域名等所有参数都由后台提供，所以el-upload中的参数就全部绑定在data中。

### template

```html
<el-upload
    class="upload-demo"
    drag
    :action="uploadAPI"
    :before-upload="handleBeforeUpload"
    :data="form"
    :on-success="handleUploadSuccess"
    :multiple="false">
    <i class="el-icon-upload"></i>
    <div class="el-upload__text">将文件拖到此处，或<em>点击上传</em></div>
    <div class="el-upload__tip" slot="tip">只能上传jpg/png文件，且不超过500kb</div>
</el-upload>
```

### script
这里要注意的是，我们获取token的方法，返回的是Promise形式。

```js
export default{
    name: 'Upload',
    data () {
      return {
        uploadAPI: '', // 上传的域名
        bucketDomain: '', // 存储空间的访问域名
        form: {} // 上传时要传给七牛服务器的参数
      }
    },
    methods: {
      // 上传前执行的方法，注意这里返回的是Promise
      // 你也可以在这里通过file的值自定义一个key
      handleBeforeUpload(file){
        return getUploadToken()
          .then(res => {
            if (res.status) {
              this.uploadAPI = res.uploadAPI;
              this.bucketDomain = res.bucketDomain;
              this.form = {
                token: res.uptoken
              }
            } else {
              return Promise.reject();
            }
          })
      },
      handleUploadSuccess(res){
        let url = `${this.bucketDomain}/${res.key}`;
        console.log(url);
      }
    }
}
```

其实也并不是很难。但是要注意的是，如果你想在`before-upload`这个方法中通过对文件MD5
生成一个`key`值，是不能直接 `let key = md5(file)`的，具体是为什么我也不知道，如果有大神知道的话可以告诉我。

最后，给你们推荐一个第三方的SDK，名字叫做 [qiniu4js](https://github.com/lsxiao/qiniu4js)，在readme中提供的文档还是比较够用的。

