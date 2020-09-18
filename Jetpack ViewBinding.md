# Jetpack ViewBinding

在传统的Android开发的过程中，获取控件通过findViewById的方式显得过于麻烦，Jetpack为我们提供了ViewBinding组件可以便捷地访问控件，ViewBinding的作用很简单，在开启ViewBinding之后，**会为每一个XML布局文件生成一个绑定类**，该绑定类会**包含对根视图及其具有ID的所有视图的引用**，之后我们可以通过该绑定类对象，像访问该对象的属性那样访问各个视图。ViewBinding为我们生成的绑定类的名称为：**XML 文件的名称转换为驼峰式大小写，并在末尾添加“Binding”一词**，例如result_profile.xml生成的绑定类为ResultProfileBinding。

> ViewBinding会为每一个XML布局文件生成绑定类，如果想要忽略某一个XML布局文件，那么需要在该XML的根视图下增加属性**tools:viewBindingIgnore="true"**

### 1. Activity中使用ViewBinding

在Activity中使用ViewBinding的步骤为：

1. 调用**绑定类的静态方法inflate**生成绑定类对象
2. 通过第一步生成的绑定类对象的**getRoot方法或者root属性**访问对应XML中的根视图View
3. 将第二步获取到的root视图view传递到Activity的**setContentView**方法中
4. 之后就可以通过第一步得到的绑定类对象来自由访问视图下的控件了

```kotlin
    private lateinit var binding: ResultProfileBinding

    override fun onCreate(savedInstanceState: Bundle) {
        super.onCreate(savedInstanceState)
        binding = ResultProfileBinding.inflate(layoutInflater)
        val view = binding.root
        setContentView(view)
    }
```

### 2. Fragment中使用ViewBinding

在Fragment中使用ViewBinding的步骤同Activity中类似，其执行步骤位于其**onCreateView**方法下：

1. 调用**绑定类的静态方法inflate**生成绑定类对象，而这里的inflate方法的调用同传统的fragment调用时一样的，三个参数分别是inflater、container、false
2. 通过第一步生成的绑定类对象的**getRoot方法或者root属性**访问对应XML中的根视图View
3. 从**onCreateView()方法**中返回第二步获取的根视图View

```kotlin
    private var _binding: ResultProfileBinding? = null
    // This property is only valid between onCreateView and
    // onDestroyView.
    private val binding get() = _binding!!

    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View? {
        _binding = ResultProfileBinding.inflate(inflater, container, false)
        val view = binding.root
        return view
    }

    override fun onDestroyView() {
        super.onDestroyView()
        _binding = null
    }
    
```

