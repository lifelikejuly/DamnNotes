# 包冲突
项目原本使用25.3.0版本，本来没有使用26.1.0的support-v4库还找不到哪里依赖的，导致依赖库冲突，AnitorCompatHelper无法使用。最后才找到，原来com.google.android.gms中有调用support的依赖库。在gradle中使用如下方式删除指定依赖排除不需要的库。
```
api ('com.google.android.gms:play-services-base:15.0.0'){
        exclude group: 'com.android.support'
    }
```
# 补充
由于主项目support库全部升级到27.1.0，使用以上方法不起作用。原因其实就是26+版本以上AnimatorCompatHelper类被移除，所以clearInterpolator方法找不到。最终替换使用新的方法。
```
TimeInterpolator mDefaultInterpolator = new ValueAnimator().getInterpolator();
view.animate().setInterpolator(mDefaultInterpolator);
```
> 印象中Android SDK升级一般不会直接移除旧方法而使用@Deprecated建议开发者不要调用。这次算是了解到过时API还是会到被弃用的可能，所以开发中升级SDK需要谨慎及时替换被Deprecated掉的API。
# 参考
* https://blog.csdn.net/yuzhiqiang_1993/article/details/78214812
* https://blog.csdn.net/xx326664162/article/details/71488551