# Jetpack Navigation

使用Navigation组件的步骤为：

1. **定义navigation资源文件**：这是一种新的xml资源文件，专门为Navigation组件提供，在该资源文件中定义不同的Fragment之间跳转关系。而该资源文件中可以为各个fragment提供重要的xml标签有：
   * **action标签**：该标签用来定义一个Fragment可以具有的**跳转动作**，该标签的属性标识并描述该动作
     * id：该属性表示特定的Action，使用NavigationController进行路由导航的时候需要通过id找到对应的Action
     * destination/popUpTo：这两个属性互斥，分别表示该Action是跳转到下一个Fragment，还是回退到之前某一个Fragment，他们的值都是一个指示Fragment的id
   * **argument标签**：该标签用来定义跳转到一个fragment的时候传递的数据，该数据可以在fragment中通过bundle获得
     * name：数据的名字
     * argType：用来指定数据的类型
     * defaultValue：用来为数据初始化一个默认值
2. 在Activity的layout布局中添加fragment标签，对于这里的fragment标签的一些属性有一些特殊的要求
   * name属性**必须是androidx.navigation.fragment.NavHostFragment**
   * **navGraph**：这个属性是一个新的属性，该属性的值**就是上面定义的navigation文件**，采用@navigation/xxx的形式指明
   * defaultNavHost：该属性也是一个新的属性，一般设置为true，表示由Navigation拦截用户点击返回键
3. 在展示的自定义的Fragment中为特定的View添加点击事件实现Fragment之间的跳转，由不同的方式可以实现跳转
   1. 调用**Navigation.findNavController().navigate(fragmentId)**实现跳转到navigation资源文件中添加过的fragment中。这种方式的跳转，需要通过fragmentId动态地指定跳转到的fragment，是一种**动态形式**的跳转。
   2. 调用**Navigation.createNavigationOnClickListener(actionId)**实现，这种方式的跳转完全由**navigation资源文件**中的Action标签决定，这种方式的跳转是一种**静态形式**的跳转，因为其目标已经在xml文件中写死了。

#### 静态or动态跳转

关于是使用Action标签来定义静态的跳转规则，还是使用动态的NavigationController控制跳转，仁者见仁智者见智，不过还是推荐使用静态的跳转方式，因为Fragment之间的跳转逻辑一般来说不会发生改变，没有必要使用动态方式。

而且，Action标签下还可以定义enterAnim、exitAnim等属性来指定进入和出去的动画效果，而对于进场和退出的动画，更加是固定不变的，也不推荐使用kt或者java代码的形式添加。

因此，还是推荐使用action标签来指定静态的跳转规则。



