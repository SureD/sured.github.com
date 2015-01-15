---
layout: post
title: "C++的数组不支持多态？--讨论"
description: ""
category: C++
tags: [C++]

---

### “C++的数组不支持多态？”--讨论

---



今天在酷壳看到这样一篇有意思的[博客](http://coolshell.cn/articles/9543.html#more-9543)：**C++的数组不支持多态？**
正好最近在看Inside the C++ Object Model就仔细研究了一下，顺便复习一下里面的data member的内容；  具体的问题在上面给出的链接中可以找到的～。
简单来说问题就是创建了一个指针数组，指针采用基类指针，当我们用delet释放资源时会出错：原博客<sup>**1**</sup>中给出的描述如下：
{% highlight c++ linenos %}
     class Base
    {
      public:
        virtual ~B(){ cout <<"B::~B()"<<endl; }
    };
     
    class Derived : public Base
    {
      public:
        virtual ~D() { cout <<"D::D~()"<<endl; }
    };
     
    Base* pBase = new Derived[10];
    delete[] pBase;  
{% endhighlight %}
作者已经解释的很好了，当然作者为了普及知识还是讲了很多其他的东西的，我觉的总而言之这个问题很简单，因为采用 delete[] 析构，调用是按照 pBase[0]..[1]..这种方式来调用每个数组中类的析构函数的，由于是数组的原因，pBase[0]这种方式相当于语法糖，访问时相当于pBase+offset，而这个offset是在编译时期就固定好的，因此只有一种情况上述代码可以执行，就是当base和derive大小相同时……这样也就没什么意义了……因此不仅是析构函数调用不成功，其他的函数也肯定时调用不成功的……。如果非要用怎么办呢？我试了一下，用vector应该可以(但不清楚这个的实际用途？)：
  
{% highlight c++ linenos %}
      #include<iostream>
        #include<vector>
        using namespace std;
        class A
        {
            public:
                int a;
                virtual ~A(){cout<<"A::~A()"<<endl;};
                virtual void foo(){cout<<"Woo I am A"<<endl;};
        };
        class B:public A
        {
            int dd;
            public:
                virtual ~B(){cout<<"B::~B()"<<endl;};
                void foo(){cout<<"WOo I am B"<<endl;};
                void bar(){cout<<"Nahhhh"<<endl;};
        };
        int main()
        {
           // A *p = new B[2];
           // p[0].foo();
           // p[1].foo();
            vector<A*> va;
            va.push_back(new A);
            va.push_back(new B);
            va[0]->foo();
            va[1]->foo();
            cout<<"SizeB: "<<sizeof(B)<<"SizeA: "<<sizeof(A)<<endl;
            return 0;
        }
{% endhighlight %}
在做点做点延伸，因为最近看Inside c++这本书的第三章 data member ，对类里面的布局很感兴趣，因此也实验了一下，我用的时g++的编译器，总结总结：

>1.一个空类被构造时，它的占用大小是1，因为编译器为这个空类安插了一个char
2.存在内存对齐的现象，为了提高总线传输效率  
3.base class subobject在derived class会保持原样性<sup>2</sup>:即在其继承的类中，基类会保持原装，其为了内存对齐所补的内容也会保持，这是因为在某些复制情况下，将基类复制给子类（*注意时复制，看书中的解释(p104)应当是bitwise的，是强制转换时产生的？此处有疑问*）如果不采用这种规则，会污染掉子类中其他的成员。
4.虚拟继承的实现：这个标准并没有规定，因此各家编译器都有自己的实现，微软加了一个virtual base class table，作者是对vptr进行改进，当offest为正时为虚函数，为负时为虚基类的表。
.....
这里简单写一下，当然还有很多内容，后面会写一个详细版的，这本书一直断断续续的再看，最近抽时间又认真的读了第一章和第三章，收获颇丰～

-------
上面的代码中我还加了一个bar()函数，发现函数（非虚）是不在对象的布局中的，刚开始觉得很疑惑，那么不占空间（也没有指针）是怎么访问到的呢？后来突然想起来C++在调用这种函数时，是会 传入 *this这个指针的，因此这种函数和普通函数的差别不大（inline除外）这个应该是第四章的内容

----
参考：  
[1] http://coolshell.cn/articles/9543.html#more-9543
[2] Lippman S B. Inside the C++ object model[M]. Reading: Addison-Wesley, 1996.  

----

后记：最近一直开题一直在忙的不可开交的，抽时间更新下博客（其实我晚上回去还是有那么一个小时更新的……只是我以练口语之名看了一集美剧……掩面。。）好在也大致确定了后面的研究方向，后面会在Objectness这方面折腾研究一番～
贴几个关键词最后，都是最近遇到的或者是一直再用但是有些地方有点模糊的，欢迎大家讨论：
dynamic_cast<> 这个在项目中我很少用到，一直觉得很神秘……
RAII 这个思想很经典，值得深入研究研究，发个相关[链接1](http://en.wikibooks.org/wiki/More_C++_Idioms/Copy-and-swap)，[链接2](http://www.cnblogs.com/hsinwang/articles/214663.html)   
这两天看了一些关于压缩编码的算法，是由一道题引发的……**如何利用1Mb的空间给100万个8位数字排序** [链接](http://stackoverflow.com/questions/12748246/sorting-1-million-8-digit-numbers-in-1mb-of-ram/13067807#13067807)给出答案的人有个博客，里面详细讲解了算法的实现，以及非常漂亮的压缩算法**算术编码**[博客地址](http://preshing.com/20121026/1mb-sorting-explained/)，这个算法实在是太漂亮了，以至于我一天什么都没干……就直研究这个了……在看的时候发现中文的资料很少，待我和作者联系联系看看能不能让我翻译～
