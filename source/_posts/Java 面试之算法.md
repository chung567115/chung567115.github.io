---
title: Java面试之算法
categories: 
  - 笔经面经
tags:
  - Java
  - 校招
date: 2018-07-21 11:11:49
---

> 笔经面经归纳汇总：算法

<!-- more -->

# Java面试之算法
##  排序算法  
![排序](/29-1.png)

 &emsp;&emsp;名词解释：  

- n: 数据规模
- k: “桶”的个数
- In-place: 占用常数内存，不占用额外内存
- Out-place: 占用额外内存

###  快排的Partition函数与归并的Merge函数  
```c
// 快速排序
void QuickSort(T a[], int low, int high) {
    if (low < high) {
        /************ Partition 函数**************/
        pivot = a[low];
        i = low; j = high;
        while (i < j) {
            while (i < j && a[j] >= pivot) {
                j--;
            }
            a[i] = a[j];
            while (i < j && a[i] <= pivot) {
                i++;
            }
            a[j] = a[i];
        }
        a[i] = pivot;
        /*****************************************/
		
        // 对子序列快排
        QuickSort(a, low, i-1);
        QuickSort(a, i+1, high);
    }
}


// 归并排序
void MergeSort ( T a[], int low, int high ) {
    if ( low >= high ) {
        return;
    } else {
        mid = (low + high) / 2;
        MergeSort(a, low, mid);
        MergeSort(a, mid+1, high);
        Merge(a, low, mid, high);
    }
}
/****************** Merge 函数*********************/
void Merge(T a[], int low, int mid, int high) {
    // 归并到 b[]
    i = low; 
    k = low;
    while(i <= mid && j <= high ) {
        if(a[i] <= a[j]) { 
            b[k] = a[i]; i++; 
        } else { 
            b[k] = a[j]; j++; 
        }
        k++;
    }
    // 归并剩余元素
    while(i <= mid) {
        b[k++] = a[i++];
    }
    while(j <= high) {
        b[k++] = a[j++];
    }
    // 从 b[]复制回 a[]
    a[low..high] = b[low..high];
}
/*************************************************/
```
> 排序详解可参考博文 https://www.cnblogs.com/onepixel/articles/7674659.html

## 二分查找
```c
int BinarySearch( DataType a[], int low, int high, DataType x ) {
    if ( low>high ) {
        return -1; // 查找失败
    }
    mid = (low + high) / 2; // 折半
    if ( a[mid] == x ) {
        return mid; // 找到
    } else if ( x < a[mid] ) {
        return BinarySearch(a, low, mid - 1, x);
    } else {
        return BinarySearch(a, mid + 1, high, x);
    }
}
```

## 图的DFS算法与BFS算法  
```c
// DFS 深度优先搜索
void DFSTraverse(Graph G) {
    visited [0..G.vexnum-1] = false; // 初始化访问标志为未访问(false)
    for(v = 0; v < G.vexnum; v++) {
        if(!visited[v]) {
            DFS(G, v); // 从未被访问的顶点开始 DFS
        }
    }
}
void DFS(Graph G, int v) {
    visit ( v ); 
    visited [v] = true; // 访问顶点 v 并作标记
    for(w = FirstAdjVex(G,v); w >= 0; w = NextAdjVex(G,v,w)) {
        if (!visited[w]) {
            DFS(G, w); // 分别从每个未访问的邻接点开始 DFS
        }
    }
}


// BFS 广度优先搜索
void BFSTraverse(Graph G) {
    visited[0..G.vexnum-1] = false; // 初始化访问标志为未访问(false)
    InitQueue(Q);
    for(v = 0; v < G.vexnum; v++) {
        if(!visited[v]) {
            // 从 v 出发广度优先搜索
            visit(v); 
            visited [v] = true;
            EnQueue(Q, v);
            while(!QueueEmpty(Q)) {
                DeQueue(Q, u);
                for(w = FirstAdjVex(G, u); w >= 0; w = NextAdjVex(G, u, w)) {
                    if(!visited[w]) {
                        visit(w); 
                        visited[w] = true;
                        EnQueue(Q, w);
                    }
                }
            }
        }
    }
}

```

## 最小生成树Kruskal算法、Prim算法
- Kruskal：不构成环的情况下，每次选取最小边（边较少时特别有效）。
- Prim：记 $V$ 是连通网的顶点集， $U$ 是求得生成树的顶点集， $TE$ 是求得生成树的边集：
	1. 开始时， $U={v_0}$， $TE = \Phi$；
	2. 计算 $U$ 到其余顶点 $V-U$ 的最小代价，将该顶点纳入 $U$，边纳入 $TE$；
	3. 重复 2 直到 $U=V$。

> 同一个连通网的最小生成树可能是不唯一的，但其代价都是最小(唯一的)。

## 最短路径Dijkstra算法  、Floyd算法
- Dijkstra：求一个顶点到其他各顶点的最短路径：
	1. 初始化：用起点 v 到该顶点 w 的直接边(弧)初始化最短路径，否则设为 $\infty$；
	2. 从未求得最短路径的终点中选择路径长度最小的终点 $u$：即求得 $v$ 到 $u$ 的最短路径；
	3. 修改最短路径：计算 $u$ 的邻接点的最短路径，若$(v,…,u)+(u,w)<(v,…,w)$，则以
	$(v,…,u,w)$代替。
	4. 重复 2--3，直到求得 $v$ 到其余所有顶点的最短路径。

- Floyd：求每对顶点之间的最短路径：
	&emsp;&emsp;依次计算 $A^{(0)}$， $A^{(1)}$， …， $A^{(n)}$。 $A^{(0)}$为邻接矩阵，计算$A^{(k)}$时， $A^{(k)}(i,j)=min\{A^{(k-1)}(i,j),A^{(k-1)}(i,k)+A^{(k-1)}(k,j)\}$。
	&emsp;&emsp;技巧：计算$A^{(k)}$的技巧。第$k$行、第$k$列、对角线的元素保持不变，对其余元素，考查$A(i,j)$与$A(i,k)+A(k,j)$（第 $k$ 列 $i$ 行元素加上第 $k$ 行 $j$ 列元素），如果后者更小则替换$A(i,j)$，同时修改路径。


## 串匹配KMP算法  

> 参考博文 http://www.ruanyifeng.com/blog/2013/05/Knuth%E2%80%93Morris%E2%80%93Pratt_algorithm.html


## 五大算法思想
### 贪心算法  
 &emsp;&emsp;例：背包问题，均分纸牌，最大整数  

> 参考博文 https://blog.csdn.net/qq_32400847/article/details/51336300

### 动态规划算法  
&emsp;&emsp;例：0-1背包问题，钢条切割问题  

> 参考博文 https://blog.csdn.net/qq_32400847/article/details/51148917

### 分治算法  
&emsp;&emsp;例：棋盘覆盖、找出伪币、求最值  

> 参考博文 https://blog.csdn.net/qq_32400847/article/details/51029121

### 回溯算法  
&emsp;&emsp;例：0-背包问题、旅行商问题、八皇后问题  

> 参考博文 https://blog.csdn.net/qq_32400847/article/details/51474105

### 分支限界算法  
&emsp;&emsp;例：装载问题，旅行售货员问题  

> 参考博文 https://blog.csdn.net/qq_32400847/article/details/51813606

