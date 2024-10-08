 在有向图G中，如果两个顶点u，ｖ间有一条从ｕ到ｖ的有向路径，同时还有一条从ｖ到ｕ的有向路径，则称两个顶点强连通。如果有向图G的每两个顶点都强连通，称G是一个强连通图。有向非强连通图的极大强连通子图，称为强连通分量。
Tarjan算法是基于对图深度优先搜索的算法，每个强连通分量为搜索树中的一棵子树。搜索时，把当前搜索树中未处理的节点加入一个堆栈，回溯时可以判断栈顶到栈中的节点是否为一个强连通分量。 定义DFN(u)为节点u搜索的次序编号(时间戳)，Low(u)为u或u的子树能够追溯到的最早的栈中节点的次序号。 由定义可以得出，Low(u)=Min {Low(u), Low(v) } (u,v)为树枝边，u为v的父节点 . Low(u)=Min {Low(u), DFN(v) } DFN(v),(u,v)为指向栈中节点的后向边(指向栈中结点的横叉边) } 当结点u搜索结束后，若DFN(u)=Low(u)时，则以u为根的搜索子树上所有还在栈中的节点是一个强连通分量。

```c++
#include <iostream>
#include <vector>
#include <stack>
#include <algorithm>
using namespace std;
//拓补排序ac不了这个题，只能用scc
vector<vector<int>> a;//邻接表
vector<int> dfn, low, inStack, sccId;
stack<int> s;
int index = 0, sccCount = 0;
//dfn：记录每个节点的访问时间戳。
//low：记录每个节点能追溯到的最早节点的时间戳。
//inStack：记录节点是否在栈中。
//sccId：记录每个节点所属的强连通分量的ID。 
//stack：用于存储当前路径上的节点。
//index：全局时间戳。
//sccCount：强连通分量计数。
void tarjan(int u) {
    dfn[u] = low[u] = ++index;
    s.push(u);
    inStack[u] = 1;
    for (int v : a[u]) {
        if (!dfn[v]) {//没访问过
            tarjan(v);
            low[u] = min(low[u], low[v]);
        }
        else if (inStack[v]) {
            low[u] = min(low[u], dfn[v]);
        }
    }
    if (dfn[u] == low[u]) {
        sccCount++;
        int v;
        do {
            v = s.top();
            s.pop();
            inStack[v] = 0;
            sccId[v] = sccCount;
        } while (u != v);
    }
}

int main() {
    int n, m;
    cin >> n >> m;
    a.resize(n + 1);
    dfn.resize(n + 1, 0);
    low.resize(n + 1, 0);
    inStack.resize(n + 1, 0);
    sccId.resize(n + 1, 0);

    for (int i = 0; i < m; i++) {
        int begin, end;
        cin >> begin >> end;
        a[begin].push_back(end);
    }

    for (int i = 1; i <= n; i++) {
        if (!dfn[i]) {
            tarjan(i);
        }
    }

    vector<int> inDegree(sccCount + 1, 0);
    for (int u = 1; u <= n; u++) {
        for (int v : a[u]) {
            if (sccId[u] != sccId[v]) {
                inDegree[sccId[v]]++;
            }
        }
    }

    int ans = 0;
    for (int i = 1; i <= sccCount; i++) {
        if (inDegree[i] == 0) {
            ans++;
        }
    }

    cout << ans << endl;
}
```
- adjList.resize(n + 1);：调整邻接表的大小。

- dfn.resize(n + 1, 0);：初始化 dfn 数组。

- low.resize(n + 1, 0);：初始化 low 数组。

- inStack.resize(n + 1, 0);：初始化 inStack 数组。

- sccId.resize(n + 1, 0);：初始化 sccId 数组。

- for (int i = 0; i < m; i++)：读取所有道路信息，并填充邻接表。

- for (int i = 1; i <= n; i++)：遍历所有节点，调用 tarjan 函数。

- vector<int> inDegree(sccCount + 1, 0);：初始化每个强连通分量的入度。

- for (int u = 1; u <= n; u++)：遍历所有节点，计算每个强连通分量的入度。

- int ans = 0;：初始化答案。

- for (int i = 1; i <= sccCount; i++)：统计入度为0的强连通分量的数量。

- cout << ans << endl;：输出答案。