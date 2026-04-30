## Set 2.1

- Definition of Sets
- Special sets: $𝓡,ℕ,ℤ,ℚ$
- Truth set :

Given a predicate _P_, and a domain _D_, The _**truth set**_ of _P_(_x_) is denoted by $\{x∈D | P(x)\}$

- Relations: $x \in S, S \subset T, S \subsetneqq T, S=T$
- Cardinality
- Power set (幂集) : 以所有子集的为元素的集合

$A = \{a, b\},P(A) = \{ \emptyset, \{a\}, \{b\}, \{a,b\}\}$

- Cartesian Product == Tuple

The _Cartesian product_ of _A_ with _B_, denoted A x B is the set of ordered pairs $\{<a, b> | a \in A \wedge b\in B\}$

## Set operations 2.2

- **Venn Diagrams (韦恩图)**:

一般方便处理 两到三个集合之间的运算

- **The Addition Principle (加法原理) 容斥原理**
    
- Algebraic Properties of Set Operations ****
    
    - Commutative properties (交换律): A ∪ B = B ∪ A, A ∩ B = B ∩ A
    - Associative properties (结合律): (A ∪ B) ∪ C = A ∪ (B ∪ C), (A ∩ B) ∩ C = A ∩ (B ∩ C)
    - Distributive properties (分配律): A ∩ (B ∪ C) = (A ∩ B) ∪ (A ∩ C), A ∪ (B ∩ C) = (A ∪ B) ∩ (A ∪ C)
    - Idempotent properties (等幂律): A ∪ A = A, A ∩ A = A
    - Properties of the Complement (补集的性质): A = A
    - Properties of a Universal Set (全集的性质): A ∪ U = U, A ∩ U = A
    - Properties of the Empty Set (空集的性质): A ∪ ∅ = A, A ∩ ∅ = ∅
- **Methods for Proving Set Identities**
    
    - Method 1: Mutual subsets
    - Method 2: Use set builder notation
    - Method 3: Membership Tables (成员表法),comparing it to truth tables and explaining how to use 1s and 0s to indicate membership and non-membership, respectively.
    
    这就是成员表方法：有一点蠢，但是也是我没有想到的方式。
    
    ![[Pasted image 20250911091224.png]]
    
- **Generalized Unions & Intersections (广义并与交)**
    
    - **Representing Sets with Bit Strings (用位串表示集合)**operations correspond to bitwise operations (OR, AND, NOT).
        - Example: U = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10}, odd integers are represented as 1010101010, even integers as 0101010101, and integers not exceeding 5 as 1111100000.
- **Multisets (多重集)**: 允许重复，记录重复次数的集合