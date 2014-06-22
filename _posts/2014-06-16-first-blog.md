---
layout: post
title: "算法导论读书笔记"
categories:
- 
tags:
- 


---
---
#算法导论读书笔记#
---
##堆排序
   
最近在读算法导论，第一次看时总是走马观花，缺少总结理解，最也搭上了博客于是就总结总结自己的理解~顺便锻炼下自己的写作能力。  
堆的定义：可以视作一个完全二叉树，并且数的每一层都是的满的。其中有几个定义：
> 结点在树中的高度：是结点向下到某个叶结点的最长简单路径的边的条数  
> 树的高度即为根的高度      


堆的左右儿子的求法，可以将堆看作一个数组，下标从1开始，结点i的左右儿子分别为：*left=2i；right=2i+1*  


* 在程序中其求法可采用位运算左移以及低位+1来实现。<br>

堆的分类 
  *  最大堆：根结点的值最大，并且其每个结点都满足这个条件。
  *  最小堆：根结点的值最小，并且每个结点都依次满足此条件。
  *  堆的一些相关计算：
   * 高度为h的堆中：最多的元素个数：全满的情况
   <img src="http://chart.googleapis.com/chart?cht=tx&chl=  2^0+2^1\cdots+2^h=2^{h+1}-1" style="border:none;">
最少元素个数：只含有一个叶结点的情况
<img src="http://www.forkosh.com/mathtex.cgi? 2^0+2^1\cdots+2^{h-1}+1=2^{h}">  
* 含n个元素的堆的高度为  <img src="http://www.forkosh.com/mathtex.cgi? ceiling(lgn)">  通过最多最少的元素求<img src="http://www.forkosh.com/mathtex.cgi? log">便可得。
* 当用数组表示存储了n个元素的堆时，叶子结点的下标为：  
根据前两个性质，可以得出此堆的高度为<img src="http://www.forkosh.com/mathtex.cgi? ceiling(lgn)">因此叶子结点的下标可以这样计算：
<img src="http://www.forkosh.com/mathtex.cgi? 2^{ceiling(lgn)-1}+1,2^{ceiling(lgn)-1}+2,\cdots,n">即
<img src="http://www.forkosh.com/mathtex.cgi? ceiling(n/2)+1,ceiling(n/2)+2,\cdots,n">
  
---- 
### 保持堆的性质
即为保持最大堆或最小堆得性质。  
* 保持最大堆：方法即为从一个给定的结点下标的父节点开始，与其儿子进行比较，若儿子大于其父结点则进行交换，交换后，有可能破坏其性质，则继续以此交换后的结点作为父节点进行迭代。
* 保持最小堆：方法与最大堆相同，不同的是比较方法相反。
* 时间复杂度的计算：以保持最大堆为例，作用在一个以结点i为根、大小为n的子树上：其中进行判断大小的时间复杂度为<img src="http://www.forkosh.com/mathtex.cgi? \Theta(1)"> 然后考虑迭代过程的代价，最坏的情况是在最底层恰好半满的时候，即左子树或右子树达到最多的情况，其大小为<img src="http://www.forkosh.com/mathtex.cgi?2n/3">
  * 证明： 树的大小为n，设当左子树的size达到最大时，其中最底层半满，其个数为x ，设其高度为h
  因此  
 <img src="http://www.forkosh.com/mathtex.cgi?x+x=2^{h}">即若此树为满树时叶子结点的个数  
 那么<img src="http://www.forkosh.com/mathtex.cgi?2(x+x)-1=2^{h+1}-1">即为次数为满树时结点的个数  
 根据假设可得到 <img src="http://www.forkosh.com/mathtex.cgi?2(x+x)-1=n+x">
 因此左子树的size为<img src="http://www.forkosh.com/mathtex.cgi?(n+x-1)/2"> 可以得到
 <img src="http://www.forkosh.com/mathtex.cgi?leftsize\approx2n/3">
>网上的一个比较简单的解释为：一颗完全二叉树，最下层半满的时候，根节点的左子树节点数就是右子树的2倍，所以左子树节点数为2/3n	 

  * 因此其时间复杂度为：<img src="http://www.forkosh.com/mathtex.cgi?T(n)\le T(2n/3)+\Theta(1)">
  可求的<img src="http://www.forkosh.com/mathtex.cgi?T(n)=O(lgn)">

---
###建堆
先提出一个结论：
在任意高度h上，至多有<img src="http://www.forkosh.com/mathtex.cgi?floor(n/2^{h+1})">个结点，其证明可参考：[堆排序](http://www.cnblogs.com/algorhythm/archive/2012/08/28/2659693.html)
建堆的方式即从底层的非叶子结点开始迭代调用保持最大堆的算法，其运行时间的界为<img src="http://www.forkosh.com/mathtex.cgi?O(n)">既可以在线性时间内建成一个最大（最小）堆。

---
###堆排序
这个比较好理解，即首先先建堆，然后将其根结点与数组的最后一个元素互换，然后将数组待排序的size-1，然后不断的在根结点进行保持堆得算法运算，不断的将保持堆后的根结点放入数组的最后一个元素，最后即完成排序。其时间复杂度为<img src="http://www.forkosh.com/mathtex.cgi?O(nlgn)">

----
#### C++实现

{% highlight java linenos %}
#include "stdafx.h"
#include<assert.h>
#include <stdio.h>
#include<math.h>
class Heap
{
public:
	// construction & dec
	Heap(){};
	~Heap(){};
	//method
	bool init(int *pArray,int arraySize){
		assert(pArray);
		m_pArray = pArray;
		m_iArraySize = arraySize;
		return true;

	};
	// get index with shift  heapsize = arraysize+1
	const int parent(int iHeap_index) {return iHeap_index/2-1;};
	const int left(int iHeap_index) {return 2*iHeap_index-1;};
	const int right(int iHeap_index) {return 2*iHeap_index;};
	// MAX-HEAPIFY(A,i)
	void maxHeapify(int iHeap_index){
		int largestIndex; 
		int iArray_index = iHeap_index-1;
		int leftArrayIndex = left(iHeap_index);
		int rightArrayIndex = right(iHeap_index);
		if (leftArrayIndex<m_iArraySize&&m_pArray[leftArrayIndex]>m_pArray[iArray_index])
		{
			largestIndex = leftArrayIndex;
		}else 
			largestIndex = iArray_index;
		if (rightArrayIndex<m_iArraySize&&m_pArray[rightArrayIndex]>m_pArray[largestIndex])
		{
			largestIndex = rightArrayIndex;
		}
		if (largestIndex!=iArray_index)
		{
			int temp = m_pArray[iArray_index];
			m_pArray[iArray_index] = m_pArray[largestIndex];
			m_pArray[largestIndex] = temp;
			maxHeapify(largestIndex+1);

		}


	};
	// BUILD MAX HEAP 
	void buildMaxHeap(){
	int istartNode = m_iArraySize/2;
	for (int i = istartNode; i>0 ; i--)
	{
		maxHeapify(i);
	}
	};
	// sort the heap
	void heapSort(){
		int itempSiez = m_iArraySize;
		buildMaxHeap();
		for (int i = m_iArraySize; i > 1; i--)
		{
			int temp = m_pArray[i-1];
			m_pArray[i-1] = m_pArray[0];
			m_pArray[0] = temp;
			--m_iArraySize;
			maxHeapify(1);


		}
		m_iArraySize=itempSiez;
	};
	// print
	void printarray(){
		for (int i = 0; i < m_iArraySize; i++)
		{
			printf("%d\n",m_pArray[i]);

		}
	};


private:
	int* m_pArray;
	int m_iArraySize; 
};


int _tmain(int argc, _TCHAR* argv[])
{
	//int a[]={16,4,10,14,7,9,3,2,8,1};
	int a[]={4,1,3,2,16,9,10,14,8,7,7};
	Heap heapp;
	if(heapp.init(a,11))
	{
		//heapp.maxHeapify(2);
		//heapp.buildMaxHeap();
		heapp.heapSort();
		heapp.printarray();
	}

   return 0;
}
{% endhighlight %}