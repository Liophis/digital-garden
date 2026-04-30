1. **函数的定义与基本概念**
    - 函数是从集合A（定义域）到集合B（陪域）的映射，表示为f: A → B，每个x ∈ A有唯一的y = f(x) ∈ B。
    - 部分函数（Partial Function）允许某些x ∈ A没有对应的y ∈ B。
    - 函数的图形表示：有序对集合{(a, f(a)) | a ∈ A}。
2. **函数的组成部分**
    - **定义域（Domain）**：输入集合A。
    - **陪域（Codomain）**：输出集合B。
    - **值域（Range）**：实际输出的集合f(A) ⊆ B。
    - **像（Image）与源像（Preimage）**：y = f(x)是x的像，x是y的源像。

![[Pasted image 20250911091555.png]]

由这个例子我们可以很直接的认识到，以上概念的关系

1. **特殊函数类型**
    - **内射（Injection）**：one to one function 每个y ∈ Range(f)有唯一x ∈ A。
    - **满射（Surjection）**：onto function 陪域等于值域（f(A) = B）。
    - **双射（Bijection）**：既是内射又是满射，可逆。

The Identity Function

Inverse Functions

几个特殊函数

Floor and ceiling, Factorial function, Characteristic function, Mod-n function, Partial function…

![[Pasted image 20250911091611.png]]

![[Pasted image 20250911091617.png]]

---

**复合函数 （ Function Composition）**

- 定义：$(f∘g)(x)=f(g(x))$ 注意顺序
- 性质：结合律 $(h∘g)∘f=h∘(g∘f)(h∘g)∘f=h∘(g∘f)$，但不满足交换律。

---

- 序列与序列的求和
    
    ![[Pasted image 20250911091649.png]]
    
    ![[Pasted image 20250911091657.png]]
    ![[Pasted image 20250911091705.png]]
    ![[Pasted image 20250911091715.png]]
    

---

### Cardinality of Sets

- **基数定义**：集合的大小，通过双射比较。
- **可数集（Countable）**：与ℕ存在双射（如ℤ、ℚ）。
- **不可数集（Uncountable）**：如实数集ℝ（康托尔对角线法证明）。P（N）
![[Pasted image 20250911091741.png]]