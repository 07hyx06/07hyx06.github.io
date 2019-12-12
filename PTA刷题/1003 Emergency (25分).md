# 1003 Emergency (25分)
## 题意：
一个国家有```N```个城市，每个城市都有一定数量的救援队，地图共有```M```条边。如果城市```c2```着火，那么从城市```c1```赶到```c2```的最短路径有几条？所有最短路径中，能集结的最多救援队数量是多少？<br>
## 题解：
既然是求最短路，肯定是要打一个迪杰斯特拉的板子了。在更新最短路径长度的同时，更新最短路径条数与集结的救援队数量即可。看一眼题目的数据量发现地图很小，```N<=500```，那么用邻接矩阵存图就好了。<br>
首先开一个结构体数组维护```最短路径长度，最短路径条数与集结的救援队数量```：
```C
struct path{
	int l,np,nr;//最短路径长度，最短路径条数，最多可集合的救援队数
}p[maxn];
```
各个数组含义如下：
```C
int num[maxn];//每个城市的救援队数量 
int mp[maxn][maxn];//地图 
int vis[maxn];//记录节点是否被访问过
```
下面是关键之处，即维护```最短路径长度，最短路径条数与集结的救援队数量```的过程，分为两种情况讨论：<br>
1. 如果发现有一条到```i点```更短的路径，那么应当更新最短路长度为```p[i].l=mp[idx][i]+minl```，此时的最短路径数量应当与源点到```idx点```的数量相同，因此更新为：```p[i].np=p[idx].np```，最后更新救援队数量：```p[i].nr=p[idx].nr+num[i]```。
2. 如果发现经过```idx点```到```i点```的路径长度与之前的最短路径相同，需要增加最短路径数量```p[i].np+=p[idx].np```，维护救援队数量```p[i].nr=max(p[i].nr,p[idx].nr+num[i])```，**注意这里是取```max```**。

## AC代码：
```C
#include <bits/stdc++.h>
#define maxn 510
#define inf 9999999
using namespace std;
int n,m,c1,c2;
int num[maxn];//每个城市的救援队数量 
int mp[maxn][maxn];//地图 
int vis[maxn];//记录节点是否被访问过 
struct path{
	int l,np,nr;//最短路径长度，最短路径条数，最多可集合的救援队数 
}p[maxn];
void init()
{
	for(int i=0;i<n;i++)
	{
		if(mp[c1][i]) 
		{
			p[i].l=mp[c1][i];
			p[i].np=1;
		}
		else 
		{
			p[i].l=inf;
			p[i].np=0;
		}
	}
	p[c1].l=0;
	p[c1].np=1;
	p[c1].nr=num[c1];
	vis[c1]=1;
}
void dijkstra()
{
	init();
	while(1)
	{
		int idx=-1,minl=inf;
		for(int i=0;i<n;i++)//找最小边 
		{
			if(vis[i]==0&&p[i].l<minl)
			{
				minl=p[i].l;
				idx=i;
			}
		}
		if(idx==-1) break;
		vis[idx]=1;
		p[idx].nr=max(p[idx].nr,p[c1].nr+num[idx]); 
		for(int i=0;i<n;i++)//更新最短距离 
		{
			if(mp[idx][i])
			{
				if(p[i].l>mp[idx][i]+minl)//发现更短的路径 
				{
					p[i].l=mp[idx][i]+minl;
					p[i].np=p[idx].np;//覆盖最短路条数 
					p[i].nr=p[idx].nr+num[i];//更新救援队数量 
				}
				else if(p[i].l==mp[idx][i]+minl)//最短路长度不变 
				{
					p[i].np+=p[idx].np;//增加最短路条数 
					p[i].nr=max(p[i].nr,p[idx].nr+num[i]); 
				}
			}
		}
	}
}
int main()
{
	scanf("%d%d%d%d",&n,&m,&c1,&c2);
	for(int i=0;i<n;i++) scanf("%d",&num[i]);//每个城市救援队数量
	for(int i=0;i<m;i++) 
	{
		int tx,ty,tw;
		scanf("%d%d%d",&tx,&ty,&tw);
		mp[tx][ty]=mp[ty][tx]=tw;
	}
	dijkstra();
	printf("%d %d",p[c2].np,p[c2].nr);
}
```
