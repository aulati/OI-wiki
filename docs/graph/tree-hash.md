判断一些树是否同构的时，我们常常把这些树转成哈希储存起来，以降低复杂度。

树哈希是很灵活的，可以设计出各种各样的哈希方式；但是如果随意设计，很有可能是错误的，可能被卡。以下介绍一类正确且易于实现的方法。

## 方法

这类方法需要一个多重集的哈希函数。以某个结点为根的子树的哈希值，就是以它的所有儿子为根的子树的哈希值构成的多重集的哈希值，即：

$$
h_x = f(\{ h_i \mid i \in son_x \})
$$

其中 $h_x$ 表示以 $x$ 为根的子树的哈希值，$f$ 是多重集的哈希函数。

通常使用以下的哈希函数：

$$
f(S) = c + \sum_{x \in S} g(x)
$$

其中 $c$ 为常数，一般使用 $1$ 即可。$g$ 为整数到整数的映射，一般使用 xor shift，不建议使用多项式。为了避免出题人对着 xor hash 卡，还可以在映射前后异或一个随机常数。

## 例题

### [UOJ #763. 树哈希](https://uoj.ac/problem/763)

这是一道模板题。不用多说，以 $1$ 为根跑一遍 DFS 就好了。

??? "参考代码"
    ```cpp
    --8<-- "docs/graph/code/tree-hash/tree-hash_4.cpp"
    ```

### [\[BJOI2015\] 树的同构](https://www.luogu.com.cn/problem/P5043)

我们用上述方式进行哈希。注意只有当根一样时，同构的两棵子树哈希值才相同。由于数据范围较小，我们可以暴力求出以每个点为根时的哈希值，排序后比较。

如果数据范围较大，我们也可以通过换根 dp 的方式，遍历树两遍，求出以每个点为根时的哈希值。我们还可以利用上面的多重集哈希函数：把以所有结点的树的哈希值存到多重集中，把它的哈希值算出来，进行比较（做法一）。

还可以通过找重心的方式来优化复杂度。一棵树的重心最多只有两个，既可以分别比较（做法二），也可以在有两个重心时取哈希值的较小（大）值，作为整棵树的哈希（在做法二的基础上稍作修改即可）。

??? "做法一"
    ```cpp
    --8<-- "docs/graph/code/tree-hash/tree-hash_1.cpp"
    ```

??? "做法二"
    ```cpp
    --8<-- "docs/graph/code/tree-hash/tree-hash_2.cpp"
    ```

### [HDU 6647](https://vjudge.net/problem/HDU-6647)

题目要求的是遍历一棵无根树产生的本质不同括号序列方案数。

首先，注意到一个结论，对于两棵有根树，如果他们不同构，一定不会生成相同的括号序列。我们先考虑遍历有根树能够产生的本质不同括号序列方案数，假设我们当前考虑的子树根节点为 $u$，记 $f(u)$ 表示这棵子树的方案数，$son(u)$ 表示 $u$ 的儿子节点集合，从 $u$ 开始往下遍历，顺序可以随意选择，产生 $|son(u)|!$ 种排列，遍历每个儿子节点 $v$，$v$ 的子树内有 $f(v)$ 种方案，因此有 $f(u)=|son(u)|! \cdot \prod_{v\in son(u)} f(v)$。但是，同构的子树之间会产生重复，$f(u)$ 需要除掉每种本质不同子树出现次数阶乘的乘积，类似于多重集合的排列。

通过上述树形 dp，可以求出根节点的方案数，再通过换根 dp，将父亲节点的哈希值和方案信息转移给儿子，可以求出以每个节点为根时的哈希值和方案数，每种不同的子树只需要计数一次即可。

??? "参考代码"
    ```cpp
    --8<-- "docs/graph/code/tree-hash/tree-hash_3.cpp"
    ```

## 参考资料

文中的哈希方法参考并拓展自博客 [一种好写且卡不掉的树哈希](https://peehs-moorhsum.blog.uoj.ac/blog/7891)。
