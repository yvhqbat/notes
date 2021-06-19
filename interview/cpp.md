c++

[TOC]
## C++ 集锦
### 1. C++面向对象的三种特性？（每条详细说说）

封装、继承、多态

- [三大特性：封装,继承,多态](https://www.cnblogs.com/1711643472qq/p/5909839.html)

三大特性是：封装,继承,多态

所谓封装：
也就是把客观事物封装成抽象的类，并且类可以把自己的数据和方法只让可信的类或者对象操作，对不可信的进行信息隐藏。封装是面向对象的特征之一，是对象和类概念的主要特性。 简单的说，一个类就是一个封装了数据以及操作这些数据的代码的逻辑实体。在一个对象内部，某些代码或某些数据可以是私有的，不能被外界访问。通过这种方式，对象对内部数据提供了不同级别的保护，以防止程序中无关的部分意外的改变或错误的使用了对象的私有部分。

所谓继承：
是指可以让某个类型的对象获得另一个类型的对象的属性的方法。它支持按级分类的概念。继承是指这样一种能力：它可以使用现有类的所有功能，并在无需重新编写原来的类的情况下对这些功能进行扩展。 通过继承创建的新类称为“子类”或“派生类”，被继承的类称为“基类”、“父类”或“超类”。继承的过程，就是从一般到特殊的过程。要实现继承，可以通过“继承”（Inheritance）和“组合”（Composition）来实现。继承概念的实现方式有二类：实现继承与接口继承。实现继承是指直接使用基类的属性和方法而无需额外编码的能力；接口继承是指仅使用属性和方法的名称、但是子类必须提供实现的能力；

所谓多态：
就是指一个类实例的相同方法在不同情形有不同表现形式。多态机制使具有不同内部结构的对象可以共享相同的外部接口。这意味着，虽然针对不同对象的具体操作不同，但通过一个公共的类，它们（那些操作）可以通过相同的方式予以调用。



### 2. 继承和组合

- [C++继承和组合](https://blog.csdn.net/qq_22080999/article/details/82048108)

若在逻辑上B 是一种A （is a kind of），则允许B 继承A 的功能，它们之间就是Is-A 关系。如男人（Man）是人（Human）的一种，女人（Woman）是人的一种。那么类Man 可以从类Human 派生，类Woman也可以从类Human 派生。



若在逻辑上A 是B 的“一部分”（a part of），则不允许B 继承A 的功能，而是要用A和其它东西组合出B，它们之间就是“Has-A关系”。例如眼（Eye）、鼻（Nose）、口（Mouth）、耳（Ear）是头（Head）的一部分，所以类Head 应该由类Eye、Nose、Mouth、Ear 组合而成，不是派生而成。



### 3. 访问控制：public/private/protected 和 友元 friend

```cpp
#include <iostream>
#include <string>

class Base{
public:
  Base(int ta, int tb, int tc):a(ta), b(tb), c(tc){}

  void Seta(int ta){a=ta;}
  void Setb(int tb){b=tb;}
  void Setc(int tc){c=tc;}

  friend void show(const Base &b);

public:
  int a;
private:
  int b;
protected:
  int c;
};

void show(const Base &b){
  std::cout<<b.a<<std::endl;
  std::cout<<b.b<<std::endl;
  std::cout<<b.c<<std::endl;
}


class Derived : public Base{
public:
  void Seta(int ta){a=ta;}
  //void Setb(int tb){b=tb;}
  void Setc(int tc){c=tc;}
};


void controlTest(){
  Base x(1,2,3);
  std::cout<<x.a<<std::endl;
  // std::cout<<x.b<<std::endl;
  // std::cout<<x.c<<std::endl;

  show(x);
}

int main(){
    controlTest();
    return 0;
}
```



类的属性public、private 和 protected

- 类的public成员可以被任意实体访问，你可以认为它就是c语言中的struct结构体，可以直接用a.x这种形式访问；
- 类的private成员不能直接被类的实体访问，也不能被子类的实体访问，但是可以被类的成员函数访问；
- 类的protected成员不能直接被类的实体访问，但是可以被子类访问，也可以被类的成员函数访问；

友元

C++控制对类对象私有部分的访问。通常，公有类方法提供惟一的访问途径，但是有时候这种限制太严格，以至于不适合特定的编程问题。在这种情况下，C++提供了另外一种形式的访问权限：友元。友元有3种：

- 友元函数；
- 友元类；
- 友元成员函数。

通过让函数成为类的友元，可以赋予该函数与类的成员函数相同的访问权限。



### 4. C++构造函数可以是虚函数吗？为什么？

构造函数不能是虚函数。

1. 构造一个对象的时候，必须知道对象的实际类型，而虚函数行为是在运行期间确定实际类型的。而在构造一个对象时，由于对象还未构造成功。编译器无法知道对象的实际类型，是该类本身，还是该类的一个派生类，或是更深层次的派生类。无法确定。
2. 虚函数的执行依赖于虚函数表。而虚函数表在构造函数中进行初始化工作，即初始化vptr，让他指向正确的虚函数表。而在构造对象期间，虚函数表还没有被初始化，将无法进行。



### 5. C++析构函数可以是虚函数吗？为什么要将析构函数设置为虚函数？

析构函数可以是虚函数。

1. 析构函数的作用是在对象撤销之前做必要的“清理现场”的工作。
   当派生类的对象从内存中撤销时一般先调用派生类的析构函数，然后再调用基类的析构函数。
   但是**用`new`和`delete`分配和释放对象时，系统只执行基类的析构函数，而不会执行派生类的析构函数。**
2. 如果将基类的析构函数声明为虚函数时，由该基类所派生的所有派生类的析构函数也都自动成为虚函数，即使派生类的析构函数与基类的析构函数名字不相同。
3. 最好把基类的析构函数声明为虚函数。这将使所有派生类的析构函数自动成为虚函数。
   这样，如果程序中显式地用了delete运算符准备删除一个对象， 而delete运算符的操作对象用了指向派生类对象的基类指针，则系统会调用相应类的析构函数。



### 6. C++如何实现多态？虚表指针是什么时候初始化的？实例化一个对象需要哪几个阶段（三个）？

C++实现多态的机制，在继承体系下，将父类的某个函数声明成虚函数（即加上virtual关键字），在派生类中对这个虚函数进行重写，利用父类的指针或引用调用虚函数。

1. 隐式转换：将派生类的指针转化为基类指针；
2. 虚函数机制（虚函数表）；
3. dynamic_cast ：使得基类指针能够向下转型；



参考：[虚函数表的实现细节](https://blog.csdn.net/lingfengtengfei/article/details/12345809)

虚函数表：

- 拥有虚函数的类会有一个虚表，虚表存放在类定义模块的数据段中。模块的数据段通常定义在改模块的全局数据和静态数据，这样我们可以把虚表看作是模块的全局数据或静态数据；
- 类的虚表被这个类的所有对象共享。类的对象可以有很多，但他们的虚表指针指向同一个虚表，从这个意义上说，我们可以把虚表简单理解为类的静态数据成员。值得注意的是，虽然虚表是共享的，但是虚表指针并不是，类的每一个对象有一个属于它自己的虚表指针；
- 虚表中存放的是虚函数的地址；



虚表指针初始化时机：进入到构造函数体之前。

1. Make the instance point to the base class's *vtbl*.
2. Construct the base class instance member variables.
3. Execute the body of the base class constructor.



C++对象实例化过程：

参考： [C++类实例化过程](http://www.cppblog.com/uniker/archive/2010/08/16/123619.html)

When you construct an instance of a derived class, what happens, exactly? If the class has a *vtbl*, the process goes something like the following.

Step 1: Construct the top-level base part:.

1. Make the instance point to the base class's *vtbl*.
2. Construct the base class instance member variables.
3. Execute the body of the base class constructor.

Step 2: Construct the derived part(s) (recursively):

1. Make the instance point to the derived class's *vtbl*.
2. Construct the derived class instance member variables.
3. Execute the body of the derived class constructor.



Destruction happens in reverse order, something like this:

Step 1: Destruct the derived part:

1. (The instance already points to the derived class's *vtbl*.)
2. Execute the body of the derived class destructor.
3. Destruct the derived class instance member variables.

Step 2: Destruct the base part(s) (recursively):

1. Make the instance point to the base class's vtbl.
2. Execute the body of the base class destructor.
3. Destruct the base class instance member variables.



### 7. C++ 偏特化



### 8. 重载、重写、覆盖

参考：[c++中重载，重写，覆盖](https://www.cnblogs.com/tianzeng/p/9775672.html)



### 9. static 关键字的作用？（要全面）怎么实现的？

1.  `static`函数

```
static int function() {
    //code
    return value;
 }
```

以为返回的是一个static值（其实不是），后来才知道原来是**声明该函数是个static类型的函数，意义为该函数只能在本文件之内使用**.

2. `static`的用法总结

两种用法：面向过程的用法和面向对象的用法。上面的问题即为面向过程的用法。

1. 面向过程的static用法：
   1. 静态全局变量：静态全局变量存储在内存的全局数据区，其作用域仅仅为定义该变量的文件（因为这一条，其他文件可以定义相同名字的变量），它会被自动初始化为0；
   2. 静态局部变量：静态局部变量同样存储在内存的全局数据区，直到程序运行结束。它的作用域为定义该变量的函数。当函数结束的时候，变量的作用域随之结束，但是变量仍然在全局数据区。如果函数再次运行，仍能访问该变量。它仅仅在声明的时候被初始化，然后再不初始化。
   3. 静态函数：声明该函数是个static类型的函数，意义为该函数只能在本文件之内使用。
2. 面向对象的static用法：
   1. 静态数据成员：静态数据成员不属于任何一个对象，所有该类的对象共享该成员。静态数据成员存储在全局数据区，定义时要分配空间。所以不能在类的声明中定义，只能放在类的外面定义，但是可以在类的里面声明。静态数据成员同样遵循public，protect，private访问规则。
   2. 静态成员函数：静态成员函数和静态数据成员一样，不属于任何一个对象，属于类定义的一部分。普通函数都隐含了一个this指针，this指针指向对象本身。但是静态成员函数不含this指针，从这个意义上讲，静态成员函数不能访问属于对象的非静态数据成员，也不能访问非静态函数，它只能调用其他静态函数。但是非静态成员却能访问静态成员。静态成员函数没有this指针的额外开销，速度上稍微快一些。



### 10. inline 和 宏定义的区别？inline是如何实现的？宏定义是如何实现的？

参考： [inline 和 宏定义的区别](https://blog.csdn.net/licui94/article/details/8134290)

内联函数与宏定义区别:

 （1）内联函数在编译时展开，宏在预编译时展开；

 （2）内联函数直接嵌入到目标代码中，宏是简单的做文本替换；

 （3）内联函数有类型检测、语法判断等功能，而宏没有；

 （4）inline函数是函数，宏不是；

 （5）宏定义时要注意书写（参数要括起来）否则容易出现歧义，内联函数不会产生歧义；



### 11. 指针和引用的区别？怎么实现的？

```cpp
#include <iostream>
#include <string>

void pointer_demo(){
  int a = 1;
  int &b = a;
  int *ptr = &a;

  std::cout<<a<<std::ends<<b<<std::ends<<*ptr<<std::endl;  // 111
  std::cout<<ptr<<std::endl;  // 0x7fff4cdea324
  std::cout<<&ptr<<std::endl;  // 0x7fff4cdea318

  int x = 2;
  b = x;  // 改变了a的值为2
  ptr = &x;

  std::cout<<x<<std::ends<<b<<std::ends<<a<<std::ends<<*ptr<<std::endl;  // 2222
  std::cout<<ptr<<std::endl;  // 0x7fff4cdea314
  std::cout<<&ptr<<std::endl;  // 0x7fff4cdea318
    
  // int &c;  // error: ‘c’ declared as reference but not initialized
}


int main(){
  pointer_demo();
  return 0;
}
```

1. 指针是一个变量，只不过这个变量存储的是一个地址，指向内存的一个存储单元；而引用是原变量的一个别名而已；
2. 指针的值可以为空，但是引用的值不能为NULL，并且引用在定义的时候必须初始化；
3. 指针的值在初始化后可以改变，即指向其它的存储单元，而引用在进行初始化后就不会再改变了；
4. "sizeof引用"得到的是所指向的变量的大小，而"sizeof指针"得到的是指针本身的大小；
5. 指针和引用的自增(++)运算意义不一样；
6. 指针和引用作为函数参数进行传递时的区别（指针是值传递、引用时引用传递）；

定义指针和定义引用的底层：其实两者都是一样的，都是存放了所指变量的地址，通过它来索引到所指变量的。C++对常量指针进行一些包装后就成了引用。



### 12. 详细回答 malloc 和 mmap 的底层实现？malloc 分配的是什么？

原文链接：https://blog.csdn.net/czg13548930186/article/details/79608170

malloc():
调用malloc()时，是在PCB表(进程表)结构中的堆重点内容中申请空间，若申请空间失败，即超过给定的堆最大空间时，将会调用brk()系统调用，将堆空间向未使用的区域扩展，brk()之后新增的堆空间不会自动清除，需使用相应的系统调用来清除；

mmap():
调用mmap()系统调用使得进程之间通过映射同一个普通文件实现共享内存。普通文件被映射到进程地址空间后，进程可以像访问普通内存一样对文件进行访问，不必再调用read()，write（）等操作。



补充：

-  [Linux内存分配小结--malloc、brk、mmap](https://blog.csdn.net/gfgdsg/article/details/42709943)
-  [认真分析mmap：是什么 为什么 怎么用](https://www.cnblogs.com/huxiao-tee/p/4660352.html)



### 13. Linux 进程地址空间布局？虚拟内存、分页、分表？

参考： [计算机底层知识拾遗（一）理解虚拟内存机制](https://blog.csdn.net/ITer_ZC/article/details/42644229)

进程空间布局：

1. 内核空间
2. 用户空间
   1. 栈（stack）
      栈又称堆栈，由编译器自动分配释放，用来存储临时数据和栈帧。
   2. 内存映射段（memory mapping segment）
      将硬盘文件的内容直接映射到内存，内存映射是一种方便高效的文件I/O方式， 因而被用于装载动态共享库。
   3. 堆（heap）
      堆用于存放进程运行时动态分配的内存段。
   4. BSS段（bss segment）
      静态内存分配，保存未初始化的全局及静态变量（皆为0），可读可写。
   5. 数据段（data segment）
      静态内存分配，保存已初始化的全局及静态变量，可读可写。
   6. 代码段（text segment）
      保存可执行机器码和常量，可读不可写可执行。

虚拟内存：

1. 给所有进程提供一致的地址空间，每个进程都认为自己是在独占使用单机系统的存储资源
2. 保护每个进程的地址空间不被其他进程破坏，隔离了进程的地址访问
3. 根据缓存原理，上层存储是下层存储的缓存，虚拟内存把主存作为磁盘的高速缓存，在主存和磁盘之间根据需要来回传送数据，高效地使用了主存;



CPU在取一个实际的物理地址的值主要有几步：

1. 把进程指令使用的虚拟地址通过MMU转换成物理地址;
2. 把物理地址映射到高速缓存的缓存行;
3. 如果高速缓存命中就返回;
4. 如果不命中，就产生一个缓存缺失中断，从主存相应的物理地址取值，并加载到高速缓存中。CPU从中断中恢复，继续执行中断前的指令;



在缓存原理中，数据都是按块来进行逻辑划分的，一次换入/换出的数据都是以块为最小单位，这样提高了数据处理的性能。同样的原理应用到具体的内存管理时，使用了页(page)来表示块，虚拟地址空间划分为多个固定大小的**虚拟页(Virtual Page, VP)**，物理地址空间划分为多个固定大小的**物理页(Physical Page**, PP), 通常虚拟页大小等于物理页大小，这样简化了虚拟页和物理页的映射。

从缓存原理的角度来理解，在任何时刻，虚拟页的集合都分为3个不相交的子集：

1. 未分配的页，即没有任何数据和这些虚拟页关联，不占用任何磁盘空间;
2. 缓存的页，即已经分配了的虚拟页，并且已经缓存在具体的物理页中;
3. 未缓存的页，即已经为磁盘文件分配了虚拟页，但是还没有缓存到具体的物理页中;

虚拟内存的这种缓存管理机制是通过操作系统内核，**MMU(内存管理单元)**中的地址翻译硬件和每个进程存放在主存中的**页表(page table)**数据结构来实现的。

**页表(page table)**是存放在主存中的，每个进程维护一个单独的页表。它是一种管理虚拟内存页和物理内存页映射和缓存状态的数据结构。它逻辑上是由页表条目(Page Table Entry， PTE)为基本元素构成的数组。

1. 数组的索引号对应着虚拟页号;
2. 数组的值对应着物理页号;
3. 数组的值可以留出几位来表示有效位，权限控制位。有效位为1的时候表示虚拟页已经缓存。有效位为0，数组值为null时，表示未分配。有效位为0，数组值不为null，表示已经分配了虚拟页，但是还未缓存到具体的物理页中。 权限控制位有可读，可写，是否需要root权限;

Linux把虚拟内存划分成区域area的集合，每个存在的虚拟页面都属于一个area。一个area包含了连续的多个页。



### 14. map 和 unorderd_map 的底层实现？目前map支持`map[10]=20`的操作，其底层实现是怎样的？和insert 有什么区别？

内部实现机理不同：

- map： map内部实现了一个红黑树（红黑树是非严格平衡二叉搜索树，而AVL是严格平衡二叉搜索树），红黑树具有自动排序的功能，因此map内部的所有元素都是有序的，红黑树的每一个节点都代表着map的一个元素。因此，对于map进行的查找，删除，添加等一系列的操作都相当于是对红黑树进行的操作。map中的元素是按照二叉搜索树（又名二叉查找树、二叉排序树，特点就是左子树上所有节点的键值都小于根节点的键值，右子树所有节点的键值都大于根节点的键值）存储的，使用中序遍历可将键值按照从小到大遍历出来。
- unordered_map: unordered_map内部实现了一个哈希表（也叫散列表，通过把关键码值映射到Hash表中一个位置来访问记录，查找的时间复杂度可达到O(1)，其在海量数据处理中有着广泛应用）。因此，其元素的排列顺序是无序的。



   参考《STL源码剖析》

   - 一个是 insert 函数，一个是[]下标操作符重载；
   - []下标操作符有两种用法：左值引用和右值引用，左值引用可修改，右值引用不可修改；
   - insert函数直接调用底层红黑树的insert_unique()操作，插入成功则返回指向插入节点的迭代器；

 ```cpp
#include <iostream>
#include <map>

void map_demo(){
  std::map<int, std::string> m;

  m.insert(std::make_pair(1, "tom"));
  std::cout<<m[1]<<std::endl;

  m.insert(std::make_pair(1, "jim"));  // 操作失败，m[1] 值不变
  std::cout<<m[1]<<std::endl;

  m[1]="jim";  // 修改成功
  std::cout<<m[1]<<std::endl;

  for(auto iter = m.begin(); iter!=m.end(); iter++){
    std::cout<<iter->first<<std::ends<<iter->second<<std::endl;
  }
  
  // 遍历删除map中元素
  auto iter = m.begin();
  while(iter!=m.end()){
	  iter=m.erase(iter);
  }
  std::cout<<"m.size is "<<m.size()<<std::endl;
}

int main(){
  map_demo();
  return 0;
}
 ```



### 15. map 如何遍历删除所有元素？迭代器失效？

```cpp
#include <iostream>
#include <map>

void map_demo(){
  std::map<int, std::string> m;
  m.insert(std::make_pair(1, "tom"));
  m.insert(std::make_pair(2, "jim")); 

  for(auto iter = m.begin(); iter!=m.end(); iter++){
    std::cout<<iter->first<<std::ends<<iter->second<<std::endl;
  }
  
  // 遍历删除map中元素
  auto iter = m.begin();
  while(iter!=m.end()){
	  iter=m.erase(iter);
  }
  std::cout<<"m.size is "<<m.size()<<std::endl;
}

int main(){
  map_demo();
  return 0;
}
```



### 16. zset的底层实现是什么？为什么zset不使用红黑树作为其底层实现？为什么map不使用跳跃表作为其底层实现？

参考： [redis为什么选择了跳跃表而不是红黑树](https://www.cnblogs.com/cjjjj/p/12751487.html)

1. 在做范围查找的时候，平衡树比skiplist操作要复杂。在平衡树上，我们找到指定范围的小值之后，还需要以中序遍历的顺序继续寻找其它不超过大值的节点。如果不对平衡树进行一定的改造，这里的中序遍历并不容易实现。而在skiplist上进行范围查找就非常简单，只需要在找到小值之后，对第1层链表进行若干步的遍历就可以实现。
2. 平衡树的插入和删除操作可能引发子树的调整，逻辑复杂，而skiplist的插入和删除只需要修改相邻节点的指针，操作简单又快速。
3. 从内存占用上来说，skiplist比平衡树更灵活一些。一般来说，平衡树每个节点包含2个指针（分别指向左右子树），而skiplist每个节点包含的指针数目平均为1/(1-p)，具体取决于参数p的大小。如果像Redis里的实现一样，取p=1/4，那么平均每个节点包含1.33个指针，比平衡树更有优势。
4. 查找单个key，skiplist和红黑树的时间复杂度都为O(log n)，大体相当；而哈希表在保持较低的哈希值冲突概率的前提下，查找时间复杂度接近O(1)，性能更高一些。所以我们平常使用的各种Map或dictionary结构，大都是基于哈希表实现的。
5. 从算法实现难度上来比较，skiplist比红黑树要简单得多。



### 17. 智能指针

```cpp
#include <memory>
#include <iostream>

void smart_pointer_demo(){
  // shared_ptr
  std::shared_ptr<int> sptr = std::make_shared<int>(5);
  std::cout<<*sptr<<std::endl;

  int *ptr = sptr.get();
  std::cout<<*ptr<<std::endl;

  std::cout<<sptr.use_count()<<std::endl;
    
  // std::shared_ptr<int> sptr_1 = new int(11);  // error: conversion from ‘int*’ to non-scalar type ‘std::shared_ptr<int>’ requested
  std::shared_ptr<int> sptr_1(new int(11));
  std::cout<<*sptr_1<<std::endl;

  // weak_ptr
  std::weak_ptr<int> wptr = sptr;
  std::cout<<sptr.use_count()<<std::endl;
  // std::cout<<*wptr<<std::endl;  // error: no match for ‘operator*’
  std::shared_ptr<int> swptr = wptr.lock();
  std::cout<<*swptr<<std::endl;

  sptr.reset();
  std::cout<<sptr.use_count()<<std::endl;
  std::cout<<swptr.use_count()<<std::endl;

  // unique_ptr
  std::unique_ptr<int> uptr(new int(7));
  std::cout<<*uptr<<std::endl;
}

int main(){
  smart_pointer_demo();
  return 0;
}
```
在C++中，动态内存的管理是用一对运算符完成的：new和delete，new:在动态内存中为对象分配一块空间并返回一个指向该对象的指针，delete：指向一个动态独享的指针，销毁对象，并释放与之关联的内存。

动态内存管理经常会出现两种问题：一种是忘记释放内存，会造成内存泄漏；一种是尚有指针引用内存的情况下就释放了它，就会产生引用非法内存的指针。

为了更加容易（更加安全）的使用动态内存，引入了智能指针的概念。智能指针的行为类似常规指针，重要的区别是它负责自动释放所指向的对象。标准库提供的三种智能指针的区别在于管理底层指针的方法不同，shared_ptr允许多个指针指向同一个对象，unique_ptr则“独占”所指向的对象。weak_ptr是一种弱引用，指向shared_ptr所管理的对象，这三种智能指针都定义在memory头文件中。

原文链接：https://blog.csdn.net/flowing_wind/article/details/81301001


### 18. 内存对齐

参考： [关于面试题中结构体内存对齐计算总结](https://blog.csdn.net/sssssuuuuu666/article/details/80130632)

对齐原则：
原则1：数据成员对齐规则：结构(struct)(或联合(union))的数据成员，第一个数据成员放在offset为0的地方，以后每个数据成员的对齐按照#pragma pack指定的数值和这个数据成员自身长度中，比较小的那个进行。
原则2：结构(或联合)的整体对齐规则：在数据成员完成各自对齐之后，结构(或联合)本身也要进行对齐，对齐将按照#pragma pack指定的数值和结构(或联合)最大数据成员长度中，比较小的那个进行。
原则3：结构体作为成员：如果一个结构里有某些结构体成员，则结构体成员要从其内部最大元素大小的整数倍地址开始存储。

默认对齐值：

- Linux 默认#pragma pack(4)
- window 默认#pragma pack(8)

```cpp
#include <iostream>

#pragma pack(4)

struct A{
  int a;
  char b;
  bool c;
};

struct B{
  int b;
  A a;
  char c;
  bool d;
};

struct C{
  int b;
  char x;
  A a;
  char c;
  bool d;
};

void demo(){
  std::cout<<sizeof(A)<<std::endl;  // 8
  std::cout<<sizeof(B)<<std::endl;  // 16
  std::cout<<sizeof(C)<<std::endl;  // 20
}

int main(){
  demo();
  return 0;
}
```

为什么要内存对齐？ 

1. 平台原因(移植原因)：不是所有的硬件平台都能访问任意地址上的任意数据的；某些硬件平台只能在某些地址处取某些特定类型的数据，否则抛出硬件异常。 
2. 性能原因：数据结构(尤其是栈)应该尽可能地在自然边界上对齐。原因在于，为了访问未对齐的内存，处理器需要作两次内存访问；而对齐的内存访问仅需要一次访问。


例题：
```cpp
#include<iostream>
using namespace std;

class test {
private :

    char c='1';//1byte
    int i;//4byte
    short s=2;//2byte
};


class A{
public:
    virtual void FuncA(){};
private:
    char a;
    short b;
    int c;
};


int main(){
    cout << sizeof(test) << endl;   // 12
    cout << sizeof(A) << endl;      // 16

    return 0;
}
```

### 19. 堆和栈有什么区别？

1. 管理方式：对于栈来讲，是由编译器自动管理，无需我们手工控制；对于堆来说，释放工作由程序员控制，容易产生memory leak（内存泄漏）。
2. 空间大小：一般来讲在 32 位系统下，堆内存可以达到4G的空间，从这个角度来看堆内存几乎是没有什么限制的。但是对于栈来讲，一般都是有一定的空间大小的，例如，在VC6下面，默认的栈空间大小是1M。当然，我们可以修改：打开工程，依次操作菜单如 下：Project­>Setting­>Link，在 Category 中选中 Output，然后在Reserve 中设定堆栈的最大值和 commit。注意：reserve 最小值为 4Byte；commit 是保留在虚拟内存的页文件里面，它设置的较大会使栈开辟较大的值，可能增加内存的开销和启动时间。
3. 碎片问题：对于堆来讲，频繁的 new/delete 势必会造成内存空间的不连续，从而造成大量的碎片，使程序效率降低。对于栈来讲，则不会存在这个问题，因为栈是先进后出的队列，他们是如此的一一对应，以至于永远都不可能有一个内存块从栈中间弹出，在他弹出之前，在他上面的后进的栈内容已经被弹出。
4. 生长方向：对于堆来讲，生长方向是向上的，也就是向着内存地址增加的方向；对于栈来讲，它的生长方向是向下的，是向着内存地址减小的方向增长。
5. 分配方式：堆都是动态分配的，没有静态分配的堆。  
   栈有2种分配方式：静态分配和动态分配。静态分配是编译器完成的，比如局部变量的分配。动态分配由 malloc 函数进行分配，但是栈的动态分配和堆是不同的，他的动态分配是由编译器进行释放，无需我们手工实现。
6. 分配效率：栈是机器系统提供的数据结构，计算机会在底层对栈提供支持：分配专门的寄存器存放栈的地址，压栈出栈都有专门的指令执行，这就决定了栈的效率比较高。堆则是 C/C++ 函数库提供的，它的机制是很复杂的，例如为了分配一块内存，库函数会按照一定的算法在堆内存中搜索可用的足够大小的空间，如果没有足够大小的空间（可能是由于内存碎片太多），就有可能调用系统功能去增加程序数据段的内存空间，这样就有机会分到足够大小的内存，然后进行返回。显然，堆的效率比栈要低得多。

如果有人把堆栈合起来说，那它的意思是栈，可不是堆。



### 20. 纯虚函数

纯虚函数是一个在基类中声明的虚函数，它在该基类中没有定义具体的操作内容，要求各派生类根据实际需要定义自己的版本。
纯虚函数的声明格式为：

```
virtual 函数类型 函数名（参数表）=0；  
```

注意:
①纯虚函数没有函数体；
②最后面的“=0”并不表示函数返回值为0，它只起形式上的作用，告诉编译系统“这是纯虚函数”;
③这是一个声明语句，最后应有分号。

声明为纯虚函数之后，基类中就不再给出函数的实现部分。纯虚函数的函数体由派生类给出。定义纯虚函数的目的在于，使派生类仅仅只是继承函数的接口。

还有一种情况是函数体为空的虚函数，请注意它和纯虚函数的区别:

1. 纯虚函数根本就没有函数体，而空的虚函数的函数体为空;
2. 前者所在的类是抽象类，不能直接进行实例化；而后者所在的类是可以实例化的。
3. 它们共同的特点是都可以派生出新的类，然后在新类中给出虚函数新的实现，而且这种新的实现可以具有多态特征。

```cpp
#include <iostream>

class Base{
public:
  Base(){
    std::cout<<"Base constructor"<<std::endl;
  }

  virtual ~Base(){
    std::cout<<"Base destructor"<<std::endl;
  }

  virtual void show(){
    std::cout<<"Base show()"<<std::endl;
  }

  virtual void string() = 0;
};

class Derived : public Base{
public:
  Derived(){
    std::cout<<"Derived constructor"<<std::endl;
  }
  ~Derived(){
    std::cout<<"Derived destructor"<<std::endl;
  }
  void show(){
    std::cout<<"Derived show()"<<std::endl;
  }
  // void string(){ std::cout<<"Derived string()"<<std::endl; }
};

class DeDerived : public Derived{
public:
 void string(){std::cout<<"DeDerived string()"<<std::endl; }
};

void demo(){
  Base *b=new DeDerived();
  b->show();
  b->string();
  delete b;
}

int main(){
  demo();
  return 0;
}

/* 输出结果：
Base constructor
Derived constructor
Derived show()
DeDerived string()
Derived destructor
Base destructor
*/
```



### 21. 抽象基类（abstract base class）

由于抽象类常用作基类，通常称为抽象基类(abstract base class)。

1. 含有（或者未经覆盖直接继承）纯虚函数的类是抽象基类;
2. 抽象基类不能被实例化，即不能定义一个抽象类的对象。但是，我们可以声明一个抽象类的指针和引用。通过指针或引用，我们就可以指向并访问派生类对象，进而访问派生类的成员，这种访问时具有多态特征的。
3. 可以使用public方式继承抽象类,并且实现抽象类中的**所有**纯虚函数;

抽象类的主要作用是通过它为一个类族建立一个公共的接口，使它们能够更有效地发挥多态特性。

抽象类是一种特殊的类，抽象类出于类层次的上层，一个抽象类自身无法实例化，只能通过继承机制，C++抽象类生成抽象类的非抽象派生类，然后再实例化。

抽象类声明了一组派生类的共同接口，而接口的完整实现，即纯虚函数的函数体，要由派生类自己定义。
抽象类派生出新的类之后，如果派生类给出所有纯虚函数的函数实现，这个派生类就可以定义自己的对象，因而不再是抽象类；
反之，如果派生类没有给出全部纯虚函数的实现，这时的派生类仍然是一个抽象类。



### 22. `extern "C"`的作用

参考： [extern “C”的作用详解](https://www.cnblogs.com/xiangtingshen/p/10980055.html)

extern "C"的主要作用就是为了能够正确实现C++代码调用其他C语言代码。加上extern "C"后，会指示编译器这部分代码按C语言的方式进行编译。由于C++支持函数重载，因此编译器编译函数的过程中会将函数的参数类型也加到编译后的代码中，而不仅仅是函数名；而C语言并不支持函数重载，因此编译C语言代码的函数时不会带上函数的参数类型，一般只包括函数名。

这个功能十分有用处，因为在C++出现以前，很多代码都是C语言写的，而且很底层的库也是C语言写的，为了更好的支持原来的C代码和已经写好的C语言库，需要在C++中尽可能的支持C，而extern "C"就是其中的一个策略。

这个功能主要用在下面的情况：

1.  C++代码调用C语言代码
2.  在C++的头文件中使用
3.  在多个人协同开发时，可能有的人比较擅长C语言，而有的人擅长C++，这样的情况下也会有用到

extern是C/C++语言中表明函数和全局变量的作用范围的关键字，该关键字告诉编译器，其申明的函数和变量可以在本模块或其他模块中使用。

**记住**，语句：`extern int a; `仅仅是一个变量的声明，其并不是在定义变量a，也并未为a分配空间。变量a在所有模块中作为一种全局变量只能被定义一次，否则会出错。



### 23. static 变量存放在哪里？什么时候分配的内存？

当程序加载到内存中时，它被组织成不同的段。其中一个片段是**数据段**数据部分进一步细分为两部分：

**初始化数据段：**所有的全局、静态和常量数据都存储在这里。

**未初始化数据段(BSS)：**所有未初始化的数据都存储在此段中。

### 24. 构造函数
```cpp
#include<iostream>
using namespace std;


class C{
public:
    C(){ std::cout<<"C Constructor"<<std::endl;}
    C(const C &other){ std::cout<<"C Copy Constructor"<<std::endl;}
    C& operator=(const C &other) { std::cout<<"C Operator="<<std::endl;}
};

int main(){
    C c1;       // C Constructor
    C c2 = c1;  // C Copy Constructor
    c2 = c1;    // C Operator=

    return 0;
}
```

### 25. 单例模式
单例模式实现
单例模式的删除
单例模式和全局变量的区别
参考：
- [全局变量与单例模式](https://blog.csdn.net/gettogetto/article/details/76944178)
- [单例模式的解读以及和全局变量的区别](https://blog.csdn.net/warpar/article/details/74939015)
- [C++中的单例模式](https://blog.csdn.net/hackbuteer1/article/details/7460019)
- [深入浅出单实例Singleton设计模式](https://blog.csdn.net/haoel/article/details/4028232)


单例模式（Singleton），保证一个类仅有一个实例，并提供一个访问它的全局访问点。

定义一个单例类，使用类的私有静态指针变量指向类的唯一实例，并用一个公有的静态方法获取该实例。



单例模式可以分为懒汉式和饿汉式：

- 懒汉式：在类加载时不初始化。
- 饿汉式：在类加载时就完成了初始化，所以类加载比较慢，但获取对象的速度快。



单例模式和全局变量的区别？

全局变量是对一个对象的静态引用，全局变量确实可以提供单例模式实现的全局访问功能，但是它并不能保证应用程序只有一个实例；

全局变量可以创建多个实例，但单例模式只能创建一个；



```cpp
#include <iostream>
#include <mutex>


class Singleton{
public:
  static Singleton* GetInstance(){
    if(m_single == nullptr){
      m_mutex.lock();
      if(m_single == nullptr){
        m_single = new Singleton();
      }
      m_mutex.unlock();
    }
    return m_single;
  }

  ~Singleton(){ std::cout<<"destructor"<<std::endl; }
    
  void show(){ std::cout<<"show() "<<std::endl; }

private:
  Singleton(){ std::cout<<"constructor"<<std::endl;}
  Singleton(const Singleton&) = delete;
  Singleton& operator=(const Singleton&) = delete;

private:
  static Singleton *m_single;
  static std::mutex m_mutex;
};

Singleton* Singleton::m_single = nullptr;  // 懒汉式, 在第一次被引用时,才会实例化.
std::mutex Singleton::m_mutex;
/*******************************************************/

// TEST
void demo(){
  Singleton *s = Singleton::GetInstance();
  // Singleton *ss = Singleton::GetInstance();
  if(s!=nullptr){
    s->show();
  }
  // delete s;  // 需要手动调用delete，否则不会调用析构函数
  // 手动delete后，ss就成为空悬指针
}

int main(){
  demo();
  return 0;
}

/*
/* 
constructor
show()
*/
```

手动delete单例指针，会造成其他单例指针成为空悬指针。

可以在程序结束时调用GetInstance()，并对返回的指针掉用delete操作。这样做可以实现功能，但很容易出错。因为这样的附加代码很容易被忘记，而且也很难保证在delete之后，没有代码再调用GetInstance函数。



单例的释放？（单例指向的空间什么时候释放？该实例的析构函数什么时候执行？）

如果在类的析构行为中有必须的操作，比如关闭文件，释放外部资源，那么上面的代码无法实现这个要求。我们需要一种方法，正常的删除该实例。



程序在结束的时候，系统会自动析构所有的全局变量，也会析构所有的类的静态成员变量，就像这些静态成员也是全局变量一样。利用这个特征，我们可以在单例类中定义一个这样的静态成员变量，而它的唯一工作就是在析构函数中删除单例类的实例。

```cpp
#include <iostream>
#include <mutex>


class Singleton{
public:
  static Singleton* GetInstance(){
    if(m_single == nullptr){
      m_mutex.lock();
      if(m_single == nullptr){
        m_single = new Singleton();
      }
      m_mutex.unlock();
    }
    return m_single;
  }

  void show(){ std::cout<<"show() "<<std::endl; }
  // static void showb(){ std::cout<<"show() "<<std::endl; }


  ~Singleton(){ std::cout<<"destructor"<<std::endl; }

private:
  Singleton(){ std::cout<<"constructor"<<std::endl;}
  Singleton(const Singleton&) = delete;
  Singleton& operator=(const Singleton&) = delete;

private:
  static Singleton *m_single;
  static std::mutex m_mutex;

private:
  class GC{
  public:
    ~GC(){
      if(Singleton::m_single){
        delete Singleton::m_single;
      }
    }
  };
  static GC m_gc;
};

Singleton* Singleton::m_single = nullptr;
std::mutex Singleton::m_mutex;
Singleton::GC Singleton::m_gc;
/**********************************************/

// TEST
void demo(){
  Singleton *s = Singleton::GetInstance();
  Singleton *ss = Singleton::GetInstance();
  if(s!=nullptr){
    s->show();
  }
  //delete s;
}

int main(){
  demo();
  return 0;
}

/* 
constructor
show()
destructor
*/
```


### 26. 内存布局
- [C++内存对齐总结](https://www.cnblogs.com/zrtqsk/p/4371773.html)
- [C++ 对象的内存布局（上）](https://blog.csdn.net/haoel/article/details/3081328)
- [C++ 对象的内存布局（下）](https://blog.csdn.net/haoel/article/details/3081385)
- [C++ 虚函数表解析](https://blog.csdn.net/haoel/article/details/1948051)

### 27. `i++`
- [Inside i++](https://blog.csdn.net/haoel/article/details/2871)