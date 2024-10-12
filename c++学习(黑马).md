# c++学习和使用技巧

谨以此文，记录我的22岁 ——2023年6月

# 第一阶段



## 1.字符串string

1.使用string时，需要引用头文件

```
#include<string>
```

2.不能直接打印字符串，需要增加.c_str()，例如

```c++
printf（%s，str） ------ printf（%s，str.c_str()）
```

3.想在字符串的引号内保留引号，需要用到转义字符，例如希望`a 的值为"hello"`，用`string a = "\"hello\""`

```c++
std::string a = "\"hello\"";
std::cout << a << std::endl; // 输出："hello"
```



## 2.结构体struct

1.struct定义和函数不同，需要在花括号外边写；号

2.结构体的调用时可以省略关键词struct，直接按照类似int a的方式，例如student s1

3.结构体作为函数参数时，传递方式有值传递和地址传递；通常用地址传递，占用内参只有地址的4个字节。防止误操作，加入const固定

​		值传递：形参改变，实参不会发生变化。相当于拷贝一份实参的副本

​		地址传递：形参改变，实参跟着变化。

4.struct和class一样，可以有==属性变量==和==成员函数==，在struct结构体内也可以==函数==存在。用法与class类一模一样

5.结构体实例化后的成员，也可以用取地址、指针接收的方式。但是要使用->符号代替.运算符

```c++
struct student s1
    s1.name = "zhangsan"
struct student *p = &s1
	p->name
```



## 3.一维数组作为参数传入函数

和普通的值传递有区别，数组是地址传递[(101条消息) c++中数组作为参数传入函数_c++数组作为参数传入函数_啤酒我可以喝一件的博客-CSDN博客](https://blog.csdn.net/zouxu634866/article/details/89318769)

```c++
void printinfo(int people[])或者(int *people)
{
    int main(){
    int people[3]={0,1,2};
    printinfo(people); //people并不是数组，而是第一个元素的地址，使用时只能将数组名作为参数
}
```

1.数组名就是首个元素的地址，上述printinfo实际上传的不是数组，而是首个元素的地址

2.数组传入函数时只能将数组名作为参数(***只能传入people***)，也就是只能传入首地址

3.函数定义时的形参有两种定义方式

```c++
void printinfo(int people[] , int len)或者void printinfo(int *people , int len)
```



## 4.指针

1.int*是一种数据类型，类似int，名称叫做指针。&为地址，指针存储的就是地址。

```c++
int* p = &a  //p的值为0x0011等，是一个具体的地址
```

2.*p是解引用的操作，因为p是地址，解引用相当于直接获取该地址存储的值，就可以彻底改变该地址的数据，可以形参修饰实参

3.指针常量（int* cost）：指针的指向是固定的（不可修改），指针指向的值可以修改

**二级指针：**

```c++
worker** worker_array = new worker*[5] //创建了5个work*的指针数组
//worker_array可以理解成：等号右边创建了一个指针数组，用worker_array来指向这个指针数组的初始地址
//可以用索引的方式找到特定的一级指针，例如worker_array[1]->name，可以直接访问2号工作人员的名字
```



## 5.内存四区

==程序运行前：==

**代码区**：

用来存放二进制文件，特点是具有**共享性**和**只读性**，保证多次点开exe文件时重复利用一个区域的代码，不用在开辟新的内存空间

**全局区：**

存放全局变量、静态变量（static）、常量（全局常量和字符串常量、局部常量不在该区域中）

==程序运行后：==

**栈区：**

内存的释放由编译器决定。注意事项：不要在函数中返回局部变量的地址，因为函数调用结束后，编译器会自动释放占用的内存空间，地址就不再是变量的地址了

**堆区**：

内存的释放由程序员决定，可以利用new关键词在函数中将局部变量的地址传递回来。

一个new对应一个delete，不可以多个new用一个delete来删除

返回值是指针类型：`new 数据类型`返回值为 `数据类型 *`，例如`new int`返回值为 `int *`





## 6.引用

1.**定义：**

相当于给变量另起一个名字，使得不同的变量名可以访问同一块内存地址。赋值相当于将相同的值给予不用的内存地址。二者不一样

**2.注意事项：**

​		**引用必须初始化**，必须有要改名的变量。不能直接int &b ， 而是要先定义int a ，然后int &b = a；

​		**引用一旦完成初始化，就不能再更改引用其他变量**。int &b = a后不能再操作int &b = c；

​		**引用的数据发生变化，原始数据也会一起变化**。二者同生同死

​		值返回是相当于拷贝构造函数，复制该成员，然后创建了一个新的对象。引用只会返回自身的对象

**3.本质**

引用的本质是指针常量。编译器会自动修改。int & ref = a 编译器改写为：ref =20编译器改修为：

```c++
int* const ref = &a ； *ref = 20
```

**4.常量引用的值不能再改变**，const int & ref = a表示ref的值不可以再改变，防止函数传递中形参改变实参

```c++
void showvalue(const int & a)  
{
    a = 100000; //会报错，无法修改a的值，因为函数是用常量引用接收的实参，不可以再改变引用的数据
    cout << "a="<< a <<endl;
}
```

**5.返回值为引用：**

如果希望函数返回值是可以修改的左值，就需要返回该数据类型的引用，例如在运算符重载a=b=c中，=必须是可修改的左值，因此需要返回引用

```c++
myclass operator=(const myclass & c)
{
    ......;
    return *this;
}
```



## 7.函数高级

**1.参数传递**：

值传递（形参不改变实参），地址传递（形参改变实参），引用传递（形参改变实参）

**2.函数默认参数：**

形参中可以进行赋值，例如。同样函数也可以继续赋值，以输入的实参为准

```c++
int value(int a ,int b = 20,int c = 30)
{
    return a+b+c
}
int main()
{
	value(10,40,50)
}
```

**注意事项：**

​		第一个默认参数后，从左往右的形参都需要有默认参数

​		函数的声明和实现只能有一个默认参数

**3.占位参数：**

`返回值类型 函数名（数据类型< **无具体的形参** >）{}`

**4.函数重载：**

允许使用==相同名称==的函数，来提高复用率。函数形参列表中包括（类型不同，个数不同，顺序不同），但是返回值==需要相同==



## **8.随机数：**

需要rand()随机数函数和srand()随机数种子一起使用

**1.rand语法**：`rand() % max_num`，随机产生0~max_num之间的整数，不包括max_num

`rand() % 100`       //随机生成0~99之间的数字

`rand() % n + m `   //随机生成m~(n+m-1)之间的数字

**2.srand()用法**

需要包含头文件`#include<ctime>`，之后在main函数处输入`srand((unsigned)time(NULL));`生成随机数种子

## 9.++i和i++

++i表示先赋值然后自增，i++表示先自增然后赋值

若 a = i++; 则等价于 a=i;i=i+1;a的结果为0

而 b = ++i; 则等价于 i=i+1;b=i;b的结果为1

# 第二阶段

## 8.类与对象

**1.c++三要素：**

封装，继承，多态

**2.类的特征包括：**

属性（变量），行为（函数）。类中的属性与行为，统一称为==成员==

|      | 属性     | 行为     |
| ---- | -------- | -------- |
| 别名 | 成员属性 | 成员函数 |
|      | 成员变量 | 成员方法 |

​		基本格式如下：

```c++
class student  //class关键词+定义的类名
{
public: //添加权限
    
    //属性
    int age  ……//一大堆的变量
        
    //行为
    void showname()  //一大堆的函数
    {
        cout <<…… <<endl;
    }
};
```

**3.访问权限：**

（不写的情况下，默认权限为==私有权限==，struct默认权限为==公共权限==）：

| 公共权限public        | 类内可以访问  类外可以访问                                   |
| --------------------- | ------------------------------------------------------------ |
| **保护权限**protected | **类内可以访问  类外不可以访问（继承中 子可以继承父的保护内容）** |
| **私有权限**private   | **类内可以访问  类外不可以访问（继承中 子不可以可以继承父的私有内容）** |

**4.使用技巧：**

类内成员函数的形参可以是其他类的成员，二者比较的程序就可以写成只传递一个其他类进来

类内成员变量也可以是其他类的成员，意思是在一个类内，可以让另一个类作为成员

**5.匿名对象**

创建并使用了对象，但没有为该对象分配一个明确的名称

```c++
class people
{
public:
    people(int val)
    {
        cout << "value: " << val <<endl；
    }
}；
    
int main() {
    int sum = people(10);}//并没有实例化对象
```

**6.初始化赋值**

==不要和继承搞混==

```c++
class Person {	
	int m_A;
	int m_B;
	int m_C;
	//传统方式初始化
	Person(int a, int b, int c) {
		m_A = a;
		m_B = b;
		m_C = c;}

	//初始化列表方式初始化
	Person(int a, int b, int c) : m_A(a), m_B(b), m_C(c) {}
```



## 9.多文件编写：

**1..h文件是头文件：**	

里面写类的==声明==，不写定义，包括类内成员变量和成员函数，函数不写内容，只做声明部分

注意事项：首行需要添加一行代码，避免头文件之间多次调用

```c++
#pragma once
```

**2..cpp文件是源文件：**

里面只用写成员函数的==定义==，注意的点是函数名称前需要添加==作用域==，不用写class类、不用设置权限

注意事项：引用自定义的.h文件时，需要用" "与标准库函数中的< >区分

```c++
#include "circle.h"  //与<iostream>区分
```

**3.注意事项：**

1.声明文件中写类的定义，按照常规类的写法，例如

```c++
#pragma once
class people
{
public:
    string name;
    int age;
    people(int age,string name); //构造函数
    void show_info();
};
```

2.实现文件中==不要再写成类的写法==了，否则会报错类重复定义，应该改成以下这种：

```c++
#include "people.h"

people::people(int age,string name)
{
    this->age = age;
    this->name = name;
}

void people::show_info()
{
    cout<<"年龄为："<<age<<"姓名为"<<name<<endl;
}
```

**4.如何进行多文件编写**

比如说src文件夹中有2个cpp文件（A.cpp和B.cpp），但是只有A.cpp文件中有main函数，想在这个main函数的cpp文件中调用B.cpp文件的函数，此时就需要对B.cpp进行多文件的编写，将B.cpp中所有的函数声明写成B.h的形式，然后将B.cpp改写成函数的实现。在A.cpp调用时，只需要引入B.h的库文件即可调用B.cpp中的函数

**5.编译器改正：**

vscode编译器：

tasks.josn文件中下修改，表示编译==文件夹==所有cpp文件

```c++
“args”:[
    "${file}"                //单个文件编译，只适用于cpp文件中不牵扯其他cpp文件的单个文件
	"${fileDirname}\\*.cpp"  //多个文件编译，适用于同时编译声明和定义分开的多个cpp文件
```



## 10.析构函数

作用：主要作用在于对象**销毁前**系统自动调用，执行一些清理工作

编译器：如果**不提供构造和析构，编译器会提供**，编译器提供的构造函数和析构函数是**空实现**

语法：**析构函数语法：** `~类名(){}`

1. 析构函数，没有返回值也不写void
2. 函数名称与类名相同,在名称前加上符号  ~
3. 析构函数不可以有参数，因此不可以发生重载
4. 程序在对象销毁前会自动调用析构，无须手动调用,而且只会调用一次

误解：析构函数在类成员释放前执行（就是函数释放前执行），并不是在定义完类成员后就释放

```c++
void test()
{
	people p1;
    //这里不会调用析构函数
	cout <<...<<endl;
	people p2;
	cout << ...<<endl;
    //在这里才会调用析构函数进行释放
}
```



## 11.构造函数

**定义**：在创建对象时为对象的成员属性赋值，构造函数由编译器自动调用，无须手动调用。

**构造函数语法：**`类名(){}`

1. 构造函数，没有返回值也不写void
2. 函数名称与类名相同
3. 构造函数可以有参数，因此可以发生重载
4. 程序在调用对象时候会自动调用构造，无须手动调用,而且只会调用一次

**分类**：

​	按参数分为： 有参构造和无参构造

​	按类型分为： 普通构造和拷贝构造



## 12.拷贝构造函数

**类内实现：**传参用常量引用的方式

```c++
class Person {
public:
	int mAge；
	Person(const Person& p) 
    {
		cout << "拷贝构造函数!" << endl;
		mAge = p.mAge;
	}
}；
```

**调用方式：**两种方法，一种直接在括号内输入，一种写等号

```c++
int main()
{
    Person p1("tom",18);//假设有有参构造函数
    Person p2(p1); //方法一：直接将p1输入到参数列表中
    Person p2 = p1 ; //方法二，用赋值符号也可以
}
```

编译器提供规则：

|  无  |     拷贝     | 有参（自己） |
| :--: | :----------: | :----------: |
|  无  | 拷贝（自己） |      无      |

**1.浅拷贝：**

简单的赋值操作，原理是赋值旧对象的指针，新旧对象共享一块地址。编译器默认提供浅拷贝

注意：浅拷贝遇见堆区数据（比如有参构造里利用new在堆区开辟了一个空间），如果两个带有delete的析构函数同时作用于一个地址		（交叉重复释放），是非法操作

总结：如果属性有在堆区开辟的，一定要自己提供拷贝构造函数，防止浅拷贝带来的问题

**2.深拷贝：**

在堆区重新申请空间，进行拷贝操作

```c++
class Person{
public:
    int * m_height;
    //拷贝构造
	Person(const Person& p)
	{
		cout << "拷贝构造函数!" << endl;
		//如果不利用深拷贝在堆区创建新内存，会导致浅拷贝带来的重复释放堆区问题
		m_age = p.m_age;
        //新开辟一块空间，就不会造成新旧成员的析构函数同时作用于同一个地址
		m_height = new int(*p.m_height);	
	}
    //析构函数
    ~people()
    {
        if (m_height!=NULL)
        {
            delete m_height;
            m_height = NULL;
        }
    }
};
```

如果利用new了多个对象，不可以用一个delete同时释放，delete后面需要跟变量名称



## 13.this指针

this指针本质上就是类实例化对象后，对象的首地址，所以this指针对成员的所有都可以访问

this指针的本质是一个指针常量 ：指针的指向不可修改，指针指向的值可以修改

return * this的返回值是类本身 

静态成员函数内不能调用this指针，是先于对象存在的



## 14.const相关

常量指针：不可改变指针的内容 `const int * a  `

指针常量：不可改变指针的指向 `int * const a`

在引用作为形参传递时，可以写成例如`void func(const int& a)`，避免引用传递改变main函数中的值

常函数：

类内成员函数在后面加上const后，成员变量在成员函数内就不可以修改：特殊关键字mutable

成员函数相当于调用this指针，this本质是指针常量，只能修改指向的值，相当于在this之前再加了一个const

常对象：

在对象前面加上const，并不是在成员属性前面

常对象只能调用常函数，因为普通成员函数可以允许修改成员属性的值



## 15.static相关

静态成员变量 特点 意义

静态成员函数 特点 意义

private的意义



## 16.友元

**1.全局函数做友元：**

​		在class建立后在public前面加上`friend+全局函数的声明,eg:friend void test(int a);`形参里面**必须是class类的对象**，如果不写形参或者形参是其他类型，就会报错，因为友元的作用就是访问类内的私有成员

**2.类做友元：**

​		在class建立后在public前面加上`friend+class 类名；`

​		注意：另一个类A做友元时，必须在该类B之前**定义或者声明**，具体函数实现可以在该类之后用作用域的方式实现

**3.成员函数做友元：**

`		friend void 类名::函数名(数据类型 形参)；eg：friend void people::test(int a);`

​		注意：另一个类A必须在该类B之前定义，A可以在后面写具体实现，但是A类内所有的**定义**必须在该类B之前



## 17.运算符重载

运算符重载也可以发生函数重载，例如：`operator+（person &p,person &p2）和 operator+(person& p1,int num)`

赋值运算符chongzai_4有问题没解决，58行改写成p2（20，“李四”，30.0），p2=p1就没问题？



## 18.继承

1.**基本语法**：父类/基类+子类/派生类

```c++
//父类
class base
{
    public:
    int dad_age;
    protected:
    private:
};

//子类
class son : public base //public/private/protected三选一作为继承方式
{
    public:
    int son_age;
}
```

2.**继承方式**：

|         public         |        private        |       protected       |
| :--------------------: | :-------------------: | :-------------------: |
| **保留父类的规则不变** |  **全部变为private**  | **全部变为protected** |
|     public->public     |    public->private    |   public->protected   |
|  protected->protected  |    public->private    | protected->protected  |
| private->**不可访问**  | private->**不可访问** | private->**不可访问** |

注意：父类中的私有成员只是被**隐藏**了，依旧会被继承到子类中

**3.构造函数和析构函数**：父类构造->子类构造->子类析构->父类析构

**4.子类和父类同名的调用**：父类成员要加**作用域**

例子：父类为base，子类为son，实例化子类成员为s ，重名的分别为age，show_func()，static_age，static_func()

|      |    成员属性     |        成员函数         |  静态属性（对象调用）  |   静态属性（类名调用）    |   静态函数（对象调用）    |     静态函数（类名调用）     |
| ---- | :-------------: | :---------------------: | :--------------------: | :-----------------------: | :-----------------------: | :--------------------------: |
| 子类 |      s.age      |      s.show_func()      |      s.static_age      |      son::static_age      |      s.static_func()      |      son::static_func()      |
| 父类 | s.**base**::age | s.**base**::show_func() | s.**base**::static_age | son::**base**::static_age | s.**base**::static_func() | son::**base**::static_func() |

注意：`son::base::static_age`中前后两个::代表的含义不同。第一个表示用**类名调用**的意思，第二个是作用域的意思

5.**多继承**：`class son : public base1 , public base2`同一个子类可以继承多个父类。同名时需用不同作用域分开

6.**菱形继承的问题：** 一方面会引起最底层类的成员属性二义性，另一方面会对祖先类重复继承。

​		解决：中间层采用虚继承的方式 `class sheep : virtual public animal`

7.子类继承父类后，如果函数的形参为**父类**的引用或者指针，可以传入**子类**的实参，**反之不行**

**8.父类有有参构造函数下的继承：**

子类==必须==继承父类的==有参构造函数==，类内需要写，否则无法实例化子类对象

```c++
class people
{
public:
    int age;
    string name;
    people(int age, string name)
    {
        this->age = age;
        this->name = name;
    }
};

class son:public people
{
public:
    int son_age;
    son(int age_par,string name_par,int age_son):people(age_par,name_par),son_age(age_son) //注意这里的people的写法，好像只能这样写有参构造函数赋值，前两个变量都是父类的
    {
        cout<<"age_par"<<age_par<<endl;
        cout<<"name_par"<<name_par<<endl;
        cout<<"son_age"<<age_son<<endl;
    }
};

int main()
{
    people p1(18,"tom");
    son s1(18,"tom",23);
    return 0;
}
```



## 19.多态

1类内成员函数在哪存储，类内虚函数在哪存储，修改子类可以改变父类吗

如果是空的类，sizeof为1

2.**多态的使用**：

​			步骤1：父类改写成虚函数，

​			步骤2：子类重写虚函数（先需要继承）

​			步骤3：父类的指针或者==引用？==接收子类的地址

​			示例：

```c++
class father
{
public:
    virtual void show() //改写成虚函数
    {
        cout<<"判断是否可以实例化"<<endl; //当然也可以写成纯虚函数
    }
};

class son : public father
{
    virtual void show() //重写虚函数
    {
        cout<<"son的输出"<<endl;
    }
}；
int main()
{
    father * = new son //父类的指针或者引用接收子类的地址
}
```

3.**抽象类**：父类函数中没有任何函数，用=0表示，称为**纯虚函数**。

```c++
class father
{
public:
    virtual void show()=0；
};
```

抽象类无法==**实例化**==



## 20.读写文件

**流对象分类和打开方式：**

1. ofstream：写操作
2. ifstream： 读操作
3. fstream ： 读写操作

| 打开方式    | 解释                       |
| ----------- | -------------------------- |
| ios::in     | 为读文件而打开文件         |
| ios::out    | 为写文件而打开文件         |
| ios::ate    | 初始位置：文件尾           |
| ios::app    | 追加方式写文件             |
| ios::trunc  | 如果文件存在先删除，再创建 |
| ios::binary | 二进制方式                 |

如果打开方式有多个，可以用或操作运算符，例如 ios::in|ios::out

**1.读文件：**五步走

1. 包含头文件   

   \#include <fstream\>

2. 创建流对象  

   ifstream ifs;

3. 打开文件并判断文件是否打开成功

   ifs.open("文件路径",打开方式);

4. 读数据

   四种方式读取

5. 关闭文件

   ifs.close();

```c++
#include <fstream>
#include <string>
void test01()
{
	ifstream ifs;
	ifs.open("test.txt", ios::in); //可以该路径，如果不改就是默认文件所在的位置

    if (!ifs.is_open())
    {
        cout << "文件打开失败" << endl;
        return;
    }

    //第一种方式
    char buf[1024] = { 0 };
    while (ifs >> buf)
    {
    	cout << buf << endl;
    }

    //第二种
    char buf[1024] = { 0 };
    while (ifs.getline(buf,sizeof(buf)))
    {
    	cout << buf << endl;
    }

    //第三种
    string line;
    //getline会逐行遍历文件，并将每一行都存储在line变量中，line表示循环中每一行的数据
    while (getline(ifs, line)) //这里的getline第一个参数一定是输入流，不是文件名称，切记切结
    {
    	cout << line << endl;
    }

	ifs.close();
```

**2.写文件**：五步走

1. 包含头文件   

   \#include <fstream\>

2. 创建流对象  

   ofstream ofs;

3. 打开文件

   ofs.open("文件路径",打开方式);

4. 写数据

   ofs << "写入的数据";

5. 关闭文件

   ofs.close();

```c++
#include <fstream>

void test01()
{
	ofstream ofs;
	ofs.open("test.txt", ios::out); //可以该路径，如果不改就是默认文件所在的位置
    ofs << "姓名：张三" << endl;
    ofs << "性别：男" << endl;
    ofs << "年龄：18" << endl;
    ofs.close();
}
int main() {
	test01();
	system("pause");
	return 0;
}
```

**3.判断文件中是否有数据：**读取文件后，文件流利用eof函数

```c++
char h
ifs>>h;
if(ifs.eof())
{
    cout<<"文件为空"<<endl;
}
```

**4.读取每一行中的数据：**

```c++
while(ifs >>id && ifs >> name && ifs >> did)
```

1. 从输入流 `ifs` 中尝试提取一个整数，并将其赋值给变量 `id`。
2. 如果提取成功，则继续尝试从输入流中提取一个字符串，并将其赋值给变量 `name`。
3. 如果第二次提取也成功，则继续尝试从输入流中提取一个整数，并将其赋值给变量 `did`。
4. 如果以上所有的提取操作都成功，表达式的结果为真（`true`），进入 `while` 循环体。否则，循环结束。



## 21.new相关

**定义：**new用来在堆区手动开辟一块内存，需要注意的是返回值是对应类型的指针

​			需要delete手动释放开辟的内存空间

**区分**：区分（）和[ ]两种方式

```c++
int* a = new int(5)  //表示新开辟了一个单独的int空间，初始化数据为5
int* b = new int[5]  //表示新开辟了一个包含5个整形的数组，无初始化
    //如果需要初始化，可以写成int* b = new int[5]{1,2,3,4,5}
worker** c = new worker*[5]  
    //c[0]代表在这个指针数组中第一个元素的指针，注意为一级指针，不是二级指针，可以用c[0]->name直接访问人员姓名
```

**释放**：指针数组释放需要加上[]，指针数组也可以单个释放

```c++
delete a;  //正常释放即可
delete[] a;  //表示释放的是一个开辟的数据
delete a[2]; //只释放a指针数组中的第3个成员的指针，也可以后面重新赋值
```

**new数组应用**：一般来说new数组是为了动态管理数组的大小，比vector稍微快一些

```c++
// 动态添加元素
int *arr = new int[5];
for (int i = 0; i < 5; ++i) {
    arr[i] = i;
}
// 在运行时添加更多的元素
int newSize = 10;
int *newArr = new int[newSize];
for (int i = 0; i < 5; ++i) {
    newArr[i] = arr[i];
}
delete[] arr;  // 释放原数组的内存
arr = newArr;  // 更新指针
```

## 22.数组指针

当使用指针来维护数组时，指针的索引返回值是对应的数组中元素的数据类型，而不是指向该类型的指针

```c++
int arr[5] = {1, 2, 3, 4, 5};
int *ptr = arr; // 指向数组的第一个元素
ptr[0]返回的数据类型是int，而不是int*
```

例如，ptr[0]返回的数据类型==**是int，而不是int***==

# 第三阶段

## 1.模板函数

**概念：**模板的作用是提供一个通用的函数，可以先不指定形参和返回值的类型，在使用时编辑器自动推导，一个函数解决所有

**语法：**声明，使用(自动类型推导和显示指定类型)

```c++
template<typename T> //告诉编译器T是一个模板,里面也可以换成class，注意没有分号，一个模板函数之前得写一次
T func(T a , T b)
{
    //...具体功能的实现
}

int main()
{
    int a = 1;int b-2;
    func(a,b);     //自动类型推导
    func<int>(a,b) //显示指定类型
}
```

**调用规则：**

1. 如果模板函数和普通函数均存在，优先普通函数
2. 可以通过空模板参数列表来强制调用函数模板，例如 func<>(a,b)
3. 函数模板也可以发生重载
4. 函数模板比普通函数的形参输入更适合，就优先调用函数模板（普通函数隐式转化的情况，根据ASCII表把char->int）

**具体化（自定义类型）重载示例：**

```c++
//正常写一个标准的重载
template<> 返回值 函数名<具体的类 a ， 具体的类 b>
{}
```



## 2.类模板

**1.语法：**使用时==没有==自动类型推导，只有显示指定类型，==**任何用到这个类的地方，类名称后面都需要加上参数列表<T1/int ...>等**==

```c++
template<class int_type,class str_type> //注意没有分号，尖括号内可以写多个类型，类内成员变量有多少种数据类型，就可以写多少个
class people
{
    int_type age;
    str_type name;
    people(int_type age,str_type name)
    {
        this->age = age;
        this->name = name;
    }
};
void func()
{
    //无法自动类型推导
    people<int,string> p1("tom",999); //调用时，指定模板的数据类型，与最上面模板定义相对应
}
```

**2.区别：**相较模板函数，有以下区别

1. 类模板没有自动类型推导的方式
2. 类模板的参数列表（尖括号内）可以有默认参数，使用时显示指定类型该参数可以省略，例如上例中template<class int_type  **=  int** ,class str_type>，使用时 people<string> p1("tom",999)

**3.作为参数传入函数：**

三种方式：大多数都用第一种

1.传入指定的类型，和实例化成员写的类型完全一样   2.类模板的参数形成新的模板传入    3.整个类形成新的模板传入

```c++
1.void test01(people<int,string>p1);
2.template<typename T1,typename T2>
  void test02(people<T1,T2>p1);
3.template<typename T>
  void test03(T p1)
```

**4.继承：**如果父类是一个类模板，子类继承有两种方式，要么继承时候规定父类的模板参数列表类型，要么子类也写成一个模板

要注意在父类有有参构造函数时，子类是类模板时候的继承语法

```c++
#include <iostream>
using namespace std;
#include<string>

template<class T1,class T2>
class people
{
    public:
    T1 age;
    T2 name;
    people(T1 age, T2 name)
    {
        this->age = age;
        this->name = name;
        cout<<"父类name: "<<name<<endl;
    }
};

template <class T1,class T2,class T3>
class son:public people<T1,T2>
{
public:
    //这里可以参考继承部分的有参构造函数如何继承，不同的是，注意people父类后面需要使用显示类型转化，需要在类模板参数列表中写入定义的T1和T2
    son(T1 age_par,T2 name_par,T3 name_child):people<T1,T2>(age_par,name_par),m(name_child){}
    T3 m;
    void show()
    {
        cout<<"T1数据类型: "<<typeid(T1).name()<<endl;
        cout<<"T2数据类型: "<<typeid(T2).name()<<endl;
        cout<<"T3数据类型: "<<typeid(T3).name()<<endl;
    }
};

int main()
{
    son<int,string,char> s1(18,"tom",'Q');
    s1.show();
    return 0;
}
```

**5.多文件编写**

问题：类模板中成员函数的创建时机不是在开始时创建，而是**在调用时候**才会创建，普通的类成员函数在实例化后就创建了，因此多文件编写不能和传统的方法一样

解决方法：将类模板的声明和实现写到同一个头文件中，后缀改为==**.hpp**==，约定俗成只要看到==**.hpp**==，就认为是一个类模板

调用方法：在主文件中#include "people.hpp"

**6.友元：**

类内实现：直接在类内写friend+全局函数，注意函数形参为class类的实例化对象

类外实现：类内声明，类外实现。在类内写声明时，需要注意写成模板函数的形式，在函数名后加<>告诉编译器这是一个模板函数，类外实现时定义函数为模板函数，并在最前面声明存在类模板。

```c++
#include <iostream>
using namespace std;
#include<string>
//告诉编译器有这么一个类模板
template <class T1,class T2>
class people;
//类外实现的模板函数
template <class T1, class T2>
void show2(people<T1,T2> p)
{
    cout<<"全局函数的类外实现: "<<endl;
    cout<<"姓名: "<<p.name<<" 年龄： "<<p.age<<endl;
}

template<class T1,class T2>
class people
{
//全局函数类内实现，声明+实现
friend void show1(people<T1,T2> p) //友元传递的形参一般就是类实例化的对象
    {
        cout<<"全局函数的类内实现: "<<endl;
        cout<<"姓名: "<<p.name<<" 年龄： "<<p.age<<endl;
    }
//全局函数类外实现，声明
friend void show2<>(people<T1,T2> p);//注意这里一定要在函数名称后面加<>，因为在外部实现时，有T1和T2的存在，需要写成模板函数，所以在声明时也需要告诉编译器，这个函数就是一个模板函数，加上<>空的模板参数列表就可以

public:
    people(T1 age, T2 name)
    {
        this->age = age;
        this->name = name;
        cout<<"父类name: "<<name<<endl;
    }
private:
    T1 age;
    T2 name;
};
```

# 第四阶段：STL和容器

## 1.总体简介：

STL六大组件：容器、算法、迭代器、仿函数、适配器、空间配置器

1. 容器：各种数据结构，如vector、list、deque、set、map等,用来存放数据。
2. 算法：各种常用的算法，如sort、find、copy、for_each等
3. 迭代器：扮演了容器与算法之间的胶合剂。也称为函数对象
4. 仿函数：行为类似函数，可作为算法的某种策略。
5. 适配器：一种用来修饰容器或者仿函数或迭代器接口的东西。
6. 空间配置器：负责空间的配置与管理。



## string容器：

1. 赋值操作：`=或者成员函数assign("aaa")`
2. 拼接操作：`(str3 = str1 + str2)或者成员函数append("aaa")`，注意**不能直接用两个字符串"ab"+"cd"的方式**，可以将ab和cd分别赋值给str1和str2再用**+**拼接
3. 查找操作：`成员函数find(字符或字符串)--第一次出现的索引；rfind(字符或字符串)--最后一次出现的索引`，从0开始计数，**不存在返回-1**
4. 替换操作：`成员函数replace(int 开始替换的位置, int 想替换的字符长度, 需要替换的新的字符串)`
5. 比较操作：`成员函数compare(str2)`,ASCII相等返回0，大于返回1，小于返回-1，通常用于比较两个字符串是否相等(也可用=比较)
6. 获取大小：`成员函数size()`，返回int
7. 存取操作：`str[i]` 取：访问字符串中的第`i`个字符，存：可以用`str[i] = a`改变字符第`i`个位置的字符，位置索引是从0开始
8. 插入操作：`成员函数insert(int 指定的位置，需要插入的字符串)`，位置索引是从0开始
9. 删除操作：`成员函数erase(int 指定的位置，int删除的字符长度)`，位置索引是从0开始
10. 从字符串中获取子串：`成员函数substr(int 开始截取的位置，int 截取长度)`，位置索引是从0开始，返回值是string

## **2.单端双端容器**

## vector容器：

==单端容器，尾部容器，前端封口。支持随机访问迭代器==

**迭代器的类型定义可以简化用`auto`自动推导**

<img src="picture\clip_image002-17098024178971.jpg">

**1.迭代器：**`vector<数据类型，内置或者自定义>::iterator`，遍历容器可以用迭代器的方式

```c++
#include<vector>
//迭代器遍历vector元素，采用引用方式传递参数，在函数内部可以修改vector的元素
void bianli(vector<int>& v)
{
    for(vector<int>::iterator it = v.begin();it!=v.end();it++)
    {
        cout<<*it<<" ";
        //如果vector中存放的是自定义的类，那么访问时候某一个元素的某一个属性时，需要在*it外面加上（），操作符有先后顺序
        //cout<<(*it).name<<" "<<(*it).name<<endl;
        cout<<endl;
    }
}
//如果不想修改，采用const关键字引用的方式，此时迭代器要换成const_iterator
void bianli_const(const vector<int>& v)
{
    for(vector<int>::const_iterator it = v.begin();it!=v.end();it++)
    {
        cout<<*it<<" ";
        cout<<endl;
    }
}
```

1. 迭代器支持的成员函数`begin(),end(),rbegin(),rend()`,**注意，这几个成员函数返回值类似指针，需要进行解引用才能用**。vector的迭代器支持随机访问，意味着其他位置的迭代器可以用**`vector<int>::iterator it = v.begin(); it++   这种类型`**的形式

   <img src="picture\image-20240227112959363.png">

   ```c++
   //更改迭代器的位置
   void erasevector(vector<int>& v) 
   {
       vector<int>::iterator it = v.begin();
       it=it+3;
       v.erase(v.begin()++);
       printvector(v);
   }
   ```

2. 迭代器的使用可以看做是**指针**，迭代器解引用后的数据类型就是vector模板参数列表<>内的数据类型，可以采用**解引用**的方式或者**是->**的方式调用

3. 内置的函数`v.begin()`访问的是容器中起始元素的指针，`v.end()`访问的是容器中最后一个元素的**下一个指针**，并不是最后一个指针

4. 容器参数列表<>中可以存放各种类型的数据，包括内置的、自定义的、甚至还能存放嵌套的容器，例如`vector< vector<int> >`

5. vector容器在申请更多内存的同时(例如reserve)，容器中的所有元素会被复制或移动到新的内存地址，这会导致之前创建的迭代器失效，需要移动到新的内存地址后，再次创建新的迭代器。。。其实也用不到用变量接收begin和end的值，直接用`v.begin()`不会存在这个问题

```c++
#include <iostream>
#include <vector>
using namespace std;
int main()
{
    vector<int>values{1,2,3};
    cout << "values 容器首个元素的地址：" << values.data() << endl;
    auto first = values.begin();
    auto end = values.end();
    //增加 values 的容量
    values.reserve(20); 
    cout << "values 容器首个元素的地址：" << values.data() << endl;
    //错误写法
    while (first != end) {
        cout << *first;
        ++first;
    }
    //正确写法，需要重新
    auto first = values.begin();
    auto end = values.end();
    while (first != end) {
        cout << *first;
        ++first;
    }
    return 0;
}
```

**2.常用内置成员函数：**

- 构造函数： 默认构造、区间构造、拷贝构造`vector<int> v2(v1)`，初始化时可以直接赋值`vector<int> v1={1,2,3,4,5,6}`
- 赋值函数：`=`或者`assign(v.begin(),v.end())`
- `empty(); `                         //判断容器是否为空
- `capacity();`                    //容器的容量，一般>容器中元素个数
- `size();`                           //返回容器中元素的个数
- `resize(int num);`           //重新指定容器的长度为num，若容器变长，默认值填充新位置。容器变短，超出容器长度的元素被删除。
- `resize(int num, elem);`  //重新指定容器的长度为num，若容器变长，则以elem值填充新位置。
- `push_back(ele);`                                                          //尾部插入元素ele
- `pop_back();`                                                                 //删除最后一个元素
- `insert(const_iterator pos, ele);`                         //==迭代器指向位置pos插入元素ele==，**const_iterator是begin等，可以it++的形式更改迭代器的位置，不一定在起始或者终止位置上，详见上面的代码块**
- `insert(const_iterator pos, int count,ele); `            //迭代器指向位置pos插入count个元素ele
- `erase(const_iterator pos);`                                       //删除迭代器指向的元素
- `erase(const_iterator start, const_iterator end);  ` //删除迭代器从start到end之间的元素
- `clear();`                                                                       //删除容器中所有元素
- `operator[]; `       //返回索引idx所指的数据
- `front(); `            //返回容器中第一个数据元素
- `back();`              //返回容器中最后一个数据元素
- `swap(vec);`        // 将vec与本身的元素互换
- ==`reserve(int)`;    //给容器预留空间，如果一开始知道容量的话，预留空间可以节省空间。可以增加容量，但是不可以减少容量==

**3. swap巧用:**

​	`vector<int>(v1).swap(v2)`：第一个（）内表示创建一个匿名对象，该匿名对象的大小和容量来自于v1，然后该匿名对象和v2交换大小和容量，v2此时就相当于有了v1的容量和大小，然后编译器执行完这句话后，会自动释放掉匿名对象。这样的好处是假设v1是一个小的容器，v2是一个容量大但是size小的容器（有很多空间没用），就可以用这个方法收缩空间，v2会变成和v1一样大。当然可以自身和自身收缩

```c++
void test02()
{
	vector<int> v;
	for (int i = 0; i < 100000; i++) {
		v.push_back(i);
	}
	cout << "v的容量为：" << v.capacity() << endl;
	cout << "v的大小为：" << v.size() << endl;

	v.resize(3);//这一步只能搜索size，无法改变capacity，即便是用上reserve也没法改变capacity
	cout << "v的容量为：" << v.capacity() << endl;
	cout << "v的大小为：" << v.size() << endl;

	//收缩内存
	vector<int>(v).swap(v); //匿名对象
	cout << "v的容量为：" << v.capacity() << endl;
	cout << "v的大小为：" << v.size() << endl;
}
```



## deque容器

==双端容器，支持随机访问迭代器==

<img src="picture\clip_image002-1547547642923.jpg">

**1.区别**：在前端插入数据很快，双端容器，前后都可以插入。

​			没有`capacity()`的容量函数，

​		  `vector`成员函数只有`push_back()`，而`deque`具有`push_front()`

**2.常用内置成员函数：**

- 构造函数： 默认构造、区间构造、拷贝构造`vector<int> v2(v1)`
- 赋值函数：`=`或者`assign(v.begin(),v.end())`
- `empty(); `                         //判断容器是否为空
- `size();`                           //返回容器中元素的个数
- `resize(int num);`           //重新指定容器的长度为num，若容器变长，默认值填充新位置。容器变短，超出容器长度的元素被删除。
- `resize(int num, elem);`  //重新指定容器的长度为num，若容器变长，则以elem值填充新位置。
- ==`push_front(ele);`                                                        //头部插入元素ele==
- `push_back(ele);`                                                          //尾部插入元素ele
- ==`pop_front();`                                                                 //删除第一个元素==
- `pop_back();`                                                                 //删除最后一个元素
- `insert(const_iterator pos, ele);`                             //迭代器指向位置pos插入元素ele，**const_iterator是begin等，可以it++的形式更改迭代器的位置，不一定在起始或者终止位置上，详见上面的代码块**
- `insert(const_iterator pos, int count,ele); `            //迭代器指向位置pos插入count个元素ele
- `erase(const_iterator pos);`                                       //删除迭代器指向的元素
- `erase(const_iterator start, const_iterator end);  ` //删除迭代器从start到end之间的元素，迭代器均可
- `clear();`                                                                       //删除容器中所有元素
- `operator[]; `       //返回索引idx所指的数据
- `front(); `            //返回容器中第一个数据元素
- `back();`              //返回容器中最后一个数据元素
- 全局函数`sort(d.begin(),d.end())`，将deque容器中的数据按照从小到大的顺序排列，改变了容器内部排列顺序，**并不是成员函数**



所有不支持随机访问的迭代器，不支持标准算法

不支持随机访问迭代器的容器，往往内部支持成员函数算法

## **3.栈和队列**

==**没有迭代器，只能访问固定元素**==，无法遍历，无迭代器

## stack容器

==**单端栈，先进后出，不支持迭代器**==，只有头部数据才能被访问到，其他位置无法访问，除非依次弹出。压入数据叫做入栈，弹出数据叫做出栈

<img src="picture\clip_image002-1547604555425.jpg">

**1.内置函数接口**

- 构造函数只有两种，默认构造（模板类实现），拷贝构造
- 赋值操作 =
- `push(elem);`       //向**栈顶**添加元素
- `pop();`                //从**栈顶**移除第一个元素
- ==`top(); `                //返回栈顶元素==
- `empty();`            //判断堆栈是否为空
- `size(); `              //返回栈的大小



## **queue容器**

==**队列，先进先出，不支持迭代器**==，只支持访问头部和尾部元素，，只有队头和队尾才可以被外界使用，因此队列不允许有遍历行为

<img src="picture\clip_image002-1547606475892.jpg">

**1.常用接口**

- 构造函数只有两种，默认构造（模板类实现），拷贝构造
- 赋值操作 =
- `push(elem);`                             //往队尾添加元素
- `pop();`                                      //从队头移除第一个元素
- `back();`                                    //返回最后一个元素
- `front(); `                                  //返回第一个元素
- `empty();`            //判断堆栈是否为空
- `size(); `              //返回栈的大小

## **4.链表**

## list容器

==**链表容器，不支持随机访问迭代器，只支持双向迭代器**==，迭代器只能`it++`，不能`it=it+2`

本质：**双向循环**链表。链表的组成：链表由一系列**结点**组成，结点的组成：一个是存储数据元素的**数据域**，另一个是存储下一个结点数据地址的**指针域**。

和普通的链表有两点不一样

1. **双向**：第二个结点的指针域不仅指向第三个结点的地址，反过来还指向第一个结点的地址，这样的好处是迭代器可以实现++和--的两种操作，迭代器是双向迭代器，不支持随机访问，只可以前移和后移，不能跳跃式访问
2. **循环**：首尾相连，尾部的指针域指向头部结点的存储位置，头部结点的指针域指向尾部结点的存储位置

<img src="picture\clip_image002-1547608564071.jpg">

list的优点：

* 采用动态存储分配，不会造成内存浪费和溢出
* 链表执行插入和删除操作十分方便，修改指针即可，不需要移动大量元素

list的缺点：

* 链表灵活，但是空间(指针域) 和 时间（遍历）额外耗费较大

**2.常用接口**：

* `size(); `                             //返回容器中元素的个数
* `empty(); `                           //判断容器是否为空
* `resize(num);`                   //重新指定容器的长度为num，若容器变长，则以默认值填充新位置，如果容器变短，则末尾超出容器长度的元素被删除。
* `resize(num, elem); `       //重新指定容器的长度为num，若容器变长，则以elem值填充新位置，如果容器变短，则末尾超出容器长度的元素被删除。
* `push_back(elem)`;          //在容器尾部加入一个元素
* `pop_back()`;                          //删除容器中最后一个元素
* ==`push_front(elem)`;               //在容器开头插入一个元素== 和vector的区别
* ==`pop_front()`;                        //从容器开头移除第一个元素==
* ==`insert(pos,elem)`==;                //**pos是一个迭代器**，在pos位置插elem元素的拷贝，返回新数据的位置。
* `insert(pos,n,elem)`;            //在pos位置插入n个elem数据，无返回值。
* `insert(pos,beg,end)`;          //在pos位置插入[beg,end)区间的数据，无返回值。
* `clear()`;                               //移除容器的所有数据
* `erase(beg,end)`;                   //删除[beg,end)区间的数据，返回下一个数据的位置。
* `erase(pos)`;                          //删除pos位置的数据，返回下一个数据的位置。**pos是迭代器**
* ==`remove(elem)`;                      //删除容器中所有与elem值匹配的元素。==，vector没有，另外传递的参数并不是迭代器，是直接的数字
* `front();`        //返回第一个元素。
* `back();`         //返回最后一个元素。
* ==**没有[ ]索引**==：list存储空间不连续，不支持方括号索引
* **特殊操作**
* `reverse();`   //反转链表
* `sort();`        //链表排序，并不是标准算法，而是list内置的成员函数，采用l.sort()的方式调用

自定义的类型，用到排序算法时，需要自己写一个仿函数

```c++
people类，类内有姓名和年龄
bool compare(people p1,people p2)
{
    return p1.age>p2.age //如果p1.age>p2.age，返回真
}
int main()
{
    list<people> L;
    L.sort(compare) //compare是自己写的仿函数，从大到小排序
}
```



## **5.排序容器**

## set/multiset容器

**1.特点**：

- `set`容器插入数据时，会自动根据数据的大小排序，按照从小到大插入
- `set`容器插入重复值不算，只会记录一个；`multiset`可以插入重复值，使用时只用包含`#include <set>`即可

**2.接口：**

- 构造函数：默认构造和有参构造

- `size();`          //返回容器中元素的数目

- `empty();`        //判断容器是否为空

- `swap(st);`      //交换两个集合容器

- `insert(elem);`           //在容器中插入元素，和其他的容器不一样，没有==**push**==的操作

- `clear();`                    //清除所有元素

- `erase(pos);`              //删除pos迭代器所指的元素，返回下一个元素的迭代器。

- `erase(beg, end);`     //删除区间[beg,end)的所有元素 ，返回下一个元素的迭代器。

- `erase(elem);`            //删除容器中值为elem的元素。

- ==独有属性==

- `find(key);`                  //查找key是否存在，k是一个具体数值（自定义的数据不好使），若存在，返回该键的元素的**迭代器**；若不存在，返回set.end();可以通                  

  ​                                       过返回的迭代器计数，得到元素的位置索引index

- `count(key);`                //`set`中统计key的元素个数，要么是1，要么是0，可以用来判断容器中是否有这个数据，`multiset`可以统计出多个数据

- ==对组`pair`创建方式==

- `pair<type, type> p ( value1, value2 );` //尖括号里面的两个数据类型可以不相同

- `pair<type, type> p = make_pair( value1, value2 );`

**3.改变默认排序顺序**

原理：利用仿函数(仿函数本质上是一个数据类型，是一个类)进行修改排序准则，在set容器插入数据之后，就不能修改了，因此需要在插入函数`insert()`上动手脚，利用仿函数重载`()`运算符，让插入数据的时候，可以按照自己的期望来。

基本数据类型：

```c++
//仿函数本质上是一个数据类型，是一个类
class fanghanshu
{
public：
    bool operator()(type a , type b)//type是自定义的数据或者基本数据类型
    {
        return a > b
    }
};

int main()
{
    set<int> s; //可以直接规定类型，不规定插入时候的规则
    set<int,fanghanshu> s; //如果要定义插入时的规则，那在定义时候要多在尖括号里多加上定义的仿函数
    for(s<int,fanghanshu>::iterator it =s.begin() ; it!=s.end(); it++)
}
```

自定义数据插入，只有规定了比较的仿函数，才能插入

```c++
class fanghanshu
{
public:
    bool operator()(const people &p1,const people &p2)
    {
        if(p1.age==p2.age) //先按照年龄排序，如果相同按照体重排序
        {
            return p1.weight>weight;
        }
        else
        {
            return p1.age>p2.age;
        }
    }
};

int main()
{
    set<people,fanghanshu> s;
}
```



## map/multimap容器

**特点**：

- 容器元素都是对组pair
- 插入值是key和value
- 根据key自动排序，数字类型默认按照从小到大排序，字符串默认按照首字母的ASCII排序
- `map`的key键值不能相同；`multimap`的key键值可以相同
- **迭代器的类型定义可以简化用`auto`自动推导**

**map容器接口：**

- 构造函数：默认构造+拷贝构造

- `size();`          //返回容器中元素的数目

- `empty();`        //判断容器是否为空

- `swap(st);`      //交换两个集合容器

- `insert(elem);`           //在容器中插入元素，元素==**必须是对组形式**==，三种插入方式：

     								1：`m.insert(pair<int,int>(1,10))` 2：`m.insert(make_pair<1,10>)` 3：`m[1]=10`,方括号内表示key，等号后表示value

- **insert可以存在返回值，一种是迭代器，可以用该迭代器直接索引key对应的位置，另一种返回值是pair<迭代器，是否插入成功bool>**

- ==`key的索引：[]`            //`m[2]`便可访问key为2的value，`m["a"]`便可访问key为“a”的value==

- `clear();`                    //清除所有元素

- `erase(pos);`              //删除pos迭代器所指的元素，返回下一个元素的迭代器

- `erase(beg, end);`    //删除区间[beg,end)的所有元素 ，返回下一个元素的迭代器

- ==`erase(key);`            //删除容器中值为key的元素==，可以根据key值删除元素

- `find(key);`                  //查找key是否存在,若存在，返回该键的元素的迭代器；若不存在，返回set.end()，end()都是指向最后一个元素的下一个元素

- `count(key);`                //统计key的元素个数

- 排序：自定义仿函数

```c++
void printm(map<int,int>m)
{
    for(map<int,int>::iterator it=m.begin();it!=m.end();it++)
    {
        cout<<"key: "<<(*it).first<<" value: "<<it->second<<endl;
    }
    cout<<endl;
}
//仿函数自定义排序
class fanghanshu
{
public:
    bool operator()(int a,int b)
    {
        return a>b;
    }
};
void printm(map<int,int,fanghanshu>m)
{
    for(map<int,int,fanghanshu>::iterator it=m.begin();it!=m.end();it++)
    {
        cout<<"key: "<<(*it).first<<" value: "<<it->second<<endl;
    }
    cout<<endl;
}
void test01()
{
    map<int,int> m;
    //insert插入格式
    m.insert(pair<int,int>(1,10));
    m.insert(make_pair(4,20));
    m[3]=30;
    printm(m);
    cout<<"大小为："<<m.size()<<endl;
    //empty
    if(!m.empty())
    {
        cout<<"容器不为空"<<endl;
    }
    //swap
    cout<<"交换后"<<endl;
    map<int,int>m2(m);
    m2[10]=10;
    m2.swap(m);
    printm(m);
    //erase
    m.erase(10);
    printm(m);
    //find
    map<int,int>::iterator pos = m.find(3);
    if(pos!=m.end())
    {
        cout<<"该元素的value为："<<pos->second<<endl;
    }
    else
    {
        cout<<"容器中没有这个元素"<<endl;
    }
    //count
    cout<<"key相对应的元素个数为： "<<m.count(2)<<endl;
    //倒叙
    map<int,int,fanghanshu> m3;
    m3.insert(make_pair(1,10));
    m3.insert(make_pair(2,20));
    m3.insert(make_pair(3,30));
    m3.insert(make_pair(4,40));
    m3.insert(make_pair(5,50));
    printm(m3);
    cout<<"key为三对应的value为："<<m3[3]<<endl;
}
```

**multimap接口：**

有`map`容器的所有接口，区别是可以插入相同的键值，因此在访问键值对应value时候，有些区别

- `equal_range(key)`          //返回值是一个pair类型的迭代器，表示找到同一个key值对应的所有value的元素区间，因为会自动排序，所以区间是连续起来的。										用`.first`访问起始，用`.second`访问结束

```c++
void test02()
{
    multimap<int,int> mm;
    mm.insert(pair<int,int>(10,20));
    mm.insert(pair<int,int>(10,30));
    mm.insert(pair<int,int>(10,40));
    mm.insert(pair<int,int>(10,50));
    mm.insert(pair<int,int>(10,60));
    printm(mm);
    //查找
    auto pos_all = mm.equal_range(10); //返回值是pair类型的迭代器
    //遍历同一个key值对应的所有的value值
    for(auto it=pos_all.first;it!=pos_all.second;it++)
    {
        cout<<"key=10对应的value: "<<it->second<<endl;
    }
}
```



## 6.函数对象（仿函数）

**定义**：本质是一个公共类，在类内定义函数，重载`()`运算符

有两种用处，用处一：提供给`set`和`map`插入自定义数据时使用，用处二：在标准算法里使用

- 用法一：`set<people , fanghanshu>`，直接写类名
- 用法二：`sort(v.begin() , v.end() , fanghanshu())`，第三个变量需要写仿函数对象，可以不用实例化，直接写匿名函数对象、

谓词返回值必须是bool，仿函数的呢？，有没有哪些算法的要求必须是谓词，谓词在c++中的简写是什么？vscode怎么查看算法的输入重载参数

