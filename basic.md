# 基本概念

## 访问控制（access control）
共有方法或变量（public）可以在类外任意访问；私有方法或变量（private）只能在类内或被友元（friend）访问；保护方法或变量（protected）可以被派生类（subclass）访问，但不能被类外代码访问。
## 名称空间
## 拷贝构造函数 （copy constructor）

拷贝构造函数默认存在。如果类中有指针成员，涉及到动态分配内存，则必须显示地定义拷贝构造函数。

```cpp
class X
{
public:
	X(const X& x) {
		...
	};
	...
};
```

拷贝构造函数会在一下场景中被使用：

1. 用已存在的对象初始化另一个对象
2. 将对象作为函数参数传入
3. 将对象作为函数返回值传出

## 静态成员／函数 (static)
函数内的静态变量只会被初始化一次。静态变量存储在静态空间（static storage），而非系统栈（stack）。

```cpp
void func()
{
	static int var = 0;
	...
}
```
若静态变量是一个类，则只会调用一次构造函数，析构函数会在程序结束时调用。

类中的静态成员(static member)为此类的所有对象共同拥有。在使用前，必须初始化类外用范围解析操作符``::``初始化。

```cpp
class X
{
	static int var;
	...
};

int X::var = 1;
```

类中的静态函数（static function）作用于整个类，而非某一对象。静态函数可以直接通过类名或对象调用。

```cpp
class X
{
public:
	static void func() {};
	...
};

void main() {
	X::func();
	X x;
	x.func();
	...
}
```

静态函数只能使用静态成员或者调用静态函数。非静态函数无此要求。

## 常量成员／函数
常量函数（const）不允许修改类成员。
## 内联函数（inline function）
所有类中定义的成员函数均为内联函数（inline）。内联函数在编译时会被直接复制，以减少调用函数的成本。另外，编译器会实现内联函数的前向引用（forward reference）。只有在类定义结束时，才会真正计算成员函数。

一般只对比较短小的函数使用内联，以避免代码过于臃肿（code bloat）产生缓存缺失（cache miss）或增加编译时间。所以，对于较长的成员函数，推荐在类外用范围解析操作符``::``定义。常见的内联函数包括get函数（Accessor）和set函数（Mutator）。
## 友元函数
## 指针
