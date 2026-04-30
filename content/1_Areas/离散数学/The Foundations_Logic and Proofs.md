$\begin{pmatrix} 1&2&3 \\ 2&2&3 \\ 1&1&2 \end{pmatrix}$

## 一、Propositional Logic 命题逻辑

### 1. The Language of Propositions

- Propositions
- Connectives
    - Negation ¬
    - Conjunction ∧
    - Disjunction ∨
    - Implication (蕴涵) →; **contrapositive (逆否命题), inverse (逆命题), converse(否命题)
    - Biconditional↔
- Truth Tables

### 2. Applications

- System Specifications (系统规约)
- Boolean Searching (布尔搜索)
- Logic Puzzles (逻辑谜题)
- Logic Circuits (逻辑电路)

### 3. Logical Equivalences

Tautologies, Contradictions, and Contingencies

(重言式、矛盾式和可能式)

- 通过赋值的方式来验证是否为逻辑等值式
![[Pasted image 20250911090648.png]]
 ^af5530

- 假言易位（逆否命题）、归谬论 、吸收律
    
    输出律（蕴含不确定满不满足结合律）
    

Showing Logical Equivalence (证明等价性)

### 4. Normal Forms (范式)

极小项的定义：可以单纯理解为一次赋值，极大项同理

- Principal Disjunctive Normal Form (主析取范式)
- Principal Conjunctive Normal Form (主合取范式)

Propositional Satisfiability (命题可满足性) (e.g., Sudoku Example 数独例子)

## 二、Predicate Logic 谓词逻辑 First-Order Logic (FOL), Predicate Calculus

- SUMMARY
    
    The Language of Quantifiers Logical Equivalences Nested Quantifiers Translation from Predicate Logic to English Translation from English to Predicate Logic
    

### 1. Predicate and Quantifiers

- Predicates
- Variables
- Quantifiers (量词)
    - Universal Quantifier (∀)
    - Existential Quantifier (∃)
- Negating Quantifiers
- De Morgan’s Laws for Quantifiers (量词的德·摩根律)
    - $¬∀xP(x)≡∃x¬P(x)$

### 2. Nested Quantifiers 嵌套量词

- Nested Quantifiers (嵌套量词)
- Order of Quantifiers (量词的顺序)
- Negating Nested Quantifiers (否定嵌套量词)

### 3. Logical Equivalences （KEY POINT）

1. 量词与联结词
    ![[Pasted image 20250911090757.png]]
    ![[Pasted image 20250911090746.png]]![[Pasted image 20250911090815.png]]
2. 量词辖域收缩与扩张 ![[Pasted image 20250911090833.png]]
推导前束范式

## 三、Rules of Inference 推理规则

- SUMMARY
    
    Valid Arguments Inference Rules for Propositional Logic Using Rules of Inference to Build Arguments Rules of Inference for Quantified Statements Building Arguments for Quantified Statements
    

### 1. Inference Rules for Propositional Logic

- Common Rules of Inference 需要记忆名字
    - Addition (附加律): A ⇒ (A∨B)
    - Simplification (化简律): (A∧B) ⇒ A
    - **Modus Ponens (假言推理**): (A→B)∧A ⇒ B
    - Modus Tollens (拒取式): (A→B)∧¬B ⇒ ¬A
    - Disjunctive Syllogism (析取三段论): (A∨B)∧¬B ⇒ A
    - **Hypothetical Syllogism (假言三段论)**: (A→B)∧(B→C) ⇒ (A→C)
    - Equivalence Syllogism (等价三段论): (A↔B)∧(B↔C) ⇒ (A↔C)
    - Constructive Dilemma (构造性两难): (A→B)∧(C→D)∧(A∨C) ⇒ (B∨D) 特殊形式
    - Destructive Dilemma (破坏性两难): (A→B)∧(C→D)∧(¬B∨¬D) ⇒ (¬A∨¬C)

### 2. 其他推理规则

- Premise Introduction (前提引入), Conclusion Introduction (结论引入), Replacement Rule (置换规则), CP Rule (CP规则), Reductio ad Absurdum Rule (反证推理规则)
- 前提引入规则：在证明的任何步骤上都可以引入前提 结论引入规则：在证明的任何步骤上所得到的结论都可以做为后继证明的前提 置换规则：在证明的任何步骤上，命题公式中的子公式都可以用与之等值的公式置换，得到公式序列中又一个公式

## 四、Introduction of Proofs 证明导论

### **1. Introduction to Proofs Summary**

- Mathematical Proofs (数学证明)
- Forms of Theorems (定理的形式)
- Direct Proofs (直接证明): Assume p is true, and prove q (假设 p 为真，证明 q 为真)
- Indirect Proofs (间接证明)
    - Proof of the Contrapositive (反证法): Assume ¬q, and prove ¬p (假设 ¬q 为真，证明 ¬p 为真)
    - Proof by Contradiction (矛盾证明法): assumes Q is false, derives a contradiction (usually of the form P ∧ ¬P), which establishes ¬Q → 0 (假设 Q 为假，推导出矛盾 - 通常形如 P ∧ ¬P，从而证明 ¬Q → 0)
        - Example: Use a proof by contradiction to give a proof that √2 is irrational (例子：证明 √2 是无理数)

### **2. Proof Methods and Strategy Summary (证明方法和策略摘要)**

- Proof by Cases (分情况证明): Show p ↔ (a ∨ b), and (a → q) and (b → q) (显示 p ↔ (a ∨ b)，以及 (a → q) 和 (b → q))
- Existence Proofs (存在性证明) (∃x P(x))
    - Constructive (构造性证明)
    - Nonconstructive (非构造性证明)
- Disproof by Counterexample (反例证明)
- Nonexistence Proofs (非存在性证明)
- Uniqueness Proofs (唯一性证明)
- Proof Strategies (证明策略)
- Proving Universally Quantified Assertions (证明全称量化断言)

**开放性问题与著名定理**：提及了一些著名的数学猜想（如冰雹猜想 Hailstone Problem）和定理（如费马大定理 Fermat's Last Theorem），以及欧拉猜想的反例。

**其他证明方法预告**：提到了后续会学习的数学归纳法、结构归纳法、康托尔对角线法和组合证明等