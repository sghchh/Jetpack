## Jetpack Lifecycle

Lifecycle是Jetpack中负责管理的生命周期的一个组件，其通过三个关键的类协同进行工作：

* **Lifecycle**：该类用于存储有关组件（Activity或者Fragment）的生命周期状态的信息，并允许其他对象观察此状态
* **LifecycleOwner**：该类代表Lifecycle的持有者，其提供一个重要方法**getLifecycle()**来返回其所持有的Lifecycle
* **LifecycleObserver**：从名字就可以知道，该类是Lifecycle的观察者，观察者中定义Lifecycle**各个生命周期节点的响应动作**；也可以通过查询当前Lifecycle的状态，编写在状态满足一定条件的情况下才会有意义的方法

所以，Lifecycle的简单使用步骤为：

1. 自定义一个类，继承**LifecycleObserver**
   * 其可以通过为方法增加**@OnLifecycleEvent(Lifecycle.Event.xxx)**注释的形式，来定义指定生命周期时刻的响应动作
   * 可以在普通的方法中**通过lifecycle.currentState.xxx**方法查询当前Lifecycle的状态，根据当前状态实现不同的code逻辑
2. 在Activity或者Fragment中调用**getLifecycle().addObserver()**来讲上面自定义的LifeObserver对象传递过来