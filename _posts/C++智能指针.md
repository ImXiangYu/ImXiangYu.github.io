+++
title = 'C++智能指针'
date = 2024-08-18T23:20:13+08:00
draft = false
+++

# C++11 智能指针

本文参考视频：[C++11实用特性](https://www.bilibili.com/video/BV1bX4y1G7ks/?p=4&share_source=copy_web&vd_source=822e2b085983c89592154feee56920ef) 本文参考博客：[C++ 教程 | 爱编程的大丙 (subingwen.cn)](https://subingwen.cn/cplusplus/)

在C++中没有垃圾回收机制，必须自己释放分配的内存，否则就会造成内存泄露。解决这个问题最有效的方法是使用智能指针（smart pointer）。智能指针是存储指向动态分配（堆）对象指针的类，用于生存期的控制，能够确保在离开指针所在作用域时，自动地销毁动态分配的对象，防止内存泄露。智能指针的核心实现技术是引用计数，每使用它一次，内部引用计数加1，每析构一次内部的引用计数减1，减为0时，删除所指向的堆内存。

C++中提供了三种智能指针，使用时需要`#include <memory>` 分别是： `std::shared_ptr`：共享的智能指针 `std::unique_ptr`：独占的智能指针 `std::weak_ptr`：弱引用的智能指针，不共享指针，不能操作资源，只是用来监视`shared_ptr`的

## 1. 共享智能指针

共享智能指针是指多个智能指针可以同时管理同一块有效的内存，共享智能指针是一个模板类，如果要初始化，有三种方式：构造函数、`std::make_shared`辅助函数、reset方法。

共享智能指针对象初始化完毕后就指向了要管理的那块堆内存，如果想要查看当前有多少个智能指针同时管理着这块内存，可以使用一个成员函数`use_count()`，函数原型如下：

```c++
// 管理当前对象的 shared_ptr 实例数量，或若无被管理对象则为 0。
long use_count() const noexcept;
```

### shared_ptr的初始化

**构造函数初始化**

```c++
std::shared_ptr<T> ptr_name;
```

使用实例：

```c++
#include <iostream>
#include <memory>
using namespace std;

int main()
{
  // 使用智能指针管理一块 int 型的堆内存
  shared_ptr<int> ptr1(new int(520));
  cout << "ptr1管理的内存引用计数: " << ptr1.use_count() << endl;
  // 使用智能指针管理一块字符数组对应的堆内存
  shared_ptr<char> ptr2(new char[12]);
  cout << "ptr2管理的内存引用计数: " << ptr2.use_count() << endl;
  // 创建智能指针对象, 不管理任何内存
  shared_ptr<int> ptr3;
  cout << "ptr3管理的内存引用计数: " << ptr3.use_count() << endl;
  // 创建智能指针对象, 初始化为空
  shared_ptr<int> ptr4(nullptr);
  cout << "ptr4管理的内存引用计数: " << ptr4.use_count() << endl;

  return 0;
}
```

输出结果：

```c++
ptr1管理的内存引用计数: 1
ptr2管理的内存引用计数: 1
ptr3管理的内存引用计数: 0
ptr4管理的内存引用计数: 0
```

**注意：**不能使用一个原始指针初始化多个`shared_ptr`

```c++
int *p = new int;
shared_ptr<int> p1(p);
shared_ptr<int> p2(p);        // error, 编译不会报错, 运行会出错
```

**通过拷贝和移动构造函数初始化**

```c++
#include <iostream>
#include <memory>
using namespace std;

int main()
{
  // 使用智能指针管理一块 int 型的堆内存, 内部引用计数为 1
  shared_ptr<int> ptr1(new int(520));
  cout << "ptr1管理的内存引用计数: " << ptr1.use_count() << endl;
  //调用拷贝构造函数
  shared_ptr<int> ptr2(ptr1);
  cout << "ptr2管理的内存引用计数: " << ptr2.use_count() << endl;
  shared_ptr<int> ptr3 = ptr1;
  cout << "ptr3管理的内存引用计数: " << ptr3.use_count() << endl;
  //调用移动构造函数
  shared_ptr<int> ptr4(std::move(ptr1));
  cout << "ptr4管理的内存引用计数: " << ptr4.use_count() << endl;
  std::shared_ptr<int> ptr5 = std::move(ptr2);
  cout << "ptr5管理的内存引用计数: " << ptr5.use_count() << endl;

  return 0;
}
```

如果使用拷贝的方式初始化共享智能指针对象，这两个对象会同时管理同一块堆内存，堆内存对应的引用计数也会增加；如果使用移动的方式初始智能指针对象，只是转让了内存的所有权，管理内存的对象并不会增加，因此内存的引用计数不会变化。因此输出结果为：

```c++
ptr1管理的内存引用计数: 1
ptr2管理的内存引用计数: 2
ptr3管理的内存引用计数: 3
ptr4管理的内存引用计数: 3
ptr5管理的内存引用计数: 3
```

**通过`std::make_shared`初始化**
该函数可以完成内存对象的创建并将其初始化给智能指针，函数原型：

```c++
template< class T, class... Args >
shared_ptr<T> make_shared( Args&&... args );
```

`T`：模板参数的数据类型 `Args&&... args` ：要初始化的数据，如果是通过make_shared创建对象，需按照构造函数的参数列表指定

用法如下：

```c++
#include <iostream>
#include <string>
#include <memory>
using namespace std;

class Test
{
public:
  Test() 
  {
      cout << "construct Test..." << endl;
  }
  Test(int x) 
  {
      cout << "construct Test, x = " << x << endl;
  }
  Test(string str) 
  {
      cout << "construct Test, str = " << str << endl;
  }
  ~Test()
  {
      cout << "destruct Test ..." << endl;
  }
};

int main()
{
  // 使用智能指针管理一块 int 型的堆内存, 内部引用计数为 1
  shared_ptr<int> ptr1 = make_shared<int>(520);
  cout << "ptr1管理的内存引用计数: " << ptr1.use_count() << endl;

  shared_ptr<Test> ptr2 = make_shared<Test>();
  cout << "ptr2管理的内存引用计数: " << ptr2.use_count() << endl;

  shared_ptr<Test> ptr3 = make_shared<Test>(520);
  cout << "ptr3管理的内存引用计数: " << ptr3.use_count() << endl;

  shared_ptr<Test> ptr4 = make_shared<Test>("我是要成为海贼王的男人!!!");
  cout << "ptr4管理的内存引用计数: " << ptr4.use_count() << endl;
  return 0;
}
```

**通过`reset`初始化**
用法如下：

```c++
#include <iostream>
#include <string>
#include <memory>
using namespace std;

int main()
{
  // 使用智能指针管理一块 int 型的堆内存, 内部引用计数为 1
  shared_ptr<int> ptr1 = make_shared<int>(520);
  shared_ptr<int> ptr2 = ptr1;
  shared_ptr<int> ptr3 = ptr1;
  shared_ptr<int> ptr4 = ptr1;
  cout << "ptr1管理的内存引用计数: " << ptr1.use_count() << endl;
  cout << "ptr2管理的内存引用计数: " << ptr2.use_count() << endl;
  cout << "ptr3管理的内存引用计数: " << ptr3.use_count() << endl;
  cout << "ptr4管理的内存引用计数: " << ptr4.use_count() << endl;

  ptr4.reset();
  cout << "ptr1管理的内存引用计数: " << ptr1.use_count() << endl;
  cout << "ptr2管理的内存引用计数: " << ptr2.use_count() << endl;
  cout << "ptr3管理的内存引用计数: " << ptr3.use_count() << endl;
  cout << "ptr4管理的内存引用计数: " << ptr4.use_count() << endl;

  shared_ptr<int> ptr5;
  ptr5.reset(new int(250));
  cout << "ptr5管理的内存引用计数: " << ptr5.use_count() << endl;

  return 0;
}
```

对于一个未初始化的共享智能指针，可以通过reset方法来初始化，当智能指针中有值的时候，调用reset会使引用计数减1。因此输出结果如下：

```c++
ptr1管理的内存引用计数: 4
ptr2管理的内存引用计数: 4
ptr3管理的内存引用计数: 4
ptr4管理的内存引用计数: 4

ptr1管理的内存引用计数: 3
ptr2管理的内存引用计数: 3
ptr3管理的内存引用计数: 3
ptr4管理的内存引用计数: 0

ptr5管理的内存引用计数: 1
```

### 获取原始指针

当使用智能指针时，原始地址就不见了，当我们想要修改变量或者对象中的值时，需要先从智能指针对象中取出数据的原始内存地址再操作，解决方法是使用`get()` ，用法如下：

```c++
#include <iostream>
#include <string>
#include <memory>
using namespace std;

int main()
{
    int len = 128;
    shared_ptr<char> ptr(new char[len]);
    // 得到指针的原始地址
    char* add = ptr.get();
    memset(add, 0, len);
    strcpy(add, "我是要成为海贼王的男人!!!");
    cout << "string: " << add << endl;

    shared_ptr<int> p(new int);
    *p = 100;
    cout << *p.get() << "  " << *p << endl;

    return 0;
}
```

### 指定删除器

当智能指针管理的内存对应的引用计数为0时，内存会被智能指针析构掉。但我们在初始化智能指针的时候也可以自己指定删除操作，这个删除操作对应的函数被称为删除器。用法如下：

```c++
#include <iostream>
#include <memory>
using namespace std;

// 自定义删除器函数，释放int型内存
void deleteIntPtr(int* p)
{
    delete p;
    cout << "int 型内存被释放了...";
}

int main()
{
    shared_ptr<int> ptr(new int(250), deleteIntPtr);
    return 0;
}
```

删除器也可以是lambda表达式，因此可以写成：

```c++
int main()
{
    shared_ptr<int> ptr(new int(250), [](int* p) {delete p; });
    return 0;
}
```

也可以选择使用默认的删除器：

```c++
int main()
{
    shared_ptr<int> ptr(new int[10], default_delete<int[]>());
    return 0;
}
```

## 2. 独占智能指针

`std::unique_ptr`是一个独占型的智能指针，它不允许其他的智能指针共享其内部的指针，可以通过它的构造函数初始化一个独占智能指针对象，但是不允许通过赋值将一个`unique_ptr`赋值给另一个`unique_ptr`。

### unique_ptr的初始化

1. 使用构造函数初始化：
   
   ```c++
   // 通过构造函数初始化对象
   unique_ptr<int> ptr1(new int(10));
   // error, 不允许将一个unique_ptr赋值给另一个unique_ptr
   unique_ptr<int> ptr2 = ptr1;
   ```

2. 使用转移初始化：
   
   `std::unique_ptr`不允许复制，但是可以通过函数返回给其他的`std::unique_ptr`，还可以通过`std::move`来转译给其他的`std::unique_ptr`，这样原始指针的所有权就被转移了，这个原始指针还是被独占的。用法如下：
   
   ```c++
   #include <iostream>
   #include <memory>
   using namespace std;
   
   unique_ptr<int> func()
   {
     return unique_ptr<int>(new int(520));
   }
   
   int main()
   {
     // 通过构造函数初始化
     unique_ptr<int> ptr1(new int(10));
     // 通过转移所有权的方式初始化
     unique_ptr<int> ptr2 = move(ptr1);
     unique_ptr<int> ptr3 = func();
   
     return 0;
   }
   ```

3. 使用`reset`初始化：
   使用reset方法可以让unique_ptr解除对原始内存的管理，也可以用来初始化一个独占的智能指针。
   
   ```C++
   int main()
   {
     unique_ptr<int> ptr1(new int(10));
     unique_ptr<int> ptr2 = move(ptr1);
   
     ptr1.reset(); //解除对原始内存的管理
     ptr2.reset(new int(250)); //重新指定智能指针管理的原始内存
   
     return 0;
   }
   ```

### 删除器

`unique_ptr`指定删除器和`shared_ptr`指定删除器是有区别的，`unique_ptr`指定删除器需要确定删除器的类型，不能像`shared_ptr`那样直接指定删除器

```c++
int main()
{
    using func_ptr = void(*)(int*);
    unique_ptr<int, function<void(int*)>> ptr1(new int(10), [&](int*p) {delete p; });
    return 0;
}
```

## 弱引用智能指针

弱引用智能指针`std::weak_ptr`可以看做是`shared_ptr`的助手，它不管理`shared_ptr`内部的指针。`std::weak_ptr`没有重载操作符`*`和`->`，因为它不共享指针，不能操作资源，所以它的构造不会增加引用计数，析构也不会减少引用计数，它的主要作用就是作为一个旁观者监视`shared_ptr`中管理的资源是否存在。

### weak_ptr的初始化

构造函数和拷贝构造初始化：

```c++
#include <iostream>
#include <memory>
using namespace std;

int main() 
{
    shared_ptr<int> sp(new int);

    weak_ptr<int> wp1;         // 构造了一个空的weak_ptr对象
    weak_ptr<int> wp2(wp1); // 通过一个空的weak_ptr对象构造了另外一个weak_ptr对象
    weak_ptr<int> wp3(sp);     // 通过一个shared_ptr对象构造了一个可用的weak_ptr对象
    weak_ptr<int> wp4;
    wp4 = sp;                // 通过一个shared_ptr对象隐式类型转换构造了一个可用的weak_ptr对象
    weak_ptr<int> wp5;
    wp5 = wp3;                // 通过一个weak_ptr对象构造了一个可用的weak_ptr对象

    return 0;
}
```

### 常用方法

1. `use_count()` 使用use_count()方法可以获得当前所观测资源的引用计数。

2. `expired()` 通过调用该方法可以判断观测的资源是否已经被释放
   
   ```c++
   #include <iostream>
   #include <memory>
   using namespace std;
   
   int main() 
   {
     shared_ptr<int> shared(new int(10));
     weak_ptr<int> weak(shared);
     cout << "1. weak " << (weak.expired() ? "is" : "is not") << " expired" << endl;
   
     shared.reset();
     cout << "2. weak " << (weak.expired() ? "is" : "is not") << " expired" << endl;
   
     return 0;
   }
   ```

3. `lock()` 通过调用该方法可以获取其所管理资源的`shared_ptr`对象，使用方法如下：
   
   ```c++
   #include <iostream>
   #include <memory>
   using namespace std;
   
   int main()
   {
     shared_ptr<int> sp1, sp2;
     weak_ptr<int> wp;
   
     sp1 = std::make_shared<int>(520);
     wp = sp1;
     sp2 = wp.lock(); // 此时引用计数为2
     cout << "use_count: " << wp.use_count() << endl;
   
     sp1.reset(); // 引用计数-1
     cout << "use_count: " << wp.use_count() << endl;
   
     sp1 = wp.lock(); // 引用计数+1
     cout << "use_count: " << wp.use_count() << endl;
   
     cout << "*sp1: " << *sp1 << endl;
     cout << "*sp2: " << *sp2 << endl;
   
     return 0;
   }
   ```
   
   输出结果为：
   
   ```c++
   use_count: 2
   use_count: 1
   use_count: 2
   *sp1: 520
   *sp2: 520
   ```

4. `reset()` 清空`weak_ptr`，使其不监测任何资源

### 返回管理this的shared_ptr

如果我们想得到管理当前对象的`shared_ptr`，可以使用`weak_ptr`来解决，C++为我们提供了一个模板类叫做`std::enable_shared_from_this<T>`，这个类中有个`shared_from_this()`，通过这个方法可以返回一个`shared_ptr`，其在函数内部就是用`weak_ptr`监测`this` ，并调用`weak_ptr`的`lock()`方法返回一个`shared_ptr`对象。

```C++
#include <iostream>
#include <memory>
using namespace std;

struct Test : public enable_shared_from_this<Test>
{
    shared_ptr<Test> getSharedPtr()
    {
        return shared_from_this();
    }
    ~Test()
    {
        cout << "class Test is disstruct ..." << endl;
    }
};

int main() 
{
    shared_ptr<Test> sp1(new Test);
    cout << "use_count: " << sp1.use_count() << endl;
    shared_ptr<Test> sp2 = sp1->getSharedPtr();
    cout << "use_count: " << sp1.use_count() << endl;
    return 0;
}
```

**注意：**在调用enable_shared_from_this类的shared_from_this()方法之前，必须要先初始化函数内部weak_ptr对象，否则该函数无法返回一个有效的shared_ptr对象（具体处理方法可以参考上面的示例代码）

### 解决循环引用问题

如果智能指针被循环引用会导致内存泄漏，例如：

```c++
#include <iostream>
#include <memory>
using namespace std;

struct TA;
struct TB;

struct TA
{
    shared_ptr<TB> bptr;
    ~TA()
    {
        cout << "class TA is disstruct ..." << endl;
    }
};

struct TB
{
    shared_ptr<TA> aptr;
    ~TB()
    {
        cout << "class TB is disstruct ..." << endl;
    }
};

void testPtr()
{
    shared_ptr<TA> ap(new TA);
    shared_ptr<TB> bp(new TB);
    cout << "TA object use_count: " << ap.use_count() << endl;
    cout << "TB object use_count: " << bp.use_count() << endl;

    ap->bptr = bp;
    bp->aptr = ap;
    cout << "TA object use_count: " << ap.use_count() << endl;
    cout << "TB object use_count: " << bp.use_count() << endl;
}

int main()
{
    testPtr();
    return 0;
}
```

输出结果如下：

```c++
TA object use_count: 1
TB object use_count: 1
TA object use_count: 2
TB object use_count: 2
```

在共享指针只能离开作用域后引用计数只能减1，会导致TA和TB的实例对象不能被析构，最终导致内存泄漏，`weak_ptr`可以解决这个问题，只需要将TA或TB的任意一个成员改为`weak_ptr`即可。

```c++
#include <iostream>
#include <memory>
using namespace std;

struct TA;
struct TB;

struct TA
{
    weak_ptr<TB> bptr;
    ~TA()
    {
        cout << "class TA is disstruct ..." << endl;
    }
};

struct TB
{
    shared_ptr<TA> aptr;
    ~TB()
    {
        cout << "class TB is disstruct ..." << endl;
    }
};

void testPtr()
{
    shared_ptr<TA> ap(new TA);
    shared_ptr<TB> bp(new TB);
    cout << "TA object use_count: " << ap.use_count() << endl;
    cout << "TB object use_count: " << bp.use_count() << endl;

    ap->bptr = bp;
    bp->aptr = ap;
    cout << "TA object use_count: " << ap.use_count() << endl;
    cout << "TB object use_count: " << bp.use_count() << endl;
}

int main()
{
    testPtr();
    return 0;
}
```

## 总结

以上就是对C++11新添加的智能指针的总结，总的来说提升了指针使用时的安全性，同时也使得内存管理更为方便。
