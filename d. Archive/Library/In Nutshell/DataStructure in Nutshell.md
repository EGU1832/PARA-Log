# DataStructure

## Infix to Postfix
- 연산자가 출력 되는 순서는 우선 순위에 따라 다르다.
- 높은 순위의 operator를 먼저 출력해야 하기 때문에, operator의 정확한 위치를 알 때 까지 우선 저장한다.
- Stack operator:
	- Stack에 operator를 추가한다(쌓는다)고 생각할 때, 높은 순위의 operator일수록 마지막에 추가되어야 한다.
		- e.g. * 다음에 + 를 쌓는 건 안됨, * 다음에 / 를 쌓는 건 됨.
	- ( )안의 operator는 다음과 같은 과정을 따른다.
		1. "("과 operator를 ")"에 도달할 때 까지 stack에 추가한다.
		2. 괄호가 끝나면 다시 "("에 도달할 때 까지 unstack한다.
		3. "("를 삭제한다.


## Equivalent class
- Equivalent relations
	reflexivity
	symmetry
	transivity
- direct equivalent relationship
- time complexity : O(m+n), space complexity : O(m+n)

## Trees
- Root, Subtrees
- Degree of a node
	leaf(terminal) node
- Degree of a tree
- Parent, Children, Siblings
- Level, Height
- Representing tree
	generalized list
	direct representation
	left child-right sibling representation
	degree-two tree representation

## Binary Trees
- left subtree, right subtree
- BinTree
- Full Binary Tree
- Complete Binary Tree
- Array representation
- Linked representation

## Binary Tree Traversal
- L, V, R
	LVR inorder traversal
	LRV postorder traversal
	VLR preorder traversal
	:
- Iterative inorder traversal
- Level order traversal

## Additional Binary Tree Operations
- copying binary tree -> postorder traversal
- test binary tree copy -> preorder traversal
- Satisfiability Problem

## Threaded Binary Trees
- 