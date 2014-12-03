---
layout: post
title: "设计模式之策略模式"
description: "strategy pattern"
category: DesignPatterns
tags: [DesignPatterns]
---

###Head First 设计模式
---



#####随便在前面写点什么 :) 
最近读了《Head First 设计模式》 真是一本非常好的书，读起来有种相见恨晚的感觉。恨不得一口气读完，特别有趣，里面的故事让我想起来以前第一次写CPP时，准备自己创建一个类时手足无措的场景，然后就是在primer上找找看看……实在是太囧了 Orz..


#####策略模式

书中讲的第一种模式：  
**策略模式的定义：定义算法族，分别封装起来，让它们之间可以相互替换，此模式让算法的变化独立于使用算法的客户。**  
以上是书中给出的精确定义，毕竟不是专家~~，我就抄下来啦，下面说说我的理解吧：  
书中举了一个玩具工厂的例子，需要建立一个鸭子类DUCK，但是呢，这个鸭子有着不同的类别，比如有下面几种：  
1. 真鸭子： 会飞，会呱呱叫  
2. 橡皮鸭子： 不会飞，会呱呱叫   
3. 木头鸭子： 不会飞，也不回呱呱叫  
4. .......   
首先，我们想到的是最简单的方法，先定义一个鸭子的抽象类DUCK，然后一个个继承呗，这种方法呢，一个两个还好，要是突然工厂生产了很多不同种类的鸭子，好嘛，一下子就要写好多，当然不一定是写好多，可能是到处都复制粘贴一样的代码，这样实在是太不好了。  
那该怎么办呢？  
书中告诉我们要学会：  
1. **多用组合，少用继承**;  
2. **针对接口编程，不要针对实现编程**  

那我们重新思考下上面的问题出在哪儿了呢？  
第一我们肯定是针对实现编程没跑了……说到接口呢，是有啊，就只有抽象类里的那几个（fly，quack），然后来一种鸭子，我们就实现一种。  
当然我们之所以这样是因为我们一直在继承。。。看来我们1，2毛病都犯了。   
既然知道了毛病，那么应该怎么改，既然要针对接口来编程，就要找出接口喽，so easy，既然飞的方式又很多种，那么fly就是接口了，同理叫的方式也应当是接口了quack。但是。。。。我们不是已经在抽象类DUCK中定义了？哈，这就是不一样的地方了，我当时也是这么想的，那么回头看看刚开始策略模式的定义吧！我们不仅要找出接口，还要把它封装起来！因此我们将fly和quack抽象出来，变成一个类flybehavior，quackbehavior（书中用的是Java，将其变为接口interface……Java我也只是懂一点点，所以不太清楚，在C++中我感觉只能将这两个接口定义为基类了吧，如果有更好的方法请一定告诉我~毕竟我也只是freshman），然后对这个类进行继承覆盖。从而实现不同飞行方式，那么在完成了这个时候就该组合出场了！让你看看什么是多态的威力！！ Talk is cheap，show me your code（我最近在博客里很少贴code，主要原因是我的这个插件有点问题，一贴代码我的邮箱里就会有一对warning……然后gist也被墙了！！！真是气氛 #吐槽结束）我就随手写个片段，简单起见，就针对飞行吧：  
{% highlight c++ linenos %}  
class Duck{
	Flybehavior flybehavior；
public：
	fly();
	getflybehavior(Flybehavior&);	
}
class xxDuck:Duck{
	fly(){flybehavior.fly()};
	getflybehavior(Flybehavior& flyt){flybehavior = flyt};
}
class Flybehavior{
	fly();
}
class xxFlywithwings:Flybehavior{
	fly(){//do somethingh you want};
}

int main()
{
	XXDuck duck;
	xxFlywithwings flyfly；
	duck.getflybehavior(flyfly)；
	duck.fly();
}  
{% endhighlight %}

终于敲完了（……没有自动补全真是。。。）要是有什么错误请见谅，上面的代码什么意思呢，我在Duck类中建立了一个flybehavior的成员，从而将飞行的算法交给这个成员来实现（这又牵扯到另外一个概念了，时间原因，我下次在写吧~），并且getflybehavior这个函数，又可以实现动态绑定的功能，这样就更加灵活了！当我想让木头鸭子会飞的时候，我只需调用下这个函数即可！体会到多态的魔力了吧！！真是棒!
今天时间不多了，还有别的事情要做，不过写这个有种期末复习认真做笔记的即视感，继续努力吧，这里我还留下了几个没有写完，比如委托（这个实在是太猛了，前几天无意中看到了一个[Member Function Pointers and the Fastest Possible C++ Delegates](http://www.codeproject.com/Articles/7150/Member-Function-Pointers-and-the-Fastest-Possible),被作者的研究折服了！虽然估计最近在实验室中用不上，但是看看高手的理解总是有种醍醐灌顶的感觉，推荐~），还有我想起来上学期在改写一个系统的时候也遇到了这种模式，我当时还不清楚这个设计叫策略模式，不过当时就感受到方便了~下次再写吧！

---
参考文献：  
[1]. Freeman E, Freeman E, Sierra K, et al. Head First 设计模式[J]. 2007.  


---

最近事情很多，所以写的时候都比较仓促，肯定会有些错误以及自己理解不到位的情况，希望大家发现或能够告诉我~谢谢，一起进步吧~  

ps.最近正在做一个很有意思的小项目，用到了mongodb，不过我之前没接触过，正在学习中，有了解这方面的大牛希望能够多多指教指教~ please email me thanks~ ：）



SureD @2014.12.03 night
