# 深度和广度优先搜索

## <font color = blue>内容概要</font>

1. 

## <font color = blue>遗留问题</font>

1. 





### “搜索”算法

+ **算法是作用于具体数据结构之上的，深度优先搜索算法和广度优先搜索算法都是基于“图”这种数据结构的。**这是因为，图这种数据结构的表达能力很强，大部分涉及搜索的场景都可以抽象成“图”。

+ 图上的搜索算法，最直接的理解就是，在图中找出从一个顶点出发，到另一个顶点的路径。具体方法有很多，有两种最简单、最“暴力”的深度优先、广度优先搜索，还有 A*、IDA* 等启发式搜索算法。

+ 图有两种主要存储方法，邻接表和邻接矩阵。这里用邻接表来存储图。需要说明一下，深度优先搜索算法和广度优先搜索算法，既可以用在无向图，也可以用在有向图上。

  ```java
  public class Graph { // 无向图
    private int v; // 顶点的个数
    private LinkedList<Integer> adj[]; // 邻接表
  
    public Graph(int v) {
      this.v = v;
      adj = new LinkedList[v];
      for (int i=0; i<v; ++i) {
        adj[i] = new LinkedList<>();
      }
    }
  
    public void addEdge(int s, int t) { // 无向图一条边存两次
      adj[s].add(t);
      adj[t].add(s);
    }
  }
  ```



### 广度优先搜索（BFS）

+ 广度优先搜索（Breadth-First-Search），简称为 BFS。直观地讲，它其实就是一种“地毯式”层层推进的搜索策略，即先查找离起始顶点最近的，然后是次近的，依次往外搜索。

  ![](9.jpg)

+ 尽管广度优先搜索的原理挺简单，但代码实现还是稍微有点复杂度。

  + 这里面，bfs() 函数就是基于之前定义的，图的广度优先搜索的代码实现。其中 s 表示起始顶点，t 表示终止顶点。搜索一条从 s 到 t 的路径。实际上，这样求得的路径就是从 s 到 t 的最短路径。

  + 有三个重要的辅助变量 visited、queue、prev。

    + visited 是用来记录已经被访问的顶点，用来避免顶点被重复访问。如果顶点 q 被访问，那相应的 visited[q]会被设置为 true。
    + queue 是一个队列，用来存储已经被访问、但相连的顶点还没有被访问的顶点。因为广度优先搜索是逐层访问的，也就是说，我们只有把第 k 层的顶点都访问完成之后，才能访问第 k+1 层的顶点。当我们访问到第 k 层的顶点的时候，我们需要把第 k 层的顶点记录下来，稍后才能通过第 k 层的顶点来找第 k+1 层的顶点。所以，我们用这个队列来实现记录的功能。
    + prev 用来记录搜索路径。当我们从顶点 s 开始，广度优先搜索到顶点 t 后，prev 数组中存储的就是搜索的路径。不过，这个路径是反向存储的。prev[w]存储的是，顶点 w 是从哪个前驱顶点遍历过来的。比如，我们通过顶点 2 的邻接表访问到顶点 3，那 prev[3]就等于 2。为了正向打印出路径，我们需要递归地来打印，你可以看下 print() 函数的实现方式。

    ```java
    public void bfs(int s, int t) {
      if (s == t) return;
      boolean[] visited = new boolean[v];
      visited[s]=true;
      Queue<Integer> queue = new LinkedList<>();
      queue.add(s);
      int[] prev = new int[v];
      for (int i = 0; i < v; ++i) {
        prev[i] = -1;
      }
      while (queue.size() != 0) {
        int w = queue.poll();
       for (int i = 0; i < adj[w].size(); ++i) {
          int q = adj[w].get(i);
          if (!visited[q]) {
            prev[q] = w;
            if (q == t) {
              print(prev, s, t);
              return;
            }
            visited[q] = true;
            queue.add(q);
          }
        }
      }
    }
    
    private void print(int[] prev, int s, int t) { // 递归打印s->t的路径
      if (prev[t] != -1 && t != s) {
        print(prev, s, prev[t]);
      }
      System.out.print(t + " ");
    }
    ```

    ![](10.jpg)

    ![](11.jpg)

    ![](12.jpg)

+ 复杂度分析

  + 最坏情况下，终止顶点 t 离起始顶点 s 很远，需要遍历完整个图才能找到。这个时候，每个顶点都要进出一遍队列，每个边也都会被访问一次，所以，广度优先搜索的时间复杂度是 O(V+E)，其中，V 表示顶点的个数，E 表示边的个数。当然，对于一个连通图来说，也就是说一个图中的所有顶点都是连通的，E 肯定要大于等于 V-1，所以，广度优先搜索的时间复杂度也可以简写为 O(E)。
  + 广度优先搜索的空间消耗主要在几个辅助变量 visited 数组、queue 队列、prev 数组上。这三个存储空间的大小都不会超过顶点的个数，所以空间复杂度是 O(V)。





### 深度优先搜索（DFS

+ 深度优先搜索（Depth-First-Search），简称 DFS。最直观的例子就是“走迷宫”。

+ 假设你站在迷宫的某个岔路口，然后想找到出口。你随意选择一个岔路口来走，走着走着发现走不通的时候，你就回退到上一个岔路口，重新选择一条路继续走，直到最终找到出口。这种走法就是一种深度优先搜索策略。

+ 走迷宫的例子很容易能看懂，如何在图中应用深度优先搜索，来找某个顶点到另一个顶点的路径。下图中，搜索的起始顶点是 s，终止顶点是 t，希望在图中寻找一条从顶点 s 到顶点 t 的路径。如果映射到迷宫那个例子，s 就是你起始所在的位置，t 就是出口。

+ 用深度递归算法，把整个搜索的路径标记出来了。这里面实线箭头表示遍历，虚线箭头表示回退。从图中可以看出，深度优先搜索找出来的路径，并不是顶点 s 到顶点 t 的最短路径。

  ![](13.jpg)

+ 实际上，深度优先搜索用的是一种比较著名的算法思想，回溯思想。这种思想解决问题的过程，非常适合用递归来实现。

+ 把上面的过程用递归来翻译出来，就是下面这个样子。深度优先搜索代码实现也用到了 prev、visited 变量以及 print() 函数，它们跟广度优先搜索代码实现里的作用是一样的。不过，深度优先搜索代码实现里，有个比较特殊的变量 found，它的作用是，当我们已经找到终止顶点 t 之后，就不再递归地继续查找了。

  ```java
  boolean found = false; // 全局变量或者类成员变量
  
  public void dfs(int s, int t) {
    found = false;
    boolean[] visited = new boolean[v];
    int[] prev = new int[v];
    for (int i = 0; i < v; ++i) {
      prev[i] = -1;
    }
    recurDfs(s, t, visited, prev);
    print(prev, s, t);
  }
  
  private void recurDfs(int w, int t, boolean[] visited, int[] prev) {
    if (found == true) return;
    visited[w] = true;
    if (w == t) {
      found = true;
      return;
    }
    for (int i = 0; i < adj[w].size(); ++i) {
      int q = adj[w].get(i);
      if (!visited[q]) {
        prev[q] = w;
        recurDfs(q, t, visited, prev);
      }
    }
  }
  ```

+ 复杂度分析

  + 每条边最多会被访问两次，一次是遍历，一次是回退。所以，图上的深度优先搜索算法的时间复杂度是 O(E)，E 表示边的个数。
  + 深度优先搜索算法的消耗内存主要是 visited、prev 数组和递归调用栈。visited、prev 数组的大小跟顶点的个数 V 成正比，递归调用栈的最大深度不会超过顶点的个数，所以总的空间复杂度就是 O(V)。





### 如何找出社交网络中某个用户的三度好友关系

+ 社交网络可以用图来表示。这个问题就非常适合用图的广度优先搜索算法来解决，因为广度优先搜索是层层往外推进的。
+ 首先，遍历与起始顶点最近的一层顶点，也就是用户的一度好友，然后再遍历与用户距离的边数为 2 的顶点，也就是二度好友关系，以及与用户距离的边数为 3 的顶点，也就是三度好友关系。
+ 只需要稍加改造一下广度优先搜索代码，用一个数组来记录每个顶点与起始顶点的距离，非常容易就可以找出三度好友关系。