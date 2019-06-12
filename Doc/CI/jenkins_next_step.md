# Jenkins Next Step
之前已经部署过jenkins以及它的简单构建项目流程，接下来学着用用jenkins支持的更高级更高深的功能。
## 触发远程构建
设置token执行令牌获取构建权限，可以随便设置token当然为了安全起见在正式环境建议使用较为复杂的token。
JENKINS_URL/job/koperasibms/build?token=TOKEN_NAME 或者 /buildWithParameters?token=TOKEN_NAME


## Rest Api
- 获取job列表   
http://localhost:8080/api/json?pretty=true
- 获取job详情  
http://localhost:8080/job/koperasibms/api/json?pretty=true
- 获取到job当前构建状态
http://localhost:8080/job/koperasibms/lastBuild/api/json?pretty=true
```
....
"building" : false,
  "description" : null,
  "displayName" : "#8",
  "duration" : 29785,
  "estimatedDuration" : 178141,
  "executor" : null,
  "fullDisplayName" : "koperasibms #8",
  "id" : "8",
  "keepLog" : false,
  "number" : 8,
  "queueId" : 10,
  "result" : "ABORTED",
  "timestamp" : 1560337714775,
  "url" : "http://localhost:8080/job/koperasibms/8/",
  ....
```