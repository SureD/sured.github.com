---
layout: post
title: "RoundRobin线程调度算法"
description: "C++"
category: C++
tags: [C++]
--- 
###今天做了一道关于线程调度的题，采用的roundrobin调度算法，求出每个线程的等待时间

输入arrive代表着线程到达时间，run代表每个任务执行完所需要的时间，n代表线程数量，q代表了每个时间片最大的耗时

当时也没什么想法，就想直接模拟这个cpu调度策略，等待所有任务执行完后求出等待时间
{% highlight c++ linenos %}
#include<iostream>
#include <queue>
using namespace std;
int a[]={0,1,3,9};
int run[] = {2,1,7,5};
float waitingTimeRobin(int *arrival, int *run, int n, int q)
{
	// WRITE YOUR CODE HERE
	int i=0;
	queue<int> qq;
	int j=0;
	int task = -1;
	int *wt = new int[n];
	int *lst = new int[n];
	for(int tt=0;tt<n;tt++){
		wt[tt] = 0;
		lst[tt] = arrival[tt];
	}
	while(1){
		for(;j<n&&arrival[j]<=i;++j){
			if(arrival[j]==i){
				qq.push(j++);
				break;
			}
		}
		if(task == -1 && !qq.empty()){
			task = qq.front();
			qq.pop();
		}
		if(task!= -1){
			int tmp = i+q;
			wt[task] += (i-lst[task]);
			
			while(i<tmp&&run[task]>0){
				i++;
				run[task]--;
			}
			lst[task] = i;
			for(;j<n&&arrival[j]<=i;j++){
				qq.push(j);
			}
			if(run[task]>0)
				qq.push(task);
			task = -1;
		}else{
			i++;
		}
		if(qq.empty()&&j>=n)
			break;
	}
	int total = 0;
	for(int kk=0;kk<n;++kk)
		total += wt[kk];
	float ans = (float)total/(float)n;
	return ans;
}
int main(int argc, _TCHAR* argv[])
{
	float ans = waitingTimeRobin(a,run,4,2);
	cout<<ans<<endl;
	return 0;
}
{% endhighlight %}

如果有更好的想法欢迎拍砖指正呐~
代码当时写的很糙……后来也没有改了……
样例1：     
 arrival = [0,1,4] run = [5,2,3] n = 3, q = 3    
 output = 2.3333333   
样例2：     
 arrival = [0,1,3,9] run = [2,1,7,5] n =4, q = 2    
 output = 1.0000000









