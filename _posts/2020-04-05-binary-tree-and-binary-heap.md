---
title: "이진트리와 이진힙"
categories:
  - Data Structure
tags:
  - python
---

트리(Tree)는 단순연결리스트와는 달리 계층이 존재하는 자료구조이다. 트리의 일종인 이진트리(Binary Tree)는 컴퓨터 분야에서 널리 활용되므로 자세히 알아두면 좋다. 이진트리에 기반한 자료구조 중 하나인 이진힙(Binary Heap)은 $$O(log N)$$의 시간에 가장 높은 우선순위를 가진 항목을 삭제하는 연산과 임의의 우선순위를 가진 항목을 삽입하는 연산을 제공한다.

{:toc}

## 트리
먼저 일반적인 트리의 생김새에 대해 설명하는 용어를 정리했다.  

|용어|내용|
|---|---|
|루트(Root)|트리의 최상위에 있는 노드|
|자식(Child)|특정 노드 아래에 연결된 노드|
|차수(Degree)|특정 노드의 자식 수|
|부모(Parent)|특정 노드 위에 연결된 노드|
|이파리(Leaf)|자식이 없는 노드|
|내부(Internal) 노드|이파리가 아닌 노드|
|형제(Sibling)|부모가 같은 노드|
|조상(Ancestor)|특정 노드에서 출발하여 루트까지 올라갈 때 들리는 모든 노드들의 집합|
|후손(Descendant)|특정 노드 아래에 매달린 모든 노드들의 집합|
|서브트리(Subtree)|특정 노드 자신과 후손으로 구성된 트리|
|레벨(Level)|루트의 레벨은 1이고, 아래로 내려가며 레벨이 1씩 증가함|
|높이(Height)|트리의 최대 레벨|
|키(Key)|노드에 저장된 값으로 탐색에 사용됨|

## 이진트리
이진트리는 각각의 노드의 자식이 하나이거나 둘인 트리이다. 이진트리 중 완전이진트리(Complete Binary Tree)와 포화이진트리(Full Binary Tree)는 특별한 형태를 가진다.

![(1)]({{ '/images/2020-04-04-(1).png' }}){: .align-center}

|이름|내용|
|---|---|
|완전이진트리|마지막 레벨을 제외하면 각각의 레벨이 노드로 완전히 채워져 있고, 마지막 레벨은 노드가 왼쪽부터 차곡차곡 채워진 트리|
|포화이진트리|완전이진트리의 일종으로, 이파리의 레벨이 전부 동일하며 각각의 내부노드의 차수가 2인 트리|

### 이진트리 객체 생성하기

```python
class Node:
    def __init__(self, item, left=None, right=None):
        self.item = item
        self.left = left  # 왼쪽 아래에 달린 자식
        self.right = right  # 오른쪽 아래에 달린 자식

class BinaryTree:
        
    class EmptyError(Exception):
        pass
    
    def __init__(self):
        self.root = None
```

### 이진트리 순회하기

```python
    def preorder(self, node):  # 전위순회(Node -> Left -> Right)
        if node != None:
            print(str(node.item), end=' ')
            if node.left:
                self.preorder(node.left)
            if node.right:
                self.preorder(node.right)
            
    def postorder(self, node): # 후위순회(Left -> Right -> Node)
        if node != None:
            if node.left:
                self.postorder(node.left)
            if node.right:
                self.postorder(node.right)
            print(str(node.item), end=' ')
    
    def inorder(self, node): # 중위순회(Left -> Node -> Right)
        if node != None:
            if node.left:
                self.inorder(node.left)
            print(str(node.item), end=' ')
            if node.right:
                self.inorder(node.right)
                
    def levelorder(self, node):  # 최상위 레벨부터 시작하여 각 레벨마다 왼쪽에서 오른쪽으로 노드를 방문함
        if node == None:
            return
        q = [node]
        while len(q) > 0:
            new_node = q.pop(0)
            print(new_node.item, end=' ')
            if new_node.left != None:
                q.append(new_node.left)
            if new_node.right != None:
                q.append(new_node.right)
```

### 이진트리 높이 계산하기
```python
    def height(self, node):
        if node == None:
            return 0
        return max(self.height(node.left), self.height(node.right)) + 1
```

### 이진트리 복사하기
```python
    def copy(self, n):  # 연습문제 4.27
        
        if n == None:
            return
        
        nodes, coppied_nodes, q = [], [], [n]
        while len(q) > 0:
            node = q.pop(0)
            nodes.append(node)
            coppied_nodes.append(Node(node.item))
            if node.left != None:
                q.append(node.left)
            if node.right != None:
                q.append(node.right)
        
        for i in range(len(nodes)):  # O(N^2)
            index_left, index_right = None, None
            for j in range(len(nodes)):
                if nodes[i].left == nodes[j]:
                    index_left = j
                if nodes[i].right == nodes[j]:
                    index_right = j
            if index_left != None:
                coppied_nodes[i].left = coppied_nodes[index_left]
            if index_right != None:
                coppied_nodes[i].right = coppied_nodes[index_right]
        
        coppied_tree = BinaryTree()
        coppied_tree.root = coppied_nodes[0]
        
        return coppied_tree
```

우선 비어 있는 리스트인 `nodes`와 `coppied_nodes`를 준비한다. `while` 절에서 레벨순회를 하며 `nodes`에는 주어진 노드를 그대로 `append`하고, `coppied_nodes`에는 `item`은 동일하지만 `right`와 `left`는 `None`인 노드를 새롭게 만들어서 `append`한다. 이제 `for` 절에서 `coppied_nodes`에 있는 노드들을 본래 트리에서처럼 연결해준다. 마지막으로 `coppied_tree`라는 `BinaryTree()` 객체를 생성하고, `root`를 `coppied_nodes[0]`으로 설정한 뒤 반환한다다.

### 두 이진트리가 동일한지 검사하기
```python
    def is_same(self, my_root, other_root):  # 연습문제 4.18
        if my_root == None or other_root == None:
            return my_root == other_root
        if my_root.item != other_root.item:
            return False
        return self.is_same(my_root.left, other_root.left) and self.is_same(my_root.right, other_root.right)
```

## 이진힙
이진힙은 힙 속성을 만족하는 완전이진트리로 정의한다(힙 속성은 각각의 노드에 대해 부모의 우선순위가 자식의 우선순위보다 높은 속성임). 각각의 노드의 우선순위는 키값이 결정한다. 이진힙에서 키값이 작을수록 우선순위가 높은 경우 최소힙(Minumum Heap)이라 하며, 키값이 클수록 우선순위가 높은 경우 최대힙(Maximum Heap)이라 한다. 여기서는 최소힙에 대해 설명했다.  

### 이진힙의 구현
![(2)]({{ '/images/2020-04-04-(2).png' }}){: .align-center}
이진힙을 구현할 때는 리스트를 사용한다. 위와 같이 트리에서 레벨순회를 하며 `a[1]`부터 차곡차곡 키값을 저장하자. 그러면 `a[i]`의 자식은 `a[2i]`와 `a[2i + 1]`에 존재하며 `a[j]`의 부모는 `a[j//2]`에 존재하게 된다.  

## 출처
파이썬과 함께하는 자료구조의 이해 / 양성봉 지음 / 생능출판사