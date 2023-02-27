# 较好的数据结构和算法文章

## OX01 基础

* 排序b站视频：https://www.bilibili.com/video/BV1y44y1q7MJ?p=2&spm_id_from=pageDriver&vd_source=038ea29cc68a418a40006cb72a3617f0

* 动态规划：https://www.zhihu.com/question/23995189
* 数组区间问题： https://zhuanlan.zhihu.com/c_1224355183452614656
* 回溯简介总结： https://leetcode.cn/problems/combination-sum/comments/  Allen
* 回溯体系总结： liweiwei https://leetcode.cn/problems/permutations/solution/hui-su-suan-fa-python-dai-ma-java-dai-ma-by-liweiw/.  回溯其实就是横向遍历，纵向递归的组合。

## OX02 基础记忆

回溯：

* 列举所有可能的路径 （for 循环）

* 一条路走到黑（递归），发现黑之后，再回退一步尝试其他路径

* 回溯模板：

  ``` python
  def backtrack(选择列表，路径，其他参数):
      if (回朔点）：# 这条路走到底的条件。也是递归出口
          保存该结果
          return   
      else:
          for route in all_route_set :  # 逐步选择当前节点下的所有可能route
              if 剪枝条件：
                  剪枝前的操作
                  return   #不继续往下走了，退回上层，换个路再走
              else：#当前路径可能是条可行路径
                  保存当前数据  #向下走之前要记住已经走过这个节点了。例如push当前节点
                  self.backtrack(选择列表，路径，其他参数) #递归发生，继续向下走一步了。
                  回朔清理  # 该节点下的所有路径都走完了，清理堆栈，准备下一个递归。例如弹出当前节点
  ```

* 如果问题的解空间是可穷举的，则可以把解空间构建成一颗树，然后使用回溯（类似DFS）遍历所有解空间找到满足条件的解。（中间可以有一些剪枝操作优化）

​	

## OX03 树

1. `二叉树（Binary Tree）`： 每个节点最多有两个子节点。子节点有左、右的顺序之分（是有序树）

   1. 满二叉树： 每一层都挂满。节点数目： 2^n -1 
   2. 完全二叉树：除最后一层外，每一层都是满的； 最后一层只有最右边是空的。 （ 对于每个节点n来说，它的左节点位置就是2n，它的右节点位置就是2n+1。**这样我们就能够将完全二叉树存储在数组的结构中去了**。如果是非完全二叉树也用数组存储的话，那么数组的中间会产生许多的空洞，造成内存的浪费）

2. `二叉查找树（Binary Search Tree） BST`： 各节点值不同，并且对于任意一个子树：左<根<右。

   1. 插入和查找的时间复杂度都是 O(lgn)，最坏情况 O(n)。查找效率和树高成反比
   2. 也叫有序二叉树(ordered binary tree)、排序二叉树(sorted binary tree)， 因为中序遍历即可排序。

3. `平衡二叉查找树（Balanced Binary Search Tree）`： 改进的二叉查找树，保持树的高度尽量矮（利于查询）。 通过插入、删除时的 左旋、右旋来达到树的平衡。 平衡的理解： 左右平衡，不至于单边特别高。

   1. `AVL树`: 自平衡的二叉查找树。比红黑树更加平衡（平衡因子BalanceFacyor  绝对值小于等于 1），代价是插入、删除会做更多的旋转
   2. `红黑树`: 自平衡的二叉查找树。相对平衡（从根节点到叶子节点的最长的路径不多于最短的路径的长度的两倍），在插入性能和查询性能之间做了折中，插入删除相对AVL更快捷。
   3. 如果查询较多，则使用AVL； 如果插入删除较多，则使用红黑树。

4. `堆`: 也是二叉树，根结点比所有子结点大/小（大顶堆 、小顶堆）。  

   > A [Heap](https://www.geeksforgeeks.org/binary-heap/) is a special [Tree-based data structure](https://www.geeksforgeeks.org/binary-tree-data-structure/) in which the tree is a [complete binary tree](https://www.geeksforgeeks.org/binary-tree-set-3-types-of-binary-tree/). Since a heap is a complete binary tree, a heap with **N** nodes has **log N** height. It is useful to remove the highest or lowest priority element. It is typically represented as an [array](https://www.geeksforgeeks.org/array-representation-of-binary-heap/).

   1. 每次都能从堆中获取最大（小）值。如求第K大元素场景。 也可用于排序
   2. 是**完全二叉树**，可用数组存，索引很快 (结点n位置2n,2n+1)
   3. 可用于实现 PriorityQueue，或者用于排序。
   4. 堆仅仅使用数组，且不使用指针，比普通的树所用空间少。（逻辑上是树，物理上用数组来存）

5. `B树`: 平衡的 多路 查找树

6. `B+树`: 

7. 参考

   1. https://luffy997.github.io/2021/07/29/二叉搜索树、平衡二叉树、红黑树、B-树性能对比/#查找代价：（**总结的非常好**）
   2. https://www.cnblogs.com/liaowenhui/p/13944927.html （二叉树基本概念）
   3. https://tech.meituan.com/2016/12/02/redblack-tree.html 美团，红黑树





#### 0X04： 技巧

1. > 遇到一道二叉树的题目时的通用思考过程是：**是否可以通过遍历一遍二叉树得到答案？如果不能的话，是否可以定义一个递归函数，通过子问题（子树）的答案推导出原问题的答案? 如果需要设计到子树信息, 建议使用后续遍历.**

2. 
