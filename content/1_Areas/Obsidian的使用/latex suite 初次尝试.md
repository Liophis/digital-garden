常见的数学公式使用 LaTeX 渲染的一些测试

### 1. 二次方程求根公式
$$
x = \frac{-b \pm \sqrt{b^2 - 4ac}}{2a}
$$

### 2. 欧拉公式
$$
e^{i\pi} + 1 = 0
$$

### 3. 勾股定理
$$
a^2 + b^2 = c^2
$$

### 4. 微积分基本定理
$$
\int_a^b f(x) \, dx = F(b) - F(a)
$$

### 5. 二项式定理
$$
(a + b)^n = \sum_{k=0}^n \binom{n}{k} a^{n-k} b^k
$$

### 6. 高斯积分
$$
\int_{-\infty}^\infty e^{-x^2} \, dx = \sqrt{\pi}
$$

### 7. 傅里叶变换
$$
\hat{f}(\xi) = \int_{-\infty}^\infty f(x) e^{-2\pi i x \xi} \, dx
$$

### 8. 黎曼 ζ 函数
$$
\zeta(s) = \sum_{n=1}^\infty \frac{1}{n^s}
$$

### 9. 矩阵乘法
$$
\mathbf{C} = \mathbf{A} \mathbf{B}, \quad C_{ij} = \sum_{k=1}^n A_{ik} B_{kj}
$$

### 10. 泰勒展开
$$
f(x) = \sum_{n=0}^\infty \frac{f^{(n)}(a)}{n!} (x - a)^n
$$

你可以将这些 LaTeX 代码复制到支持 LaTeX 渲染的编辑器（如 Overleaf、MathJax 或 Markdown 工具）中查看渲染效果。

$$
a^2+b^2=c^2
$$

##### 尝试编辑矩阵
$$ \begin{pmatrix} a & b \\ c & d \end{pmatrix} 
$$


### 尝试编辑代码块

```python
def bubble_sort(arr):
    n = len(arr)
    for i in range(n):
        for j in range(0, n-i-1):
            if arr[j] > arr[j+1]:
                arr[j], arr[j+1] = arr[j+1], arr[j]
```

> [!NOTE] 笔记
> 这是冒泡排序的核心逻辑，通过相邻元素的比较和交换。

> [!TIP] 优化提示
> 可以加入一个 flag 来检测某次遍历是否发生了交换，如果没有，说明数组已经有序，可以提前终止循环。

> [!WARNING] 注意
> 冒泡排序的时间复杂度是 $O(n^2)$，在数据量大时性能较差。

> [!warning]


第一次知道 Markdown 还有拥有 这个 `Callout` 功能



### 尝试使用 Latex Suit 插件

$$
\sum 1+
$$

$$
\frac{a}{   e+ i^\pi }=\sum_{n=0}{n^2+n+1}
$$


一定要记住这是一个需要熟练使用，并且尝试练习的工具