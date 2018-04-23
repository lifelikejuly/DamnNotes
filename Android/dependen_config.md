# 包冲突
项目原本使用25.3.0版本，本来没有使用26.1.0的support-v4库还找不到哪里依赖的，导致依赖库冲突，AnitorCompatHelper无法使用。最后才找到，原来com.google.android.gms中有调用support的依赖库。在gradle中使用如下方式删除指定依赖排除不需要的库。
```
api ('com.google.android.gms:play-services-base:15.0.0'){
        exclude group: 'com.android.support'
    }
```

# 参考
* https://blog.csdn.net/yuzhiqiang_1993/article/details/78214812