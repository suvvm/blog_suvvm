---
title: Java琐碎知识点汇总
date: 2020-03-16 17:53:06
categories: 
- Java
tags:
- 知识点汇总
---

# Java琐碎知识点汇总

- javac指令有些类似于make 会自动寻找并编译对应依赖的类，java编译器在编译源文件时不检查目录结构，但若类类与其对应的包目录不匹配，虚拟机就找不到类
- JAR文件使用ZIP格式组织文件和子目录，可用使用所有ZIP使用程序查看JAR文件内部
- char类型一般不建议使用（⑪占2个代码单元），建议将字符串作为抽线数据类型处理（除非确实要处理UTF16代码单元） 
- 布尔类型值与整形值不能相互转换 (if (x = 0) 不能编译通过)
- JAVA不区分变量的声明与定义 (int i = 10 or extern int i)
- 整数被零除产生除零异常 浮点数被0除产生无穷大或NaN
- 可以通过为类或方法添加strictfp关键词，其所有指令都将使用严格浮点数计算（可能产生溢出）
- 有精度丢失的整型向浮点型的类型转换 int->float long->float long->double
- Java字符串为不可变字符串，对字符串变量的修改实际为使其引用另一字符串
- Java不允许内层定义的变量覆盖外层定义的变量
- 长度为0的数组与null值不同
- main函数args数组用来存放命令行参数（如java DemoClass -g AAA BBB args[0] = "-g" args[1] = "AAA" args[2] = "BBB"）
- Java没有严格意义上的多维数组，其实现为元素为数组的数组
- 局部变量不会自动初始化为null，必须调用new 或将其设为null进行初始化
- 关键词this表示当前方法隐式参数
- Java method参数使用情况:
  - 方法不能修改基本数据类型的参数
  - 方法可以改变对象参数的状态
  - 方法不能让对象参数引用一个新的参数
- Java默认域初始化:
  	如果在构造器中没有显示的对域赋予初值，将被自动赋为默认值（数值为0、布尔为false、对象引用为null）
- 当且仅当一个类不存在任何构造器时系统才会提供默认构造器
- 静态初始化块 -> 初始化块 -> 构造方法
- Java调用构造器的具体处理步骤
  1. 所有数据域被初始化为默认值
  2. 按照在类声明中出现的次序，依次执行所有域初始化语句和初始化块
  3.  若构造器第一行调用了第二个构造器则执行第二个构造器主体
  4.  执行该构造器主题
- Java有自动垃圾回收器，不支持析构器，可以为类添加finalize方法（在垃圾回收器清除对象之前调用）可用System.runFinalizerOnExit(true)确保finalizer方法在Java关闭前被调用（不安全）或使用Runtime.addShutdownHook 添加关闭钩（安全）
- 包命名规范 包名为逆序域名.项目名.模块名
- #include 与 import无共同点 C++编译器无法查看文件内部所以#include用于加载外部特性声明 Java编译器可以查看文件内部，所以即使不使用import也可以显示的给出包名，也可以使用import导入静态方法与静态域
- 可用为超类变量赋值子类引用，但不能为子类变量赋值超类引用
- 由于每次调用方法都要搜索时间开销太大，虚拟机预先为每个类创建了一个方法表（method table）
- 不允许被扩展的类叫final类 不能被子类方法覆盖的方法叫final方法 String就是final类
- 包含抽象方法（abstract）的类必须为抽象类
- 抽象类变量永远引用不到抽像类而是引用其具体子类对象
- protected域可以被子类访问也可以被本包其他类访问所以安全性比C++交差，Object clone方法为protected方法
- 只有基本类型不是对象 所有数组类型，不管是对象数组还是基本类型数组都扩展了Object类
- Object.equals方法比较对象时判断的是两个对象是否具有相同的引用，对于大多数类来说这种判断并没有什么意义，所以需要对其覆盖（重写）且equals满足自反性、对称性传递性和一致性
- hashCode定义在Object中，每个对象都有一个默认的散列码为对象的存储地址，字符串hashCode由字符串内容导出，内容相同的字符串hashCode相同
- 重写定义equals就要重写hashCode以便用户可以将对象插入散列表，重写hashCode时应用null安全的Object.hashCode获取对象内部属性hashCode，应保证equals相等为hashCode相等的充分条件，hashCode相等为equals相等的必要条件
- 数组继承了Object类的toString方法，数组类型将按Object toString打印所属类名和散列码，建议使用静态方法Arrays.toString
- ArrayList管理的数组空间被用尽时，数组列表会自动创建一个更大的数组并将所有对象从较小的数组中拷贝至较大的数组中而不是去扩展较小的数组
- 可以通过 ensureCapacity(size) 或者 new ArrayList<>(size)为数组列表设置初始容量，数组列表将直接创建包含size个对象的内部数组
- 若确定ArrayList数组列表大小不会再次改变后可以调用trimToSize 将存储区大小调整为当前元素数量所需要的储存空间，垃圾回收器将回收多余存储空间
- Java编译器在对类型转换检查后如果没有违规情况会将所有类型化数组列表转换为ArrayList对象 所以类型转换(ArrayList) 和 (ArrayList<ClassName>)将执行相同的运行时检查（warning照样有）
- 所有基本类型的包装类都是不可变类
- 包装类与基本类型相互赋值是会进行自动拆箱与自动装箱，算数表达式也可以
- boolean、byte、char<=127 int或short介于-128~127之间将被包装到固定对象中，它们的==比较一定成立
- 要想实现改变参数值的方法包装类也无法实现，但可以使用org.omg.CORBA包中的 holder类型（其值域为公有）
- Object... 表示该函数可以接收任意数量的对象
- Java printf 实际接收至为Object数组（允许将一个数组传递给可变阐述方法的最后一个参数）
- 某个特定类可以临时转换为Object 但初始就是Object类永远不能转为其他特定类
- 可以在枚举类型中添加一些构造器、方法和域
- 反射被大量应用于JavaBeans中
- Class类实则用于访问郧西是类型信息，Object 类中的 getClass( ) 方法将会返回一个 Class 类型的实例
- 虚拟机为每个类型管理一个Class对象，所以 对getClass方法进行==比较完全没有问题
- 反射显示对象私有域内容时可用setAccessible为反射对象设置可访问标志屏蔽Java语言的访问检查
- 接口中的函数默认public不用声明，但实现接口时必备吧对应方法声明为public
- 接口不是类，不能实例化，接口变量应引用实现了接口的类的对象
- Java Array.sort()方法使用的排序类型 小于47个元素使用插入排序 大于47小于286使用快排 大于286梳理数据使用归并排序若降序数组过多转回快排
- 接口默认方法冲突默认解决：超类优先 若某个接口提供了默认方法与另一接口某方法或参数冲突则必须覆盖（重写）该方法
- 若一个类的超类中某方法与接口默认方法冲突则接口默认方法被忽略（类优先原则）
- BiFunction<T,U,R> 可以用来保存lambda表达式可惜大部分情况莫得卵用
- 使用类名::方法名可以传递方法引用（如System.out::println或Math::max）如果对应方法有多个版本编译器会尝试从上下文中找出应该使用哪一个方法
- lambda表达式中的this关键词指的是创建这个lambda表达式的方法的this
- 所有内部类都有指向其外部类的引用，只有静态内部类没用指向外部类的引用
- 声明在接口中的内部类自动作为public statis
- Java的Exception异常分为RuntimeException与IOException，其中RuntimeException与C++logic_error类（程序中的逻辑错误）类似而不是runtime_error类，runtime_error类是所有由不可预知的原因引发的异常的基类
- 使用try/catch/finally时，当finally子句包含return语句时其返回值将覆盖try块与catch块中的return语句
- 异常处理效率低下，不能代替简单判断测试
- -verbose标志启动JVM可以观察类的加载过程
- JDK提供jconsole来显示JVM性能统计结果使用jconsole processID运行
- jmap可以获得一个堆的转储，显示堆中的每个对象使用
  jmap -dump:format=b,file=dumpFileName processID
  jhat dumpFileName
- Java定义泛型类型都的自动提供相应的原始类型 （删去类型参数的泛型类型名，查出类型变量替换为限定型，无限定用Object）
- Java泛型不能用类型参数代替基本类型（没有Pair\<double>只有Pair\<Double>，类型擦除之后含有Object类型的域 Object不能存储double）
- Java泛型不能实例化参数化类型的数组
  Pair\<String>[] table = new Pair\<String>[10];	// Error
  但Pair\<String>[]变量声明依旧合法,可通过声明通配类型数组，然后进行类型转换
  Pair\<String>[] table = (Pair\<String>[])new Pair\<?>[10];
- Java泛型类中静态上下文变量无效
- 不能抛出或捕获泛型类的实例对象
- 若S是T的子类Pair\<S>与Pair\<T>没有任何关系
- Java集合迭代器与C++迭代器并不相同，不直接指向对应元素，而更类似于InputStream.read每次消耗掉一个元素，所以remove操作要与next操作对应
- Java ArrayList默认大小10，扩容规则：旧容量 + 旧容量 >> 1(扩容1.5倍)
- ClassLoader中loadClass()方法结束后并未完成类的初始化
- 通过子类全类名使用父类静态变量，只会初始化父类的静态域而不会初始化子类的静态域
- 静态代码块只有被访问时才会进行初始化