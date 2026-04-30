
### 离散数学（上）

- [[The Foundations_Logic and Proofs]]
- [[Set]]
- [[Function]]
- [[Algorithimic Complexity]]


### 离散数学（下）

## [[9. Relations]]

好的，完全理解您的要求。您想要一份像图片里那样，将术语、定义和公式清晰对应起来的**词汇/概念总结表**，而不是一个带有完整解释性段落的笔记。

这是一个非常高效的复习工具。我将根据我们之前讨论的所有内容，为您生成一份这样简洁明了的笔记。

---

### **离散数学笔记：第九章 关系 (Chapter 9: Relations)**

| 英文 (Term)                      | 中文 (Chinese) & 定义 (Definition)                                                               |
| :----------------------------- | :------------------------------------------------------------------------------------------- |
| **binary relation**            | **二元关系**：关系 $R$ 是笛卡尔积的子集, $R \subseteq A \times B$                                           |
| **R-relative set of x**        | **x的R-关系集**：$R(x) = \{ y \in B \mid x \ R \ y \}$                                            |
| **R-relative set of $A_1$**    | **$A_1$的R-关系集**：$R(A_1) = \{ y \in B \mid \exists x \in A_1, x \ R \ y \}$                   |
|                                |                                                                                              |
| **reflexive**                  | **自反的**：$\forall x \in A \to (x,x) \in R$                                                    |
| **irreflexive**                | **反自反的**：$\forall x \in A \to (x,x) \notin R$                                                |
| **symmetric**                  | **对称的**：$\forall x \forall y [(x,y) \in R \to (y,x) \in R]$                                  |
| **asymmetric**                 | **非对称的**：$\forall x \forall y [(x,y) \in R \to (y,x) \notin R]$                              |
| **antisymmetric**              | **反对称的**：$\forall x \forall y [(x,y) \in R \land (y,x) \in R \to x=y]$                       |
| **transitive**                 | **传递的**：$\forall x \forall y \forall z [(x,y) \in R \land (y,z) \in R \to (x,z) \in R]$      |
|                                |                                                                                              |
| **n-ary relation**             | **n元关系**                                                                                     |
| **relational data model**      | **关系数据模型**                                                                                   |
| **primary key**                | **主键**                                                                                       |
| **composite key**              | **复合主键**                                                                                     |
| **selection operator**         | **选择运算符**：$s_C$, $R$中满足条件$C$的所有$n$元组                                                         |
| **projection operator**        | **投影运算符**：将$n$元组映射到$m$元组                                                                     |
| **join operator**              | **连接运算符**：将两个表合成一个表                                                                          |
| **the restriction of R to B**  | **R在B上的限制**：$R \cap (B \times B)$                                                            |
| **closure**                    | **闭包/封闭性**                                                                                   |
| **transitive closure**         | **传递闭包**：$R^* = \bigcup_{i=1}^{n} R^i$                                                       |
| **diagonal relation**          | **对角关系**：$\Delta = \{ (a,a) \mid a \in A \}$                                                 |
|                                |                                                                                              |
| **equivalence relation**       | **等价关系**：**自反**、**对称**、**传递**                                                                |
| **equivalence class**          | **等价类**：$[a]_R = \{ x \in A \mid (a,x) \in R \}$                                             |
| **representative**             | **代表元**：等价类中的任意一个元素                                                                          |
| **partition**                  | **划分**：将集合分解为非空、不相交的子集，其并集为全集                                                                |
|                                |                                                                                              |
| **partial order**              | **偏序关系**：**自反**、**反对称**、**传递** (记为 $\preccurlyeq$)                                           |
| **poset**                      | **偏序集**：$(A, \preccurlyeq)$                                                                  |
| **comparable**                 | **可比的**：$a \preccurlyeq b$ 或 $b \preccurlyeq a$                                              |
| **incomparable**               | **不可比的**：既不 $a \preccurlyeq b$ 也不 $b \preccurlyeq a$                                         |
| **total/linear order**         | **全序/线序**：每对元素都**可比**的偏序                                                                     |
| **chain**                      | **链**：全序集的别称                                                                                 |
| **well-ordered set**           | **良序集**：每个非空子集都有一个最小元素                                                                       |
| **lexicographic order**        | **字典序**：$(a_1, a_2) \prec (b_1, b_2)$ 当且仅当 $a_1 \prec b_1$ 或 ($a_1=b_1 \land a_2 \prec b_2$) |
| **Hasse diagram**              | **哈斯图**：偏序关系的一种简化图形表示                                                                        |
| **maximal element**            | **极大元**：没有比它“更大”的元素, $\neg \exists x \in A (m \neq x \land m \preccurlyeq x)$                |
| **minimal element**            | **极小元**：没有比它“更小”的元素, $\neg \exists x \in A (m \neq x \land x \preccurlyeq m)$                |
| **greatest element**           | **最大元**：比集合中**所有**其他元素都大, $\forall x \in A (x \preccurlyeq g)$                               |
| **least element**              | **最小元**：比集合中**所有**其他元素都小, $\forall x \in A (l \preccurlyeq x)$                               |
| **upper bound**                | **上界** (对于子集B)：大于等于B中所有元素的元素                                                                 |
| **lower bound**                | **下界** (对于子集B)：小于等于B中所有元素的元素                                                                 |
| **least upper bound (LUB)**    | **最小上界** (上确界, supremum)                                                                     |
| **greatest lower bound (GLB)** | **最大下界** (下确界, infimum)                                                                      |
| **lattice**                    | **格**：每对元素都有唯一的 LUB 和 GLB 的偏序集                                                               |
| **topological sorting**        | **拓扑排序**：与偏序兼容的全序排列                                                                          |


- 

关键术语与结论