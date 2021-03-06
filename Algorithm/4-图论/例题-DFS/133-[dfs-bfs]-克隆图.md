题目：[133. 克隆图](https://leetcode-cn.com/problems/clone-graph/)

## 方法一：dfs

```c++
class Solution {
public:
    unordered_map<int, Node*> visited;

    Node* dfs(Node* node) {
        if (node == nullptr) return nullptr;
        if (visited.count(node->val)) return visited[node->val];

        Node* copyNode = new Node(node->val);
        visited[node->val] = copyNode;
        for (Node* nextNode : node->neighbors) {
            copyNode->neighbors.push_back(dfs(nextNode));
        }
        return copyNode;
    }

    Node* cloneGraph(Node* node) {
        return dfs(node);
        ;
    }
};
```

执行用时：40 ms, 在所有 Python3 提交中击败了56.04%的用户

内存消耗：15.4 MB, 在所有 Python3 提交中击败了31.73%的用户

```python
"""
# Definition for a Node.
class Node:
    def __init__(self, val = 0, neighbors = None):
        self.val = val
        self.neighbors = neighbors if neighbors is not None else []
"""

class Solution:
    def cloneGraph(self, node: 'Node') -> 'Node':
        
        def dfs(node):
            if not node: return None
            if node in visited: return visited[node]            

            clone = Node(node.val)
            visited[node] = clone
            for n_node in node.neighbors:
                clone.neighbors.append(dfs(n_node))

            return clone
        
        visited = {}
        return dfs(node)
```

执行用时：0 ms, 在所有 Go 提交中击败了100.00%的用户

内存消耗：2.9 MB, 在所有 Go 提交中击败了76.75%的用户

```go
/**
 * Definition for a Node.
 * type Node struct {
 *     Val int
 *     Neighbors []*Node
 * }
 */

func cloneGraph(node *Node) *Node {
	visited := map[*Node]*Node{}

	var dfs func(node *Node) *Node
	dfs = func(node *Node) *Node {
		if node == nil {
			return nil
		}
		if _, ok := visited[node]; ok {
			return visited[node]
		}
		clone := &Node{node.Val, []*Node{}}
		visited[node] = clone
		for _, n_node := range node.Neighbors {
			visited[node].Neighbors = append(visited[node].Neighbors, dfs(n_node))
		}
		return clone
	}

	return dfs(node)
}
```

## 方法二：bfs

**重建图和遍历图不一样的地方**：

**需要用 visited 保存 原图中的节点 和 克隆图中对应的节点**

```c++
class Solution {
public:
    Node* cloneGraph(Node* node) {
        if (node == nullptr) return nullptr;

        queue<Node*> que;
        que.push(node);
        Node* copyRoot = new Node(node->val);
        // key:原图中的节点 val:克隆图中对应的节点
        unordered_map<Node*, Node*> visited = {{node, copyRoot}};

        while (!que.empty()) {
            Node* cur = que.front();
            que.pop();
            for (Node* nextNode : cur->neighbors) {
                if (visited.count(nextNode) == 0) {
                    que.push(nextNode);
                    visited[nextNode] = new Node(nextNode->val);
                }
                visited[cur]->neighbors.push_back(visited[nextNode]);
            }
        }
        return copyRoot;
    }
};

```

执行用时：40 ms, 在所有 Python3 提交中击败了56.04%的用户

内存消耗：15.4 MB, 在所有 Python3 提交中击败了46.98%的用户

```python
"""
# Definition for a Node.
class Node:
    def __init__(self, val = 0, neighbors = None):
        self.val = val
        self.neighbors = neighbors if neighbors is not None else []
"""

class Solution:
    def cloneGraph(self, node: 'Node') -> 'Node':
        if not node: return None
        clone = Node(node.val)
        queue = [node]
        visited = {node: clone} # key:原图中的节点 val:克隆图中对应的节点

        while queue:
            node = queue.pop(0)
            for n_node in node.neighbors:
                if n_node not in visited:
                    queue.append(n_node)
                    visited[n_node] = Node(n_node.val) # 只有没来过的点需要初始化下
                # 将当前节点在克隆图中对应的节点 的 相邻点 添加上
                visited[node].neighbors.append(visited[n_node])
        return clone
```

执行用时：0 ms, 在所有 Go 提交中击败了100.00%的用户

内存消耗：2.9 MB, 在所有 Go 提交中击败了35.06%的用户

```go
/**
 * Definition for a Node.
 * type Node struct {
 *     Val int
 *     Neighbors []*Node
 * }
 */

func cloneGraph(node *Node) *Node {
	if node == nil {
		return nil
	}
	clone := Node{node.Val, []*Node{}}
	queue := []*Node{node}
	visited := map[*Node]*Node{}
	visited[node] = &clone

	for len(queue) > 0 {
		node := queue[0]
		queue = queue[1:]
		for _, n_node := range node.Neighbors {
			if _, ok := visited[n_node]; !ok {
				queue = append(queue, n_node)
				visited[n_node] = &Node{n_node.Val, []*Node{}}
			}
			visited[node].Neighbors = append(visited[node].Neighbors, visited[n_node])
		}
	}

	return &clone
}
```

