# Graph

![](https://github.com/shamy1st/data-structure/blob/main/images/directed-graph.png)

* **Complexity** if you have dense graph use matrix implementation otherwise use list implementation.

![](https://github.com/shamy1st/data-structure/blob/main/images/dense-graph.png)

average          | add edge  | remove edge| query edge| find neighbors| add node      | remove node   | space
-----------------|-----------|------------|-----------|---------------|---------------|---------------|----------------
Adjacency Matrix | O(1)      | O(1)       | O(1)      | O(V)          | O(V^2)        | O(V^2)        | O(V^2)
Adjacency List   | O(K)      | O(K)       | O(K)      | O(K)          | O(1)          | O(V^2)        | O(V + E)

worst            | add edge  | remove edge| query edge| find neighbors| add node      | remove node   | space
-----------------|-----------|------------|-----------|---------------|---------------|---------------|----------------
Adjacency Matrix | O(1)      | O(1)       | O(1)      | O(V)          | O(V^2)        | O(V^2)        | O(V^2)
Adjacency List   | O(V)      | O(V)       | O(V)      | O(V)          | O(1)          | O(V^2)        | O(V^2)

  * **Average**
  ![](https://github.com/shamy1st/data-structure/blob/main/images/graph-complexity-average.png)
  
  * **Worst Case**
  ![](https://github.com/shamy1st/data-structure/blob/main/images/graph-complexity-worst.png)

* **Implementation**
  * **Adjacency Matrix**
  ![](https://github.com/shamy1st/data-structure/blob/main/images/graph-matrix.png)
  
  * **Adjacency List**
  ![](https://github.com/shamy1st/data-structure/blob/main/images/graph-list.png)

* **Traversals**
  * **Depth First**
  ![](https://github.com/shamy1st/data-structure/blob/main/images/graph-depth-first.png)
  
  * **Breadth First**
  ![](https://github.com/shamy1st/data-structure/blob/main/images/graph-breadth-first-1.png)
  ![](https://github.com/shamy1st/data-structure/blob/main/images/graph-breadth-first-2.png)
  
* **Topological Sort** the right order that we need to process a nodes of a graph.
  * like schedule jobs
  * doesn't produce a unique solution.
  * only for directed acyclic graphs. acyclic means without cycles.
  * nodes with outgoing edges should comes first, and nodes without any outgoing edges comes last.
  
  * How it work?
    1. traverse Depth First and go deep to the last node, add this node to stack. 
        * [X -> A -> P]
    2. go back to nodes that this node comes from and push them one by one, but after their children.
        * push P
        * push "children of A" then push A
        * push "children of X", in this case push B but also after push "children of B"
        * push X
    3. pop all elements from the stack, this is the correct order.
        * pop X
        * pop B
        * pop A
        * pop P
  
  ![](https://github.com/shamy1st/data-structure/blob/main/images/graph-topological-sort.png)

* **Cycle Detection**
![](https://github.com/shamy1st/data-structure/blob/main/images/graph-cycle-1.png)
![](https://github.com/shamy1st/data-structure/blob/main/images/graph-cycle-2.png)

        public class Main {
            public static void main(String[] args) {
                Graph graph = new Graph();
                graph.addNode("A");
                graph.addNode("B");
                graph.addNode("C");
                graph.addEdge("A", "B");
                graph.addEdge("B", "C");
                graph.addEdge("C", "A");
                System.out.println(graph.hasCycle());
            }
        }

        public class Graph {
            private class Node {
                private String label;

                public Node(String label) {
                    this.label = label;
                }

                @Override
                public String toString() {
                    return label;
                }
            }

            private Map<String, Node> nodes;
            private Map<Node, List<Node>> adjacencyList;

            public Graph() {
                this.nodes = new HashMap<>();
                this.adjacencyList = new HashMap<>();
            }

            public void addNode(String label) {
                Node node = new Node(label);
                nodes.putIfAbsent(label, node);
                adjacencyList.putIfAbsent(node, new ArrayList<>());
            }

            public void addEdge(String from, String to) {
                if(!nodes.containsKey(from) || !nodes.containsKey(to))
                    throw new IllegalStateException();

                Node fromNode = nodes.get(from);
                Node toNode = nodes.get(to);

                adjacencyList.get(fromNode).add(toNode);
            }

            public void removeNode(String label) {
                if(!nodes.containsKey(label))
                    return;

                Node node = nodes.get(label);
                for(Node fromNode : adjacencyList.keySet())
                    adjacencyList.get(fromNode).remove(node);

                adjacencyList.remove(node);
                nodes.remove(label);
            }

            public void removeEdge(String from, String to) {
                if(!nodes.containsKey(from) || !nodes.containsKey(to))
                    return;

                Node fromNode = nodes.get(from);
                Node toNode = nodes.get(to);
                adjacencyList.get(fromNode).remove(toNode);
            }

            public void print() {
                for(Node node : adjacencyList.keySet())
                    System.out.println(node + " is connected to " + adjacencyList.get(node));
            }

            public void traverseDepthFirst(String start) {
                if(!nodes.containsKey(start))
                    return;

                Node startNode = nodes.get(start);
                traverseDepthFirstRecursive(startNode, new HashSet<>());
            }

            private void traverseDepthFirstRecursive(Node node, Set<Node> visited) {
                System.out.println(node);
                visited.add(node);
                for(Node child : adjacencyList.get(node)) {
                    if(!visited.contains(child))
                        traverseDepthFirstRecursive(child, visited);
                }
            }

            public void traverseDepthFirstIterative(String start) {
                if(!nodes.containsKey(start))
                    return;

                Node startNode = nodes.get(start);

                Set<Node> visited = new HashSet<>();
                Stack<Node> stack = new Stack<>();
                stack.push(startNode);

                while(!stack.isEmpty()) {
                    Node current = stack.pop();
                    System.out.println(current);
                    visited.add(current);

                    for(Node child : adjacencyList.get(current)) {
                        if(!visited.contains(child))
                            stack.push(child);
                    }
                }
            }

            public void traverseBreadthFirst(String start) {
                if(!nodes.containsKey(start))
                    return;

                Node startNode = nodes.get(start);

                Set<Node> visited = new HashSet<>();
                Queue<Node> queue = new ArrayDeque<>();
                queue.add(startNode);

                while(!queue.isEmpty()) {
                    Node current = queue.remove();
                    System.out.println(current);
                    visited.add(current);

                    for(Node child : adjacencyList.get(current)) {
                        if(!visited.contains(child))
                            queue.add(child);
                    }
                }
            }

            public List<String> topologicalSort() {
                Set<Node> visited = new HashSet<>();
                Stack<Node> stack = new Stack<>();

                for(Node node : nodes.values())
                    topologicalSort(node, visited, stack);

                List<String> sorted = new ArrayList<>();
                while(!stack.isEmpty())
                    sorted.add(stack.pop().label);

                return sorted;
            }

            private void topologicalSort(Node node, Set<Node> visited, Stack<Node> stack) {
                if(visited.contains(node))
                    return;

                visited.add(node);

                for(Node child : adjacencyList.get(node))
                    topologicalSort(child, visited, stack);

                stack.push(node);
            }

            public boolean hasCycle() {
                Set<Node> all = new HashSet<>();
                all.addAll(nodes.values());

                Set<Node> visiting = new HashSet<>();
                Set<Node> visited = new HashSet<>();

                while(!all.isEmpty()) {
                    Node current = all.iterator().next();
                    if(hasCycle(current, all, visiting, visited))
                        return true;
                }
                return false;
            }

            private boolean hasCycle(Node node, Set<Node> all, Set<Node> visiting, Set<Node> visited) {
                all.remove(node);
                visiting.add(node);

                for(Node child : adjacencyList.get(node)) {
                    if (visited.contains(child))
                        continue;

                    if (visiting.contains(child))
                        return true;

                    if(hasCycle(child, all, visiting, visited))
                        return true;
                }

                visiting.remove(node);
                visited.add(node);

                return false;
            }
        }
