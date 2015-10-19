---
layout: post
title: "深度探索C++面对对象模型第三章笔记"
description: "C++"
category: C++
tags: [C++]
--- 



###胡言乱语……
  好久没有更新了，期间自己写了一些乱起八糟的东西。不好意思发布上来- -，不过我发现了一个非常好的在线的Markdown编辑器，最近一直在用它->[CmdMarkdown](https://www.zybuluo.com/mdeditor)  强烈推荐！！  
  这本书第三四章翻来覆去看了几遍，还是有点收获，第三四章分别对c++中成员变量以及成员函数的内存布局做了分析，对于了解C++有着很大的帮助。

----
###概览
   第三章的重心主要放在成员变量上，作者  
**1.**首先分析几种不同成员变量的内存布局以及存取方法，例如static与nonstatic，  
**2.**然后介绍了在继承下的情况，这部分就比较复杂，首先是最简单的具体继承（即不存在多态）下的内存布局，并且介绍了这种方式会存在 **一些坑**    
**3.**增加多态：介绍这样所带来的成本，以及实现的方式，并介绍了多重继承以及虚拟继承的实现方式，以及一些容易踩入的坑。   
**4.**作者最后耐心的做了很多实验，不得不佩服！！以验证前面理论的正确性。
###一些重点的介绍
**1.成员变量的存取**：static自然不用说，访问static member与类并没有关系（当然语法上是有关系的）因此可以带来的负担并不重。 nonstatic member：这类数据，在编译时期，其在一个类中的偏移量就已经确定，因此调用这个也没有造成太多的损耗（这里是指在没有继承的情况下）  
**关于指向data member的指针**： 这个比较有趣，也就是上文所说的在类里的偏移量offset详情可以看书中p98，总的来说就是你在使用offset时，需要将得到的offset的值 *减1*使用，目的是为了解决这种情况：有两个指向data member的指针，一个初始化了为0，另一个指向了这个class中第一个data member，因此问题出现了，第一个成员的偏移是0，而之前的那个空指针也是0，因此需要区分这种情况，将真实的offset都加了一~    
作者在这之前也留了一道题：通过实例访问成员变量与通过类指针方位成员变量在效率上的区别：这里主要区分在如果这个成员不是继承得来的吗，那么在编译时期就可以确定，但如果这个成员是继承于virtual base class，那么就需要增加一层间接访问，需要推迟到执行期来执行，这个在后面会讲到。  
**2.继承对于内存布局的影响**：单一继承：书中又叫具体继承，这里不存在多态的使用，因此在存取上不会存在一些额外的负担，但是会造成两个坑：  
> 1.在派生类进行初始化时，需要基类的初始化成本，这个会对效率产生损耗。   
> 2.padding损耗，这个比较重要，之前提到过，编译器会对类进行一些内存的增补，将其进行字节上的对其，如果为了能够把类变得很层次化，将其分割成为好几层，这样可能会造成很大的内存膨胀……后果是得不偿失。因此在进行分层时一定要仔细考虑。书中的p106页介绍了c++中的复制是bitwise的，因此就需要在派生类中原封不动地保留基类的样子，否则会产生一些蛋疼效果。。   


**3.增加多态后的影响**增加多态后的成本是：1.增加了一个vtbl，2.加强构造/析构函数。关于vbtl在内存中的放置，c++标准并没有给出规定，任何位置都可以，一般好像都是放置在前端的。  
在了解了实现成本后根据造成影响的大小不同分为：**多重继承与虚拟继承**  
>**多重继承：** 这里主要介绍了多重继承下的内存布局，最左面的排在最上面，后面的依次排开，了解这个内存布局的主要意义在于进行指针转换时，需要对指针进行向上或者向下的调整（个人感觉，其实这部分在第四章中的函数调用时用的更多，因为要进行this指针的调整）  
>**虚拟继承**  一iostream这类为例，哈哈这个不就是设计模式中的**装饰器模式**看来了解了解设计模式还是很有用的。首先介绍了几种获得virtual base class地址的不同的实现机制：最简单的是在类中背负一个virtual base class的指针，这样的坏处是成本不固定，以及虚拟继承链的增长会导致简介存取层次的增加。第二种是微软的实现方式：增加了一个virtural base 表，作者所推崇的方式是通过将vbtl的索引为负时指向virtual base class，比较巧妙。

**实验**这部分主要是验证作者之前的理论，有兴趣的可以仔细的看看。

---

###总结

这一章将类成员的内存分布说的很清楚，也告诉了很多实战中的坑，对于今后在设计类时有很大的帮助……最主要的是我之前一直看不懂的一篇文章终于看懂了TT


当然这只是我个人的总结，才疏学浅，肯定会存在一些错误，希望路过的各位前辈拍砖指正！~
