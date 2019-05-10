## 单例模式

```
public class Singleton{
    private Singleton(){}
    public static final Singleton getInstance(){
        return SingletonHolder.INSTANCE;
    }
    private static class SingletonHolder{
        private static final Singleton INSTANCE = new Singleton();
    }
}
```
## Builder模式

## 原型模式
拷贝clone是原型模式的一种形式

## 工厂方法模式
Collection：List、Set
## 抽象工厂模式

## 享元模式

## 责任链模式
事件分发机制

## 观察者模式
notifyDataSetChanged()


## 参考
* https://www.runoob.com/design-pattern/design-pattern-tutorial.html