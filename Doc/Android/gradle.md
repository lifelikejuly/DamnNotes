
查看依赖冲突
```
configurations.all {
  resolutionStrategy {
    failOnVersionConflict()
  }
}
```

类冲突
```
AGPBI: {"kind":"error","text":"Program type already present: com.orhanobut.logger.AndroidLogAdapter","sources":[{}],"tool":"D8"}
```