# Map

## 红黑树

### 由来

我们要一个能够快速查找同时也能快速增加删除的数据结构。

1. 数组顺序查找(Sequential Search/Linear Search)

   虽然插入删除很快O(1)，但是查找时间复杂度是O(n)

2. 数组二分查找(Binary Search)

   让数据有序，进行二分查找，虽然查找快了O(logn)，但是插入删除慢了O(n)

3. 二叉搜索树(BST/Binary Search Tree)

   查找时间复杂度O(h)，插入O(h)，效率取决于树的高度，那怎么降低树的高度呢？

4. 平衡二叉树(AVL Tree/named after inventors Adelson-Velsky and Landis)

   任意节点的左右节点高度(平衡因子)小于等于1

   ![image-20210829012659321](C:\Users\Howard\AppData\Roaming\Typora\typora-user-images\image-20210829012659321.png)

   查找时间复杂度O(logn)

   插入有以下几种情况

   左旋(下图)、右旋

   ![image-20210829013210109](C:\Users\Howard\AppData\Roaming\Typora\typora-user-images\image-20210829013210109.png)

   LL（右旋）

![image-20210829013512266](C:\Users\Howard\AppData\Roaming\Typora\typora-user-images\image-20210829013512266.png)

​	RR（左旋）

![image-20210829013558960](C:\Users\Howard\AppData\Roaming\Typora\typora-user-images\image-20210829013558960.png)

​	LR（先左旋后右旋）

![image-20210829013839433](C:\Users\Howard\AppData\Roaming\Typora\typora-user-images\image-20210829013839433.png)

![image-20210829014045603](C:\Users\Howard\AppData\Roaming\Typora\typora-user-images\image-20210829014045603.png)

​	RL（先右旋后左旋）

![image-20210829014136308](C:\Users\Howard\AppData\Roaming\Typora\typora-user-images\image-20210829014136308.png)

![image-20210829014158558](C:\Users\Howard\AppData\Roaming\Typora\typora-user-images\image-20210829014158558.png)

对高度平衡十分严格，基本每次插入删除都需要调整若干次，影响效率

5. 红黑树（RBT、Red-Black Tree）

   红黑树是一个自平衡的二叉查找树，需要额外一个bit存储颜色（红或者黑）

   红黑树在插入和删除时比AVL树的调整次数少，平衡性略差于AVL树

   ![image-20210829014941483](C:\Users\Howard\AppData\Roaming\Typora\typora-user-images\image-20210829014941483.png)

   1. 根节点一定是黑色
   2. 红色节点的孩子一定是黑色（不能有两个红色节点相连）
   3. 任何一个节点到每个叶子节点的路径上黑色节点个数相同

   推论：红黑树高度<=2*log2(n+1)

   