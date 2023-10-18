---
title: FDS 笔记 - Tree
date: 2023-10-16 18:18:17
tags: [课程,算法,数据结构,技术]
index_img: https://s1.imagehub.cc/images/2023/10/18/covver.jpeg
categories: 课程
---

### Definition

![](https://s1.imagehub.cc/images/2023/10/18/tree.png)
A tree is a collection of nodes. The collection can be empty, otherwise, a tree consists of
+ a distinguished node r, called the **root**
+ and zero or more nonempty subtrees $T_1, T_2,...,$ each of whose roots are connected by a directed edge from r.  

Tree is a special type of graph.  
A tree is also referring to a kind of perennial plant with an elongated stem.
### Terms
**Degree of a node** : number of subtrees of the node.
**Degree of a tree** : The maximum number of the degree of nodes.
**Parent** : A node that has subtrees.
**Children** : The roots of the subtrees of a parent. (Partial Order Class)
**Sibling** : Children of the same parent (Equivalent Class)
**Path from $n_1$ to $n_k$** : A sequence of nodes $n_1,n_2...n_k$ such that $n_i$ is the parent of $n_{i+1}$ for $1<i<k$
**Length of path** : Number of edges on the path
**Depth of ni** : Length of the unique path from the root to $n_i$
**Ancestors of a node** : all the nodes along the path from the node up to the root.
**Decendants of a node** : all the nodes of the subtree.

### Implementation
#### FirstChild-NextSibling Representation
![](https://s1.imagehub.cc/images/2023/10/18/fnr.png)
Every node have two pointers `First Child` and `Next Sibling`. The structure is displayed in the following figure.  
![](https://s1.imagehub.cc/images/2023/10/18/nc.png)
The FirstChild-NextSibling Representation can be used to implement any kind of tree, though they might have a uncertain number of subtrees in their nodes.
#### Binary tree
A binary tree is a special type of tree that every node of the tree except the leaves have two subtrees. The binary tree can be easily implemented by the structure:
![](https://s1.imagehub.cc/images/2023/10/18/bt.png)
### Tree Traversal
The traversal of a tree is an algorithm to visit every node of a tree exactly once.   
There are Four different algorithms can be applied.  
The time complexity should all be $O(n)$.
#### Preorder Traversal 
```c
procedure preorder(tree)
    if notEmpty(tree) then
        visit(tree);
        for each child c of tree
            preorder(c);
```
#### Postorder Traversal
```c
procedure postorder(tree)
    if notEmpty(tree) then
        for each child c of tree
            postorder(tree)
        visit(tree)
```
#### Levelorder Traversal
```c
procedure levelorder(tree)
    enqueue(tree)
    while queue is not empty
        visit dequeue()
        for each child c of tree
            enqueue(c)
```
#### Inorder Traversal
Recursively
```c
procedure inorder(tree)
    if notEmpty(tree) then
        inorder(tree->left)
        visit(tree->element)
        inorder(tree->right)
```
Iteratively
```c
procedure inorder_iter(tree)
    for true
        for tree = tree->next 
            push(tree)
        tree = top(stack)
        pop()
        if isEmpty(tree) then break
        visit(tree->element)
        tree = tree->right
```
### Application
#### Hierarchical file system
![](https://s1.imagehub.cc/images/2023/10/18/hfs.png)
Listing format: files that are of depth $d_i$ will have their names indented by $d_i$ tabs.  
Used the preorder traveral of tree.
#### Threaded Binary Tree
Rules: 
+ if `tree->left` is **null**, replace it with a pointer to the inorder predecessor of `tree`  
+ If `tree->right` is **null**, replace it with a pointer to the inorder successor of `tree`
+ There must not be any loose threads. Therefore a threaded binary tree must have a head node of which the left child points to the first node.
```c
typedef struct ThreadedTreeNode *PtrTo ThreadedNode;
typedef struct PtrToThreadedNode TheadedTreee;
typedef struct ThreadedTreeNode{
    ElementType  Element;
    int          LeftThread;
    ThreadedTree Left;
    int          RightThread;
    ThreadedTree Right;
}
```
