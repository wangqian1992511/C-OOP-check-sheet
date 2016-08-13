# 基本概念

## 访问控制（access control）

共有方法或变量（public）可以在类外任意访问；私有方法或变量（private）只能在类内或被友元（friend）访问；保护方法或变量（protected）可以被派生类（subclass）访问，但不能被类外代码访问。

## 名称空间（namespace）

名称空间可以用来定义可见范围（scope）。也就是说，在不同名称空间内的相同标识符（identifier）是互相没有干扰的。

定义一个名称空间，要使用namespace关键字：

```cpp
namespace Space {
	...
}
```

创建名称空间时有一些要求：

1. 名称空间只能在全局空间或另一个名称空间中被定义
2. 名称空间是无法被实例化的
3. 名称空间可以在不同文件之间拓展（比如在如下的Header2.h中，名称空间Space有var1和var2两个成员）

```cpp
// Header1.h
namespace Space {
	int var1;
}

// Header2.h
#include "Header1.h"
namespace Space {
	int var2;
}
```

使用名称空间有以下方法：

1. 通过范围解析（scope resolution）操作符``::``（``Space::x``）
2. 使用using关键字（``using namespace Space;`` 或 ``using Space::x``）

可以发现类本身就好比一个名称空间。后面将会提到，范围解析操作符在类中可以用来调用静态成员或函数。然而，类是可以实例化的，而名称空间不能。

## 拷贝构造函数 （copy constructor）

拷贝构造函数默认存在。如果类中有指针成员，涉及到动态分配内存，则必须显示地定义拷贝构造函数。

```cpp
class X {
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
void func() {
	static int var = 0;
	...
}
```
若静态变量是一个类，则只会调用一次构造函数，析构函数会在程序结束时调用。

类中的静态成员(static member)为此类的所有对象共同拥有。在使用前，必须初始化类外用范围解析操作符``::``初始化。

```cpp
class X {
	static int var;
	...
};

int X::var = 1;
```

类中的静态函数（static function）作用于整个类，而非某一对象。静态函数可以直接通过类名或对象调用。

```cpp
class X {
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

## 常量成员／函数（const）

函数内的常量变量必须在声明时初始化，而且之后不能进行修改。在C++的习惯中，已经不使用define定义宏作为常量，而是使用const关键字。

```cpp
void func() {
	const int var = 0;
	...
}
```

const和指针同时有两种情况。像如下所示，const在星号左边，表示指针指向一个常量：

```cpp
const int* pt1;
int const* pt2;
```

而下面这种const在星号右边的情况表示指针本身是不可变的:

```cpp
int var = 1;
int* const pt = &var;
```

当然也可以同时出现两个const：

```cpp
const int var = 1;
const int* const pt = &var;
```

const也可以用来修饰函数的参数和返回值。当const修饰参数时，只有const能被传入。当const修饰返回值时，如果返回值为用户自行定义的变量类型，则返回的变量无法被修改。

下面谈一下const和类的关系。一种情况是const用来修饰类中的成员，形成常量成员。则这样的成员变量必须在构造函数中初始化，且之后不能修改。

若一个成员函数为常量成员函数，则我们需要在其参数列表后标示const：

```cpp
class X {
	void func() const;
};
```

这样的常量成员函数不允许修改类的成员变量。如果一个函数不需要修改类的成员，我们通常将其设为常量成员函数，以增强程序的鲁棒性。

如果一个类本身在声明时被const修饰，则其成员变量再任何情况下都不会被修改。

## 内联函数（inline function）
所有类中定义的成员函数均为内联函数（inline）。内联函数在编译时会被直接复制，以减少调用函数的成本。另外，编译器会实现内联函数的前向引用（forward reference）。只有在类定义结束时，才会真正计算成员函数。

一般只对比较短小的函数使用内联，以避免代码过于臃肿（code bloat）产生缓存缺失（cache miss）或增加编译时间。所以，对于较长的成员函数，推荐在类外用范围解析操作符``::``定义。常见的内联函数包括get函数（Accessor）和set函数（Mutator）。

## 友元函数（friend function）

友元函数是在类的范围外定义的函数，却可以访问类的所有成员（包括私有成员）。尽管它在类中声明，但由于其定义在类外，所以友元函数不属于成员函数。另外，友元函数也违反了类的封装性（encapsulation）。

```cpp
class X {
   int var;
public:
   friend int getVar(X x);
   ...
};

int getVar(X x) {
   return x.var;
}
```

用类似的方法，我们还可以指定另一个类的某个或所有成员函数为一个类的友元函数。

```cpp
class X {
public:
	void func();
};

class Y {
public:
	void func();
};

class Z {
private:
	int var;
public:
	friend void X::func(); 
	friend class Y; 
};
```

## this指针 (this)

所有的对象都可以通过this指针指向自己。通过this指针可以访问成员变量和成员函数。不过，由于友元函数不是成员函数，所以友元函数无法通过this指针访问。















