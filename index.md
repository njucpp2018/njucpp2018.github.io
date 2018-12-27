## 课程信息

授课时间：2018年秋

授课老师：于耀

助教：江沛远、李昆伦

作业上传：
* 上传邮箱：njucpp2018@163.com
* 上传格式：学号_姓名_第n次作业_第n次提交.zip/rar（zip中只需包含.cpp及.h文件），我们将以截止日期前最后一次提交的版本为准。
* 截止时间：一般为下次实验课前，共两周时间。

## 作业

* assignment1 (关闭下载)  截止日期：2018.9.29   [assignment1_answer](https://github.com/njucpp2018/njucpp2018.github.io/raw/master/assignment1_answer.zip)

* assignment2 (关闭下载)  截止日期：2018.11.1   [assignment2_answer](https://github.com/njucpp2018/njucpp2018.github.io/raw/master/assignment2_answer.zip)

* assignment3 (关闭下载)  截止日期：2018.11.15  [assignment3_answer](https://github.com/njucpp2018/njucpp2018.github.io/raw/master/assignment3_answer.zip)

* assignment4 (关闭下载)  截止日期：2018.11.29  [assignment4_answer](https://github.com/njucpp2018/njucpp2018.github.io/raw/master/solutions/assignment4_answer_CPPListIterator.rar)

* assignment5 (关闭下载)  截止日期：2018.12.21  [assignment5_answer](https://github.com/njucpp2018/njucpp2018.github.io/raw/master/solutions/assignment5_answer_Draw.zip)

* [assignment6](https://github.com/njucpp2018/njucpp2018.github.io/raw/master/assignment6.zip)  截止日期：本次作业不提交 
<!-- [Find.zip](https://github.com/njucpp2018/njucpp2018.github.io/raw/master/Find.zip) -->



## 作业提交情况（严格按照格式要求，注意打包压缩，否则无效）

* [作业提交情况_汇总_3](https://github.com/njucpp2018/njucpp2018.github.io/raw/master/作业提交情况_汇总_3.xlsx)

* 强调格式要求：

* 学号_姓名_第3次作业_第2次提交.zip/rar（zip中只需包含.cpp及.h文件）

## 课件

* [3-C++对C的扩充.ppt](https://github.com/njucpp2018/njucpp2018.github.io/raw/master/3-C++对C的扩充.ppt)
* [4-数据抽象－类.ppt](https://github.com/njucpp2018/njucpp2018.github.io/raw/master/4-数据抽象－类+(2).ppt)
* [5-操作符重载.ppt](https://github.com/njucpp2018/njucpp2018.github.io/raw/master/5-操作符重载+(3).pptx)
* [第8章 继承－派生类 2018.ppt](https://github.com/njucpp2018/njucpp2018.github.io/raw/master/第8章 继承－派生类 2018.ppt)

* [第9章+类属机制－－模板.ppt](https://github.com/njucpp2018/njucpp2018.github.io/raw/master/第9章+类属机制－－模板.ppt)

## 备注：

1.作业提交以是否在提交时收到邮件自动回复为准。

2.作业所提供的测试代码与最终评分的测试代码不全相同，若出现作业成绩评分上的疑问，原则上将不予回复。

## 常见问题

### 关于自赋值

1.  因为自赋值本身是一个没有实际作用的操作，而不阻止自赋值，则总会把operator=走一遍，也就是说，为了一个没有作用的事情付出开销，性价比为0。
2.  对于带有指针的情况，例如下面的例子：

```
class A
{
    char *buff; 
    int size; 
    public:
    A &operator=(const A &rhs)
    {
        if (&rhs == this) return *this; 

        delete []buff; 
        buff = new char[rhs.size]; 

        size = rhs.size; 
        memcpy(buff, rhs.buff, size); 
    }
}; 
```
这种情况，很可能会先删除自己的动态内存，再根据右参重新开辟空间。那么，自赋值如果不组织，那么这个例子中，buff——也就是this->buff和rhs.buff是同一个变量（地址完全相同），因此，先delete []buff，等价于delete []rhs.buff，之后再重新给buff——等价于rhs.buff重新开辟空间，那么后面的memcpy，已经不是从最初那个rhs.buff拷贝内容了，而那里的内容也就完全丢失了。
    
### 浅拷贝问题

1.  浅拷贝、深拷贝，都是拷贝函数，这里两种名称，区分的是对于那些有动态内存的类，对于动态内存是复制地址（指向相同空间），还是不复制地址（指向不同空间）而复制地址对应的内容：不如上面那个类A，假设有一个拷贝构造函数，那么对于buff的复制，有两种：

```
buff = rhs.buff；
```

或者：

```
buff = new char[rhs.size];
memcpy(buff, rhs.buff, size);
```

前者为浅拷贝，后者为深拷贝。

大多数情况下，我们更愿意用深拷贝，否则会引起内存出错，但某些情况，比如智能指针，会选浅拷贝，因为这就是智能指针的目的。那么结合默认情况下，编译器给的拷贝是浅拷贝，我们就能够知道，我们到底应不应该自己去写自定义的拷贝构造函数了。
    
2.  考虑如下代码：

```
A function()
{
    A local;
    return local; 
}


A result = function(); 
```

在机器代码中，函数有一个存放返回值的地方（一般在栈上），注意这个东西和这里的local不是一回事，你可以把最后那句话，理解为两句话：

```
A return_val = local; 
A result = return_val; 
```

为什么return_val和local不是一个地方呢？因为local位于function调用是的栈上，调用结束后，就回收了，所以没法直接把local给result。

所以，先把这个local放在一个和function内部想通，也和外部想通的return_val上，然后再给result。

理解这个之后，就会发现，单这一行其实会涉及两次析构，两次拷贝，按顺序分别是：
local拷贝给return_val，local析构，return_val拷贝给result，return_val析构。

再结合前面的浅拷贝知识，你就知道了，这里会存在内存多次释放问题了。
    
3.  由于这里的两次拷贝有一定的效率损失，在后来的编译器中，加入了一种优化策略，就是直接把local拷贝给result，这种情况下，这句话仅会有一次析构和一次拷贝：
local拷贝给result，local析构。

但并不是所有的编译器都默认打开这个优化的。

但事实上我们不太需要特别紧张这一点，纠结到底会一次还是两次。只要注意三位一体原则，该做深拷贝的时候，做了；或者像智能指针这种，把引用计数做好了，那么无论编译器选择一次还是两次拷贝，都一定不会出错


