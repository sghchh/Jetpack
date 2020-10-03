# Jetpack DataStore

DataStore是一种更方便的在Android中实现持久化存储的方式，可以存储键值对来替代Preference；同时，还支持存储普通的对象。

#### 添加依赖

想要使用DataStore需要添加如下依赖：

```groovy
dependencies {
  // Preferences DataStore
  implementation "androidx.datastore:datastore-preferences:1.0.0-alpha01"

  // Proto DataStore
  implementation "androidx.datastore:datastore-core:1.0.0-alpha01"
}
```

#### Preference DataStore简单使用

想要创建一个用于代替Preference的DataStore，需要使用**Context**的扩展函数**createDataStore**，该函数中的**命名参数name是必填的**，用于指明DataStore的名字：

```kotlin
val dataStore: DataStore<Preferences> = context.createDataStore(
  name = "settings"
)
```

想要往Preference DataStore中写入数据，需要使用其提供的**edit()**函数，该函数的参数是一个变换transform函数，通常使用lambda实现：

```kotlin
suspend fun incrementCounter() {
  dataStore.edit { settings ->
    val currentCounterValue = settings[EXAMPLE_COUNTER] ?: 0
    settings[EXAMPLE_COUNTER] = currentCounterValue + 1
  }
}
```

可以看到，edit函数的transform函数的参数为我们修改Preference DataStore中的值提供了一种数组式的访问格式。但是，值得一提的是，在DataStore的机制中，这里的key值EXAMPLE_COUNTER并不是简简单单的一个字符串，而是需要**使用preferencesKey<?()函数创建**的：

```kotlin
val EXAMPLE_COUNTER = preferencesKey<Int>("example_counter")
```

这是很大的一点不同。

在Preference DataStore中读取数据的**返回值是一个Kotlin中的Flow对象**，并不是该key对应的value，这是需要注意的一点：

```kotlin
val EXAMPLE_COUNTER = preferencesKey<Int>("example_counter")
val exampleCounterFlow: Flow<Int> = dataStore.data
  .map { preferences ->
    // No type safety.
    preferences[EXAMPLE_COUNTER] ?: 0
}
```

访问DataStore中的数据需要访问其data属性，同样，也可以在data属性之上增加一些变换操作。

#### Proto DataStore

这种DataStore**使用Protocol Buffer去持久化数据到磁盘**。

