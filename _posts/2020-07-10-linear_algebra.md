---
title: "Linear Algebra"
toc: true
toc_sticky: true
categories:
  - Math
---

## Outline
  
- Span
- Linear Independence
- Rank
- Inner Product
- Norm

---

## Span (= Subspace)

- **Definition**: Given a set of vectors $v_1, \cdots, v_p \in \mathbb{R}^n$, Span ${v_1, \cdots, v_p}$ is defined as the set of all linear combinations of $v_1, \cdots, v_p$ (벡터들의 집합이 주어졌을 때, 선형 결합으로 나올 수 있는 모든 벡터들의 집합)
- That is, Span ${v_1, \cdots, v_p}$ is the collection of all vectors that can be written in the form with arbitary scalars $c_1, \cdots, c_p$

$$c_1 v_1 + c_2 v_2 + \cdots + c_p v_p$$

---

## Linear Independence

### (Practical) Definition

- Given a set of vectors $v_1, \cdots, v_p \in \mathbb{R}^n$, check if $v_j$ can be represented as a linear combination of the previous vectors ${v_1, v_2, \cdots, v_{j-1}}$ for $j = 1, \cdots, p$, e.g.,

$$v_j \in Span \,\, \{v_1, v_2, \cdots, v_{j-1}\} \,\, for \,\, some \,\, j = 1, \cdots, p$$

- If at least one such $v_j$ is found in $\{v_1, \cdots, v_{j-1}\}$, then $\{v_1, \cdots, v_p\}$ is **linearly dependent**
- If no such $v_j$ is found in $\{v_1, \cdots, v_{j-1}\}$, then $\{v_1, \cdots, v_p\}$ is **linearly independent**

### (Formal) Definition

- Consider $x_1 v_1 + x_2 v_2 + \cdots + x_p v_p = 0$ (homogeneous linear system)
- Obviously, one solution is $x = [x_1, x_2, \cdots, x_p] = [0, 0, \cdots, 0]$, which we call a trivial solution
- $v_1, \cdots, v_p$ are **linearly independent** if this is the only solution
- $v_1, \cdots, v_p$ are **linearly dependent** if this system also has **other nontrivial solutions**, e.g., at least one $$x_i$$ being **nonzero**

---

## Rank

- Definition: the rank of a matrix $A$, denoted by rank $A$, is the dimension of the column space of $A$

$$rank \,\, A = dim \,\, Col \,\, A$$

---

## Inner Product

- Given $u, v \in \mathbb{R}^n$, we can consider $u$ and $v$ as $n \times 1$ matrices
- The number $u^T v$ is called the **inner product** or **dot product** of $u$ and $v$, and it is written as 

$$u \cdot v = u^T v = \sum_{i=1}^n u_i v_i = u_1 v_1 + \cdots + u_n v_n$$

---

## Norm

- Definition: the **length (or norm)** of $v$ is the non-negative scalar $\| v \|$ defined as the square root of $v \cdot v$ (inner product 후 루트)

$$\| v \| = \sqrt{v \cdot v} = \sqrt{v^T v} = \sqrt{v_1^2 + v_2^2 + \cdots + v_n^2}$$

$$\| v \|^2 = v \cdot v$$
