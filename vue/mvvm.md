# MVVM

`MVVM`是`Model-View-ViewModel`的缩写，即模型--视图--视图模型。

* 模型：指的是后端传递的数据
* 视图：指的是所看到的页面。
* 视图模型：`MVVM`模式的核心，连接`view`和`Model`的桥梁。它有两个方向，一个是将模型转化成视图，通过数据绑定的方式，将后端传递的数据转化成所看到的页面；一个是将视图转化成模型，通过`DOM`事件监听，将所看到的页面转化成后端的数据。这两个方向都实现的，我们称之为数据的双向绑定。

在`MVVM`的框架下视图和模型是不能直接通信的，他们通过`ViewModel`来通信，`ViewModel`通常要实现一个`observer`观察者，当数据发生变化，`ViewModel`能够监听到数据的这种变化，然后通知到对应的视图做自动更新，而当用户操作视图，`ViewModel`也能监听到视图的变化，然后通知数据做改动，这实际上就实现了数据的双向绑定。

`MVVM`流程图如下：

![](/assets/MVVM.png)
