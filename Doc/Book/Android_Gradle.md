# 《Android Gradle权威指南》
## 配置Gradle环境
## Gradle Wrappler
- 生成wrapper
```
gradle wrapper
```

- Wrapper配置
## Gradle日志
- 命令行
```
./gradlew tasks //查看Task
./gradlew --refresh-dependencies assemble //强制刷新第三方依赖库
./gradlew clean jar //多任务串行执行，先执行clean再执行jar
./gradlew connectCheck ./gradlew cc //任务缩写形式执行
```

# Groovy基础
基于JVM的动态语言，语法和Java相似
## 字符串
```
def str = "s"
println "${str}" // 输出： s
println '${str}' // 单引号不具备运算能力 输出：${str}
```
## 集合
- List
```
def num = [1,2,3,4,5]
```
- Map
```
def map = ['width':1024,'height':1024]
```
## 方法
- 写法更加灵活，以下两种写法都可行结果一样。
- return可以不写
- 代码块可作为参数传递
```
task invokedMethod{
    method(1,2)
    method 1,2
}

def method(int a,int b){
    println a + b
}
```
## JavaBean
类的属性可以直接访问
```
task HelloJavaBean{
    Person p = new Person()
    println "name is ${p.name}"
    p.name = "jack"
    println "name is ${p.name}"
}

class Person{
    private String name
}
```
## 闭包
- 传递参数
```
task helloClosure {
    customeEach{
        println it
    }
}
def customeEach(closure){
    for(int i in 1..10){
        closure(i)
    }
}
```
- 闭包委托
## DSL
领域特点语言
# Gradle构建脚本基础

## Settings文件
默认文件settings.gradle，初始化以及工程树配置
```
include ':module1'
include ':module2'
```
## Build文件
应用插件、依赖Maven中心库
```

```
## projects以及tasks

# Gradle任务
## 多种方式创建任务
## 多种方式访问任务
## 任务分组和描述
## <<操作符
## 任务的执行分析
## 任务排序
## 任务的启动和禁用
## 任务的onlyIf断言
## 任务规则
# Gradle插件
# Java Gradle插件
# Android Gradle插件
# 自定义Android Gradle工程
# Android Gradle高级自定义
- 自定义BuildConfig
  ```
  productFlavors{
      google{
          buildConfigField 'String','WEB_URL','"http://www.google.com"'
      }
      baidu{
           buildConfigField 'String','WEB_URL','"http://www.baidu.com"'
      }

  }

  ```
- 动态添加自定义资源
  ```
  productFlavors{
      google{
          resValue 'String','channel_tip','google'
      }
      baidu{
           resValue 'String','channel_tip','baidu'
      }

  }
  ```
- Java编译选项
  

