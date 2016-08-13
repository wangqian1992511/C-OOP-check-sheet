# 继承

继承（inheritance）可以方便我们用父类（parent／base／super）来定义子类（child／derived／sub）。继承关系本质上是is-a关系。继承声明如下：

```cpp
class Sub : access_mode Super, access_mode Super, ..., access_mode Super {
	...
}
```

## 访问控制（access control）

父类中的公有成员和保护成员可以被继承。其在子类中的类型如下表定义：

|                  | private mode | protected mode | public mode |
|:----------------:|:------------:|:--------------:|:-----------:|
|  privated member |       X      |        X       |      X      |
| protected member |    private   |    protected   |  protected  |
|   public member  |    private   |    protected   |    public   |


一般来说，我们都是用公有继承。

需要注意的是一些特殊的公有成员也不被继承，比如构造函数、析构函数、赋值号＝等等。

## 继承类型（inheritance types）

单一继承（single inheritance）是最基本的继承关系，比如“摩托车is-a车”。多层继承（multilevel inheritance）是多个单一继承组成的单链，比如“雅马哈is-a摩托车is-a车”。

多重继承（multiple inheritance）是指一个子类从多个父类继承而来，比如“雅马哈is-a摩托车”的同时“雅马哈is-a快艇”。虚继承（virtual inheritance）是指在多重继承的基础上再增加一层父类，比如在上面的例子中加上“摩托车is-a交通工具”和“快艇is-a交通工具”。

多重继承和虚继承会产生一些问题。这些问题会在后面提及。通常情况下，我们并不推荐使用多重继承和虚继承。

## 构造函数（constructor）

在声明子类时，子类的构造函数会先被调用，之后父类的缺省（无参数）构造函数会被调用。如果想调用父类的有参数构造函数，需要：

```cpp
class Sub : public Super {
	Sub(int x) : Super(x) {
		...
	}
}
```

这种设计是因为子类只能访问自身的成员，不过它仍然拥有父类的性质。所以必须通过父类的构造函数进行初始化。

多重继承在初始化时会调用所有父类的构造函数。

## 向上转型（upcasting）

所谓向上转型是指父类的指针或者引用可以指向其子类的对象，比如：

```cpp
Super* ptr;
Sub obj;
ptr = &obj;
 
Super &ref;
ref=obj;
```
