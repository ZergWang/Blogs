给出一个图（可有向亦可无向），每条边都有一个长度，问从一个节点走到另一个节点的最短路径（即走过的边的长度之和最短）

# Floyd算法

当要求出一个图中所有的两节点间的最短路径时我们可以用Floyd算法，该算法用DP实现。

定义dis[i,j,k]表示从点i到j经过k个点的最短路径，那么对于当前的dis[i][j][k]我们有两种情况：

1.最短路径经过了k点，则dis[i,j,k]=dis[i,k,k-1]+dis[k,j,k-1]

2.最短路径没有经过k点，则dis[i,j,k]=dis[i,j,k-1]

由此我们可以得出状态转移方程：dis[i,j,k]=min{dis[i,k,k-1] + dis[k,j,k-1],dis[i,j,k-1]}

边界条件：dis[i,j,0]=len[i][j]    (如果i,j之间无边存在，可以赋一个极大值)

优化：如果我们将k循环放在最外层，那么dis就相当为一个滚动数组，那么我们可以将数组降一维。

该算法的时间复杂度为O(n^3)。

以下是程序：
```cpp
#include <fstream>
using namespace std;
ifstream fin("floyd.in");
ofstream fout("floyd.out");

const int oo=100000007;
int n,m,dis[505][505];
void init();
void work();
int main()
{
init();
work();
return 0;
}

void init()
{
fin>>n>>m;
for (int i=0;i<=n+2;++i)
for (int j=0;j<=n+2;++j) //初始化
dis[i][j]=oo;

for (int i=1;i<=m;++i)
{
int a,b,len;
fin>>a>>b>>len; //读入
dis[a][b]=len;
}
}

void work()
{
for (int k=1;k<=n;++k)
for (int i=1;i<=n;++i)
for (int j=1;j<=n;++j)
dis[i][j]=min(dis[i][j],dis[i][k]+dis[k][j]); //DP
fout<<dis[1][n]<<endl;
}
```
<br/><br/>

# Dijkstra算法

原理：当dis[i,j]+dis[j,k]<dis[i,k]时dis[i,k]赋值为dis[i,j]+dis[j,k]    （称为“松弛”）

对于每个已经确认了路径长度的点，我们找出所有它连接的点，比较并松弛就可以了。

以下是程序：
```cpp
#include <fstream>
#include <vector>
using namespace std;
ifstream fin("dijkstra.in");
ofstream fout("dijkstra.out");

struct data{int b,c;}; //.b代表节点名 .c代表边长
const int oo=10000007;
vector <data> len[1005];
int n,m,dis[1005]; //dis[i]表示从点1走到点i的最短路径
bool f[1005];

void init();
void work();
void inpu(int a,int b,int t);
int main()
{
init();
work();
return 0;
}

void inpu(int a,int b,int c)
{
data cmp;
cmp.b=b;
cmp.c=c;
len[a].push_back(cmp); //记录每一个与点a相连的点
}

void init()
{
fin>>n>>m;
for (int i=1;i<=m;++i)
{
int a,b,c;
fin>>a>>b>>c;
inpu(a,b,c);
inpu(b,a,c); //无向图双向读入
}
}

void work()
{
for (int i=1;i<=n;++i) dis[i]=oo;
dis[1]=0; //初始化
for (int i=1;i<=n;++i)
{
int max=oo,p;
for (int j=1;j<=n;++j)
if (!f[j] && max>dis[j]) 

//找一个没有访问过的且已经有最优解的点开始做松弛（何为最优？当前所有点中离起点距离最小的点）
{
max=dis[j];
p=j;
}
f[p]=1;
for (int j=0;j<=len[p].size();++j)
{
int next=len[p][j].b;
int cost=len[p][j].c;
dis[next]=min(dis[next],dis[p]+cost); //松弛
}
}
fout<<dis[n]<<endl;
}
```
<br/><br/>

# SPFA算法

因为SPFA算法是Bellman-ford算法的升级版，在这里就不谈

Bellman-ford。

SPFA算法就是从一个点开始，对与它相邻的点逐一进行松弛，与宽搜差不多，但是SPFA算法可以解决负环问题，比如说这样的一个图：

![](最短路径（Optimal%20Path）_1.jpg)

如果这个时候要我们求最短路径，那么我们可以一遍又一遍地绕这个环走，这样程序就出错了，因此在SPFA算法中如果一个图中一共有n个点，其中一个点被改进了n次，那么这就说明至少有1个点改进这个点2次，负环就被检测出了。
```cpp
#include <fstream>
#include <queue>
#include <vector>

using namespace std;
ifstream fin("SPFA.in");
ofstream fout("SPFA.out");

struct data
{
int B,D;
};

const int oo=1000000007;
int N,M,cnt=0;
vector <data> edg[1005];
int dis[10005];
int pd[10005];
bool vis[10005];
queue <int> q;

void ins(int A,int B,int D);
void init();
void SPFA();

int main()
{
init();
SPFA();
return 0;
}

void ins(int A,int B,int D)
{
data cmd;
cmd.B=B;cmd.D=D;
edg[A].push_back(cmd);
}

void init()
{
fin >> N >> M;
for (int i=1;i<=M;++i)
{
int A,B,D;
fin >> A >> B >> D;
ins(A,B,D);
}
}

void SPFA()
{
for (int i=1;i<=N;++i) dis[i]=oo;
dis[1]=0;vis[1]=1;
q.push(1); //初始化与Dijkstra算法类似
while (!q.empty())
{
int now=q.front();
q.pop();
for (int i=0;i!=edg[now].size();++i)
{
int nex=edg[now][i].B;
int cost=edg[now][i].D;
if (dis[now]+cost<dis[nex])
{
dis[nex]=dis[now]+cost;
++pd[nex];
if (pd[nex]>=N) //判断负环
{
fout<<"No solution"<<endl;
return;
}
if (!vis[nex])
{
q.push(nex);
vis[nex]=1;
}
} //无论该点是否访问过都要进行松弛
}
vis[now]=0; //因为该点已经弹出队列，因此访问应改为false
}
fout <<dis[N] <<endl;
}
```