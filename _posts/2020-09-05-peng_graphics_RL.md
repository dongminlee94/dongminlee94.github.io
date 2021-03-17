---
title: "Graphics Series using RL by Xue Bin (Jason) Peng"
use_math: true
toc: true
toc_sticky: true
categories:
  - Paper
tags:
  - Paper
  - RL
---

## Outline
  
  1. [Dynamic Terrain Traversal Skills Using Reinforcement Learning (2015)](#1)
  2. [Terrain-Adaptive Locomotion Skills Using Deep Reinforcement Learning (2016)](#2)
  3. [Learning Locomotion Skills Using DeepRL: Does the Choice of Action Space Matter? (2017)](#3)
  4. [DeepLoco: Dynamic Locomotion Skills Using Hierarchical Deep Reinforcement Learning (2017)](#4)
  5. [DeepMimic: Example-Guided Deep Reinforcement Learning of Physics-Based Character Skills (2018)](#5)
  6. [SFV: Reinforcement Learning of Physical Skills from Videos (2018)](#6)

---

<a name="1"></a>

## Dynamic Terrain Traversal Skills Using Reinforcement Learning (2015)

- Author: [Xue Bin Peng](https://xbpeng.github.io/), Glen Berseth, [Michiel van de Panne](https://www.cs.ubc.ca/~van/)
- Proceeding: ACM Transactions on Graphics (TOG) (Proc. SIGGRAPH 2015)
- Paper: [PDF](https://dl.acm.org/doi/pdf/10.1145/2766910)
- Videos: [Main video](https://www.youtube.com/watch?v=mazfn4dHPRM), [Supplementary video](https://www.youtube.com/watch?v=RTuSHI5FNzg)

### Abstract

Physics-based character에 대한 locomotion skill은 대부분 flat terrain에서 이루어진다. 하지만 locomotion skill은 더 복잡한 지형에서 dynamic하고 momentum-based motion의 생성이 잘 이루어져야한다.

이 논문에서는 simulated character가 gaps, steps, walls를 지닌 지형을 매우 dynamic한 걸음걸이를 이용하여 이동할 수 있는 controller를 학습한다. 이러한 controller를 학습시킬 수 있도록 4가지 방법을 가진 강화학습을 사용한다.

- **Action representation**
- **Non-parametric approximation** of both the value function and the policy
- **Epsilon-greedy exploration**
- The learning of a good **state distance metric**

이러한 방법을 통해 21-link planar dog와 7-link planar biped가 bounding과 running의 걸음걸이를 이용하여 지형의 challenging sequence들을 navigate한다.

---

<a name="2"></a>

## Terrain-Adaptive Locomotion Skills Using Deep Reinforcement Learning (2016)

- Author: [Xue Bin Peng](https://xbpeng.github.io/), Glen Berseth, [Michiel van de Panne](https://www.cs.ubc.ca/~van/)
- Proceeding: ACM Transactions on Graphics (TOG) (Proc. SIGGRAPH 2016)
- Paper: [PDF](https://dl.acm.org/doi/pdf/10.1145/2897824.2925881)
- Videos: [Main video](https://www.youtube.com/watch?v=KPfzRSBzNX4), [Supplementary video](https://www.youtube.com/watch?v=A0BmHoujP9k)

---

<a name="3"></a>

## Learning Locomotion Skills Using DeepRL: Does the Choice of Action Space Matter? (2017)

- Author: [Xue Bin Peng](https://xbpeng.github.io/), [Michiel van de Panne](https://www.cs.ubc.ca/~van/)
- Proceeding: ACM SIGGRAPH / Eurographics Symposium on Computer Animation 2017
- Paper: [PDF](https://dl.acm.org/doi/pdf/10.1145/3099564.3099567)
- Video: [Main video](https://www.youtube.com/watch?v=L3vDo3nLI98)

---

<a name="4"></a>

## DeepLoco: Dynamic Locomotion Skills Using Hierarchical Deep Reinforcement Learning (2017)

- Author: [Xue Bin Peng](https://xbpeng.github.io/), Glen Berseth, KangKang Yin, [Michiel van de Panne](https://www.cs.ubc.ca/~van/)
- Proceeding: ACM Transactions on Graphics (TOG) (Proc. SIGGRAPH 2017)
- Paper: [PDF](https://dl.acm.org/doi/pdf/10.1145/3072959.3073602)
- Video: [Highlights](https://www.youtube.com/watch?v=G4lT9CLyCNw), [Main video](https://www.youtube.com/watch?v=hd1yvLWm6oA), [Supplementary video](https://www.youtube.com/watch?v=x-HrYko_MRU)

---

<a name="5"></a>

## DeepMimic: Example-Guided Deep Reinforcement Learning of Physics-Based Character Skills (2018)

- Author: [Xue Bin Peng](https://xbpeng.github.io/), [Pieter Abbeel](https://people.eecs.berkeley.edu/~pabbeel/), [Sergey Levine](https://people.eecs.berkeley.edu/~svlevine/), [Michiel van de Panne](https://www.cs.ubc.ca/~van/)
- Proceeding: ACM Transactions on Graphics (TOG) (Proc. SIGGRAPH 2018)
- Paper: [PDF](https://dl.acm.org/doi/pdf/10.1145/3197517.3201311)
- Video: [Main video](https://www.youtube.com/watch?v=vppFvq2quQ0), [Supplementary video](https://www.youtube.com/watch?v=8KdDwRLtNHQ)

---

<a name="6"></a>

## SFV: Reinforcement Learning of Physical Skills from Videos (2018)

- Author: [Xue Bin Peng](https://xbpeng.github.io/), Angjoo Kanazawa, Jitendra Malik, [Pieter Abbeel](https://people.eecs.berkeley.edu/~pabbeel/), [Sergey Levine](https://people.eecs.berkeley.edu/~svlevine/)
- Proceeding: ACM Transactions on Graphics (TOG) (Proc. SIGGRAPH Asia 2018)
- Paper: [PDF](https://dl.acm.org/doi/pdf/10.1145/3272127.3275014)
- Video: [Main video](https://www.youtube.com/watch?v=4Qg5I5vhX7Q), [Supplementary video](https://www.youtube.com/watch?v=_iXt7by4nU4)
