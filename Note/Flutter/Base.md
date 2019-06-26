# Flutter

## Widget
### StatelessWidget

### StatefulWidget

### State
- initState (当Widget第一次插入到Widget树时会被调用只调用一次啊)
- didUpdateWidget (在widget重新构建时)
- deactivate (当State对象从树中被移除时会调用)
- dispose (当State对象从树中被永久移除时调用)
- reassemble (开发调试时候使用，Release版本不会调用)
- didChangeDependencies (当State对象的依赖发生变化时会被调用)
### UI
#### Row
水平方向排列其子widget
#### Column
垂直方向排列其子widget
#### Flex
可以沿着水平或垂直方向排列子widget,Row和Column都是继承于它
#### Wrap
wrap解决了Row和Column显示的widge溢出屏幕报错的问题
#### Flow
#### Stack
#### Positioned
## OpenPackage
优秀的开源开发工具库
- [dio](https://github.com/flutterchina/dio)网络请求库

## 学习资料
- https://book.flutterchina.club
- https://flutterchina.club/