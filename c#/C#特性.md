### C# 特性

特性提供功能强大的方法，用于将元数据或声明信息与代码（程序集，	类型，方法，属性等）相关联，特性与程序实体关联后，即可在运行时使用名为”反射“的技术查询特性

使用特性，以声明的方式将信息与代码相关联，特性还可以提供能够应用与各种目标的可重用元素

以 [Obsolete] 特性为例。 它可以应用于类、结构、方法、构造函数等。 用于声明元素已过时。 然后，由 C# 编译器负责查找此特性，并执行某响应操作。

### 如何将特性添加到代码中

在C#中，特性是	继承自 Attribute 基类的类，所有继承Attribute的类都可以用作其他代码的一种”标记“（实际是向目标添加元数据）

例如一个名为 ObsleteAttribute 的特性 它用于示意代码已经过时，不得再使用，可以将此特性应用于类

```c#
[Obsolete]
public class MyClass
{
}
```

请注意，虽然此类的名称为 ObsoleteAttribute，但只需在代码中使用 [Obsolete]。 这是 C# 遵循一项约定。 如果愿意，也可以使用全名 [ObsoleteAttribute]。

```C#
[Obsolete("ThisClass is obsolete. Use ThisClass2 instead.")]
public class ThisClass
{
}
```
此字符串会作为变量传递给 ObsoleAttribute 构造函数，就像在编写 
```c#
var attr = new ObsoleteAttribute("some string")
```
一样。

只能向特性构造函数传递以下简单类型参数

- bool
- int
- double
- string
- Type
- enums
- etc

和这些类新的数组，不能使用表达式或变量，可以使用任何位置参数或已命名参数

### 如何创建你的特性

创建特性与从 Attribute 基类继承一样简单
```c#
public class MySpecialAttribute : Attribute
{
}
```
执行上述操作后，我现在可以在基本代码中的其他位置使用 [MySpecial] （或 [MySpecialAttribute]） 特性

```c#
[MySpecial]
public class SomeOtherClass
{
}
```

.NET 基础库中的特性（如 ObsoleteAttribute）会在编译期间中触发某种行为，不过，你创建的任何特性只做元数据之用，并不会在编译期间在目标中生成任何相关代码，是否在代码的其他位置使用此元数据有你自行决定（使用反射）

这里要注意 ‘gotcha”，如上所述，使用特性时，只允许将某些类型的参数作为变量传递，不过，在创建特性类型时，C#编译器不会阻止你创建这些参数，如下这个类型可以正常被编译

```c#
public class GotchaAttribute : Attribute
{
    public GotchaAttribute(Foo myClass, string str) {
    }
}
```

不过，无法将此构造函数与特性语法结合使用

```c#
[Gotcha(new Foo(), "test")] // does not compile
public class AttributeFail
{
}
```

这段代码会在编译期间报错：Attribute constructor parameter 'myClass' has type 'Foo', which is not a valid attribute parameter type



### 如何限制特性使用

特性可用于多个目标，上述示例展示了特性在类上的使用情况，而特性还可用于：

- 程序集
- 类
- 构造函数
- 委托
- 枚举
- 事件
- 字段
- 泛型参数
- 接口
- 方法
- 模块
- 参数
- properties
- 返回值
- 结构

在创建特性类的时候，C#默认允许所有可能的特性目标使用此特性，如果要将特性	限制为只能用于特定目标，可以对特性特性类使用 AttributeUsageAttribute 来实现，没错，就是将特性应用于特性

```c#
[AttributeUsage(AttributeTargets.Class | AttributeTargets.Struct)]
public class MyAttributeForClassAndStructOnly : Attribute
{
}
```



如果尝试将上述特性应用于不是类也不是结构的对象，则会看到编译器错误，如 `Attribute 'MyAttributeForClassAndStructOnly' is not valid on this declaration type. It is only valid on 'class, struct' declarations`

### 如何使用附加到代码元素上的特定

特性只能用作元数据之用，不借助外在力量，特性其实没有什么用

若要	查找并使用特性，通常需要使用反射，

例如，可以使用反射获取类的相关信息（在代码开始处添加 using System.Reflection）

```c#
TypeInfo typeInfo = typeof(MyClass).GetTypeInfo();
Console.WriteLine("The assembly qualified name of MyClass is " + typeInfo.AssemblyQualifiedName);
```

此代码的打印输出如下：`The assembly qualified name of MyClass is ConsoleApplication.MyClass, attributes, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

获取 `TypeInfo` 对象（或 `MemberInfo`、`FieldInfo` 等）后，就可以使用 `GetCustomAttributes` 方法了。 这将返回一组 `Attribute` 对象。 还可以使用 `GetCustomAttribute` 并指定特性类型。

下面的示例展示了对 `MyClass`（在上文中，它包含 `[Obsolete]` 特性）的 `MemberInfo` 实例使用 `GetCustomAttributes`。

```c#
var attrs = typeInfo.GetCustomAttributes();
foreach(var attr in attrs)
    Console.WriteLine("Attribute on MyClass: " + attr.GetType().Name);
```



这将在控制台中打印输出 `Attribute on MyClass: ObsoleteAttribute`。 请尝试向 `MyClass` 添加其他特性。

请务必注意，这些 `Attribute` 对象的实例化有延迟。 也就是说，只有使用 `GetCustomAttribute` 或 `GetCustomAttributes`，它们才会实例化。 这些对象每次都会实例化。 连续两次调用 `GetCustomAttributes` 将返回两个不同的 `ObsoleteAttribute` 实例。
