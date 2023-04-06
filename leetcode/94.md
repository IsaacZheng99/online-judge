# 二叉树迭代遍历的统一写法

## 1. 常见的迭代写法

这里把三种深度优先遍历方式的常规迭代写法先给出来：

- 前序遍历：

  ```python
  class Solution:
      def preorderTraversal(self, root: TreeNode) -> List[int]:
          lResult: List = []
          lStack: List = []
          lStack.append(root)
          while lStack:
              oCur: TreeNode = lStack.pop()
              if not oCur:
                  continue
              lResult.append(oCur.val)
              lStack.append(oCur.right)
              lStack.append(oCur.left)
          return lResult
  ```

- 中序遍历：

  ```python
  class Solution:
      def inorderTraversal(self, root: TreeNode) -> List[int]:
          lResult: List = []
          lStack: List = []
          oCur: TreeNode = root
          while oCur:
              lStack.append(oCur)
              oCur = oCur.left
          while lStack:
              oCur = lStack.pop()
              lResult.append(oCur.val)
              oCur = oCur.right
              while oCur:
                  lStack.append(oCur)
                  oCur = oCur.left
          return lResult
  ```

- 后序遍历：

  ```python
  class Solution:
      def postorderTraversal(self, root: TreeNode) -> List[int]:
          lResult: List = []
          if not root:
              return lResult
          lStack: List = [root]
          while lStack:
              oCur: TreeNode = lStack.pop()
              if not oCur:
                  continue
              lResult.append(oCur.val)
              lStack.append(oCur.left)
              lStack.append(oCur.right)
          return lResult[::-1]
  ```

## 2. 对比递归写法

- 三种遍历方式的递归代码在写法上是同一的，不同之处在于处理节点的顺序不同，这里就不给代码了。
- 但是三种遍历方式的迭代代码在写法上并不统一（至少前序和中序是不同的），这是因为：对于前序遍历的迭代写法，访问到的元素和需要操作的元素是相同的，都是中间节点；对于中序遍历的迭代写法，当前要操作的节点和当前访问的节点，并不是一个节点，所以我们需要找到所有左侧的节点，再进行出栈入栈操作。而对于递归写法，函数本身的递归调用就帮我们解决了这个问题。
- 所以只要我们能够解决上述提到的”访问元素和操作元素不同“的问题，就能够统一迭代写法了。

## 3. 迭代遍历的统一写法

- 中序遍历。前面我们提到”访问元素和操作元素不同”的问题，可以考虑通过“标记”的方式解决，即在遍历的过程中，标记我们需要处理的节点，无论是哪一种遍历方式，这种思路是都可行的。代码如下：

  ```python
  class Solution:
      def inorderTraversal(self, root: TreeNode) -> List[int]:
          lResult: List = []
          if not root:
              return lResult
          lStack: List = [root]
          while lStack:
              oCur: TreeNode = lStack.pop()
              if oCur:
                  if oCur.right:
                      lStack.append(oCur.right)  # 右
                  lStack.append(oCur)  # 中
                  lStack.append(None)
                  if oCur.left:
                      lStack.append(oCur.left)  # 左
              else:
                  oCur = lStack.pop()
                  lResult.append(oCur.val)
          return lResult
  ```

  可以看到，在第12\~13行，我们通过一个空节点实现了我们的“标记”需求，同时在10\~15行，我们根据栈的性质，按照右节点、中间节点、左节点的顺序，依次将非空节点（注意这里需要是非空，因为空节点是用来标记的）放入了栈中；而当我们pop出一个空节点时，意味着下一个节点就是需要加入到lResult中的节点了。

- 同理给出前序遍历的代码：

  ```python
  class Solution:
      def preorderTraversal(self, root: TreeNode) -> List[int]:
          lResult: List = []
          if not root:
              return lResult
          lStask: List = [root]
          while lStask:
              oCur: TreeNode = lStask.pop()
              if oCur:
                  if oCur.right:
                      lStask.append(oCur.right)  # 右
                  if oCur.left:
                      lStask.append(oCur.left)  # 左
                  lStask.append(oCur)  # 中
                  lStask.append(None)
              else:
                  oCur = lStask.pop()
                  lResult.append(oCur.val)
          return lResult
  ```

- 同理给出后序遍历的代码：（有一丢丢细微的差别）

  ```python
  class Solution:
      def postorderTraversal(self, root: TreeNode) -> List[int]:
          lResult: List = []
          lStack: List = [root]
          while lStack:
              oCur: TreeNode = lStack.pop()
              if oCur:
                  lStack.append(oCur)  # 中
                  lStack.append(None)
                  if oCur.right:
                      lStack.append(oCur.right)  # 右
                  if oCur.left:
                      lStack.append(oCur.left)  # 左
              else:
                  if lStack:
                      oCur = lStack.pop()
                      lResult.append(oCur.val)
          return lResult
  ```