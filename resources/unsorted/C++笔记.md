## 警告

- warning: ISO C++ forbids converting a string constant to 'char*'

```cpp
//c++ 11中规定如下赋值方式不正确
// valid in C, invalid in C++
char *p = "abc";
//正确的方式
char *p = (char*)"abc";
char const *p = "abc";
```

- 指针常量和常量指针

```cpp
//cpp中下面两种写法等价，表示指向常量的指针
//指针的值可变，指针指向的值不可变
const int *p;
int const *p;
//指针的值不可变，指向的值可变
int* const p
//二者都不可变d
int a
const int* const p
```

- 定义类

```cpp
#include <iostream>
using namespace std;

class Student {
private:
    int age;
public:
    void setAge(int a);//声明
};
//定义
void Student::setAge(int a){
    age = a;
}
```

- 在栈中和在堆中创建对象

```cpp
class Student {
public:
    int age;
};

int main(){
    Student stu;
    stu.age =1;
    //在堆上创建对象
    Student *pstu = new Student;
    //通过箭头符号访问对象成员
    pstu -> age = 1;
    delete pstu;
    //使用sizeof获取类的内存大学
    sizeof(Student);
    sizeof(*pstu);
    sizeof(stu)
}
```

- 命名空间和作用域

```cpp
#include <iostream>
using namespace std;

void func(){
    cout << "std::func" << endl;
}

namespace ns {
    void func(){
        std::cout << "ns::func" << endl;
    }
}

class Demo{
public:
    void func();
};

void Demo::func(){
    cout << "std::demo::func" << endl;
}

int main(){
    func();
    ns::func();
    Demo d;
    d.func();
    return 0;
}
```

- 构造函数

```cpp
#include <iostream>
 using namespace std;

 class Demo{
 public:
     int age;
     int const score;
     Demo();
     Demo(int a);
     Demo(int a, int s);
     ~Demo();
     void prt();
 };

 Demo::Demo(){}
 Demo::Demo(int a){
     age = a;
 }
//采用参数初始化表
//初始化 const 成员变量的唯一方法就是使用参数初始化表
 Demo::Demo(int a, int s):age(a), score(s){
     //
 }
 void Demo::prt(){
     cout << age << endl;
 }
/*
构造函数的作用是给类成员赋初始值
以下四种创建对象的方式中，
第一种方式，成员变量没有被初始化，是随机值
第三种方式，成员变量被初始化为默认值
使用 使用new创建对象时
new Demo和 new Demo()都可以，但是如果存在
带参数的构造函数时，需要手动定义无参构造函数
*/
 int main(){
     Demo d1;
     d1.prt();
     Demo d2(2);
     d2.prt();
     Demo *dp1 = new Demo;
     dp1 -> prt();
     Demo *dp2 = new Demo(4);
     dp2 -> prt();
     return 0;
}
//析构函数~Demo()，在对象消亡时会被自动调用，可以在
//析构函数中打印一些日志
```

- this指针

```cpp
//在类的非静态成员函数中返回类对象本身的时候，直接使用 return *this；
//当参数与成员变量名相同时，如this->n = n（不能写成n = n）
```

- 引用

```cpp
//写法，使用&符，注意与取地址值的区别
int a = 1;
int &b = a;
void swap(int &m, int &n);
//使用引用作为函数参数的优点
/*
1.传递引用给函数与传递指针的效果是一样的。这时，被调函数的形参就成为原来主调函数中的实参变量或对象的一个别名来使用，所以在被调函数中对形参变量的操作就是对其相应的目标 对象（在主调函数中）的操作。
2.使用引用传递函数的参数，在内存中并没有产生实参的副本，它是直接对实参操作；而使用一般变量传递函数的参数，当发生函数调用时，需要给形参分配存储单元，形参变量是实参变量的 副本；如果传递的是对象，还将调用拷贝构造函数。因此，当参数传递的数据较大时，用引用比 用一般变量传递参数的效率和所占空间都好。
3.使用指针作为函数的参数虽然也能达到与使用引用的效果，但是，在被调函数中同样要给形参分配存储单元，且需要重复使用”*指针变量名”的形式进行运算，这很容易产生错误且程序的阅 读性较差；另一方面，在主调函数的调用点处，必须用变量的地址作为实参。而引用更容易使用，更清晰。
*/
```

- 函数指针

```cpp
int func2(int x); 　　 /* 声明一个函数 */
int (*q2) (int x); 　　/* 声明一个函数指针 */
q2=func2; 　　　　   /* 将func函数的首地址赋给指针f */
int c=(*q2)(3);　　　//3可以为任意实参，通过函数指针调用函数
```

- 函数模板

```cpp
//定义一个函数模板
template<typename T> void swap(T &a, T &b){
    T temp = a;
    a = b;
    b = temp;
}
//调用
int a = 1;
int b = 2;
//注意 std 中有swap模板
//调用自定义的swap模板前面需要加上 :: 符号
```

- 友元函数
- 内联函数
- string
- 顺序容器和关联容器（sequential container and associate container）

```cpp
/*
序列容器：vector,list,deque,stack,queue,heap,priority_queue,slist.

关联容器：set，map，multiset,multimap底层机制都是以RB-tree完成的。hash_set,hash_map,hash_multiset,hash_multimap的底层机制是hashtabel。
*/
```
- IO Library
- Dynamic Memory
- 
