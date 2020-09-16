# Jetpack Starup

在Android开发的过程中，经常会使用到很多第三方的组件、库等工具，而一些第三方库或者组件在使用的时候，可能会需要执行一些初始化的操作，我们在处理这种情况时，往往是自定义一个Application类，在该类的onCreate方法中去做一些初始化的工作。Jetpack为我们提供了一个Starup组件来处理这种业务，简单使用Starup组件的步骤如下：

1. 定义一个类，该类需要继承**Initializer<T**，关于该类，其需要重写父类的两个方法
   * **create(Context)**方法：我们需要**在该方法中实现我们对于某一个第三方组件或者库的初始化工作**，同时，该方法必须返回一个T类型的对象，一般情况下，该对象是这个第三方组件或者类的对象
   * **dependcies()**方法：在一些第三方库或者组件之间存在依赖关系，可能需要在此之前先初始化另一个第三方组件或者库，而该方法返回一个列表，该列表元素为**所依赖的类对应的Initializer类的class对象**；这样，就**会自动执行对应的create方法中的初始化代码**
2. 在mainfest清单文件中定义一个**InitializationProvider**，通过该provider我们可以实现类似于activity那样，指定一个启动provider这样才能在App启动的时候执行该Initializer中的初始化代码
   * **name**：为固定值androidx.startup.InitializationProvider
   * **authorities**：格式为"${applicationId}.androidx-startup"
   * exported：false
   * node：merge
   * meta-data
     * **name：为暴露给系统的、首次执行初始化代码的、自定义的Initializer类的全限定名**
     * value：androidx.startup

3. 对于Provider中所暴露给系统的Initializer A所依赖的其他第三方库或者类的Initializer B，如果在A的dependencis方法中返回的list中包含B的class对象，那么系统会自动将B的初始化代码优先执行

上面的第二步骤实际上是一种静态形式的发现与调用，也可以采用**动态形式**的指定第一个执行初始化的Initializer：

```kotlin
AppInitializer.getInstance(context)
    .initializeComponent(ExampleLoggerInitializer::class.java)
```



#### 实例

```kotlin
// Initializes WorkManager.
class WorkManagerInitializer : Initializer<WorkManager> {
    override fun create(context: Context): WorkManager {
        val configuration = Configuration.Builder().build()
        WorkManager.initialize(context, configuration)
        return WorkManager.getInstance(context)
    }
    override fun dependencies(): List<Class<out Initializer<*>>> {
        // No dependencies on other libraries.
        return emptyList()
    }
}

// Initializes ExampleLogger.
class ExampleLoggerInitializer : Initializer<ExampleLogger> {
    override fun create(context: Context): ExampleLogger {
        // WorkManager.getInstance() is non-null only after
        // WorkManager is initialized.
        return ExampleLogger(WorkManager.getInstance(context))
    }

    override fun dependencies(): List<Class<out Initializer<*>>> {
        // Defines a dependency on WorkManagerInitializer so it can be
        // initialized after WorkManager is initialized.
        return listOf(WorkManagerInitializer::class.java)
    }
}
```

可以看到，ExampleLogger依赖于WorkManager，所以其dependencies方法返回的list中包含后者。

```xml
<provider
    android:name="androidx.startup.InitializationProvider"
    android:authorities="${applicationId}.androidx-startup"
    android:exported="false"
    tools:node="merge">
    <!-- This entry makes ExampleLoggerInitializer discoverable. -->
    <meta-data  android:name="com.example.ExampleLoggerInitializer"
          android:value="androidx.startup" />
</provider>
```

只需要那个ExampleLogger声明在Provider中即可。

