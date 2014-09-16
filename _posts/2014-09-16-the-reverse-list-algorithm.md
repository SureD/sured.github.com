---
layout: post
title: "单向链表反转的几种操作方法"
description: "单向链表反转"
category: 算法
tags: [算法][数据结构]
--- 


#单向链表反转的几种操作方法
-----

今天看了关于链表的几个操作，其中算法导论中10.2-7中要求使用非递归的方法进行链表的反转操作，于是就尝试着写，研究了一下，除了非递归的方法外，递归当然也可以解决，并且可以分为单参数与双参数，在非递归算法中，可以使用两个指针变完成反转操作（头插法），下面贴出代码：
首先定义Node结构：
{% highlight c++ linenos %}
struct Node {
	Node *next;
	int x;
};
{% endhighlight %}
非递归的反转方法：
{% highlight c++ linenos %}
Node* RevereseLa(Node * head) //非递归
{
	if(head==NULL||head->next==NULL)
	{
		return head;
	}
	Node *prv=NULL;
	Node *cur=head;
	Node *tmp;
	while(cur->next)
	{
		tmp=prv;
		prv=cur;
		cur=cur->next;
		prv->next=tmp;
	}
	cur->next=prv;
	head->next=cur;
	return head;
}
{% endhighlight %}
从非递归方法可以看出其中采用了三个指针，一个保存cur当前指针，prv保存，tmp作为临时指针存储，其过程利用画图比较容易理解。每次反转cur与prv之间的指针即可。注意此处使用了三个指针，下面利用头插法，即可实现两个指针反转：
{% highlight c++ linenos %}
Node* Revereseh(Node *head) //头插法  仅利用两个指针
{
	Node *nnode= new Node();
	nnode->next=NULL;
	Node *p = head->next;
	//Node *tmp;
	while(p!=NULL)
	{
		head->next=p->next;
		p->next=nnode->next;
		nnode->next=p;
		p=head->next;
	}
	head->next=nnode->next;
	delete nnode;
	return head;
}
{% endhighlight %}
这个方法是在待字闺中群中一个同学说明的，之前实现时仍然使用了一个临时指针，但是可以看到在新建了node后，head->next始终没有利用到，因此可以利用head->next当作临时存储，然后利用指针p遍历链表，最终将head指向正确的位置即可。
>此处的理解可以看作是重新建立一个新的链表，利用nnode存储上一个节点的指针，然后遍历链表，将当前遍历的节点的下一个节点存到临时区域，然后将当前节点指向上一个节点（nnode）然后更新nnode，与当前节点，变进行下一次循环。

---
从以上的两个设计中可以看出，这种循环的设计应当注意两点，一点是初始条件的设置，在算法一中其初始条件为prv为空，cur为头节点；算法二中初始条件为nnode为空，p为链表的第一个节点
二是寻找循环不变式，即固定的规律。
---
一下两种分别为单参数与双参数的递归法
{% highlight c++ linenos %}
Node *RevereseLr1(Node *node) //单参数递归
{
	if(node==NULL||node->next==NULL)
		return node;
	Node *nextnode=node->next;
	node->next=NULL;
	Node *reversenode = RevereseLr1(nextnode);
	nextnode->next=node;
	return reversenode;
}
{% endhighlight %}
递归的调用比较巧妙，首先先走到链表的末端，然后不断的返回修改next的指向，我的理解是，这个递归相当于一个栈的作用，每次都保留当前的结点状态，当递归结束后，在不断的从末端出栈，因而保证了反向的顺序。因此根据栈的理解，没遍历到一个节点，便将这个节点的next节点压栈（及递归）然后将其next指向NULL，直到遍历到末端时，开始出栈，便将上一个（末端的为head）指向当前出栈的节点，知道最后一个出完。 值得注意的是，此处返回的不是head指针，而是第一个节点。
{% highlight c++ linenos %}
void RevereseLr(Node *p,Node *head) //双参数递归
{
	if(p->next==NULL)
	{
		head->next=p;
		return;
	}
	RevereseLr(p->next,head);
	p->next->next=p;
	p->next=NULL;
}
{% endhighlight %}
此处为双参数的方法，即将head传入，到了末端时，将head指对。这个程序比较简化，我是参考一片博客上写的，觉得很好！
以下是测试代码：
{% highlight c++ linenos %}
int main()
{
	Node* head = new Node();
	Node* tmp = head;
	for (int i = 0; i < 10; i++)
	{
		tmp->next=new Node();
		tmp->next->x=i;
		tmp->next->next=NULL;
		tmp=tmp->next;
	}
	//head=RevereseLa(head);// a非递归算法返回的为head->next
	//tmp=head->next;
	//////////////////////
	//RevereseLr(head->next,head);// 双参数的递归算法
	//tmp=head->next;
	////////////////
	//tmp=RevereseLr1(head); //单参数的递归算法
	//////////////
	tmp=Revereseh(head);  //头插法反转 利用两个指针
	tmp=tmp->next;
	while(1)
	{
		
		if(tmp->next==NULL)
			break;
		std::cout<<tmp->x<<std::endl;
		tmp=tmp->next;
	}
}
{% endhighlight %}
可以看到，使用非递归的方式解决反转方式的空间复杂度更低，而递归的用到了栈，因此比较消耗内存，其复杂度均为O（n）递归解决一些抽象问题时代码简洁优美，但不见得性能上很好。
   

