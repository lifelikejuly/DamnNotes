# Overview
在codelab你可以学习如何测试Android应用。Android Studio集成了单元测试、密闭测试、功能UI测试。最后我们会拓展关于Espresso测试框架更丰富的内容。
你需要学习了解的内容
1. MVP模式的轻量级应用构建设计
2. 使用Gradle和Android Studio进行测试
3. 使用JUnit4编写Android单元测试
4. 使用Espresso和Android测试支持库编写UI测试
5. 一些Espresso高级  topics (Espresso-contrib, Espresso-intents and IdlingResources)
6. 生成编码Coverage报告
你需要准备的内容
1. Android Studio version 2.1+
2. 代码用例
3. 最低Android 2.3.3 (API level 10)或更高版本的测试设备或者仿真机
4. 设备的USB数据线

# Get the sample code
你可以以zip格式或直接从github下载例子代码到你的电脑。
我们已经为你创建了每个步骤的快照。If you get stuck you can just jump ahead to the next checkpoint and continue where you left off.
每个步骤是互相关联的。首先从‘step-1-5'开始然后可以通过检查点进行你想要的下一节。
>Zip文件包含几个文件夹和文件：
* step-1-5—The starting code that you'll build upon in this codelab.
* step-6—The starting code for section 6: "Unit Testing with JUnit4 and Mockito"
* step-7—The starting code for section 7: "Writing UI tests with Espresso"
* step-8—The starting code for section 8: "Espresso-Intents"
* step-9—The starting code for section 9: "Espresso-Contrib"
* final—The complete code for the finished codelab.

你同样可以直接从github上载下每个快照，我们创建在每个分支上。
第一个快照在分支‘step-1-5':
>$ git clone https://github.com/googlecodelabs/android-testing -b step-1-5

We will be getting back to the code in the next step, where we will import the project into Android Studio and run the app for the first time. For now, just download the sample code and save it to your computer.
>If you run the app, you will notice that we are getting a blank screen with no notes or working buttons! We'll implement the logic later.

# Notes - a simple note taking app
在codelab，“Notes”是一个简单的做笔记App。别担心，我们还准备了更多用例代码。
你可以查看笔记，打开笔记，添加新笔记或添加照片。在接下去的几个步骤，we will hook up the final parts of the app，探究MVP构建和更多测试等重要内容。

# Introduction to Testing on Android
运行在设备上的应用程序会因为屏幕尺寸和分辨率，处理能力，存储器或连接性上存在很大差异。自动化测试可以弥补手动测试在不同配置的设备和其他因素所不能完成的（Manually testing your application across all these different device configurations and external factors can be sheer impossible - that's where automated testing can help.）[Android Testing Support library](https://google.github.io/android-testing-support-library/)提供很好的框架测试Android应用。通过Espresso和UI Automator实现JUnit 4-compatible test runner (AndroidJUnitRunner) 和 functional UI testing。你可以通过AS IDE直接调用测试APIs运行测试，集成到你的开发流程中。
我们接着会学习更多关于测试详情，探究如何使用工具编写你的测试代码。
# Model-View-Presenter (MVP) Architecture
# MVP in practice
# Unit Testing with JUnit4 and Mockito
在这节继续使用当前Android Studio项目。
>如果你是从本节开始的，继续使用快照‘step-6':
从Zip文件中打开名为Step-6文件导入到Android Studio或从github分支Step-6上下载：
git clone https://github.com/googlecodelabs/android-testing.git -b step-6

本节我们配置项目测试的设置，在Android Studio创建并运行我们的测试以及探究如何使用单元测试在组件之间建立关系。

我们采用测试驱动的方法，意味着我们需要在实现功能之前开始写描述功能的测试用例。测试帮助我们为presenter定义在上一节中介绍的联系。


原文地址[https://codelabs.developers.google.com/codelabs/android-testing/index.html#0](https://codelabs.developers.google.com/codelabs/android-testing/index.html#0)

参考：[https://google.github.io/android-testing-support-library/docs/index.html](https://google.github.io/android-testing-support-library/docs/index.html)
