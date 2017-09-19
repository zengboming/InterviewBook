### **广度优先搜索（BFS）**

广度优先搜索在进一步遍历图中顶点之前，先访问当前顶点的所有邻接结点。

1. 首先选择一个顶点作为起始结点，并将其染成灰色，其余结点为白色。
2. 将起始结点放入队列中。
3. 从队列首部选出一个顶点，并找出所有与之邻接的结点，将找到的邻接结点放入队列尾部，将已访问过结点涂成黑色，没访问过的结点是白色。如果顶点的颜色是灰色，表示已经发现并且放入了队列，如果顶点的颜色是白色，表示还没有发现
4. 按照同样的方法处理队列中的下一个结点。
5. 基本就是出队的顶点变成黑色，在队列里的是灰色，还没入队的是白色。

![](/assets/import123.png)

```
public void bfs(int[][] image, int start, int[] visite) {
        int n = image.length;
        queue.push(start);
        visite[start] = 1;
        while(!queue.isEmpty()) {
            int node = queue.peek();
            System.out.println(queue.poll() + 1 + " ");
            for (int i = 0; i < n; i++) {
                if (image[node][i] == 1 && visite[i] == 0) {
                    queue.offer(i);
                    visite[i] = 1;
                }
            }
        }
    }
```

### 深度优先搜索（DFS）

深度优先搜索在搜索过程中访问某个顶点后，需要递归地访问此顶点的所有未访问过的相邻顶点。

1. 选择起始顶点涂成灰色，表示还未访问
2. 从该顶点的邻接顶点中选择一个，继续这个过程（即再寻找邻接结点的邻接结点），一直深入下去，直到一个顶点没有邻接结点了，涂黑它，表示访问过了
3. 回溯到这个涂黑顶点的上一层顶点，再找这个上一层顶点的其余邻接结点，继续如上操作，如果所有邻接结点往下都访问过了，就把自己涂黑，再回溯到更上一层。
4. 上一层继续做如上操作，知道所有顶点都访问过。

```
public void dfs(int[][] image, int start, int[] visit) {
		visit[start] = 1;
		int n = image.length;

		for (int i = 0; i < n; i++) {
			if (visit[i] == 0 && image[start][i] == 1) {
				dfs(image, i, visit);
			}
		}
		System.out.println(start + 1 + " ");
	}
```

```
public void dfs1(int[][] image, int start, int[] visit) {
		stack.push(start);
		int n = image.length;
		visit[start] = 1;
		boolean tag = false;
		while (!stack.isEmpty()) {
			tag = false;
			int top = stack.peek();
			for (int i = 0; i < n; i++) {
				if (visit[i] == 0 && image[top][i] == 1) {
					visit[i] = 1;
					stack.push(i);
					tag = true;
				}
			}
			if (!tag) {
				System.out.println(top + 1 + " ");
				stack.pop();
			}
		}
	}
```

转自：[http://www.jianshu.com/p/70952b51f0c8](http://www.jianshu.com/p/70952b51f0c8)

