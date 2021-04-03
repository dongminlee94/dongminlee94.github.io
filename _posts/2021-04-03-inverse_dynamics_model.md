---
title: "Transfer from Simulation to Real World through Learning Deep Inverse Dynamics Model"
toc: true
toc_sticky: true
categories:
  - RL
---

<center> <img src='../../assets/images/inverse_dynamics/title.png' width="700"> </center>

- Author: Paul Christiano, Zain Shah, Igor Mordatch, Jonas Schneider, Trevor Blackwell, Joshua Tobin, [Pieter Abbeel](http://people.eecs.berkeley.edu/~pabbeel/), and Wojciech Zaremba
- Proceeding: arXiv preprint 2016
- Paper: [PDF](https://arxiv.org/pdf/1610.03518.pdf)

## Abstract

**Simulation과 real world의 discrepancy**를 줄이고자 한다. Simulation에서는 대부분 좋은 결과들을 얻지만, 실제로 real world로 넘어오는 경우 성능이 많이 떨어진다. 하지만 제어기의 세부적인 사항들이 아닌 friction, contact, mass, geometry 성질들에 대한 중요한 차이점들만 있는 경우, simulation을 통해 얻어진 states의 sequence는 real world에서도 유용하다.

따라서 이 논문에서의 접근은 simulator와 simulation-based control policy가 주어졌을 때, real robot에 control policy를 바로 적용하는 것이 아닌 **deep inverse dynamics model**을 이용하여 simulator에서 얻은 next state에 가장 적합하고 real system에 알맞은 action을 얻는다. 추가적으로, deep inverse dynamics model를 incremental하게 학습시키기 위해 data collection 방법도 제안한다.

---

## 1. Introduction

Simulation과 real world 간의 discrepancy를 줄이는 것은 상당히 challenge하다. 이 discrepancy를 줄이기 위한 방법은 크게 두 가지가 있다. 첫 번째는 조금 더 real world와 match되도록 simulator를 향상시키는 방법이다. 하지만 향상된 simulator에도 여전히 discrepancy가 존재하고, 더 정확한 simulation은 오히려 속도가 느리다는 단점이 있다. 두 번째 방법은 real world의 넓은 범위에 적용되도록 control policy를 향상시키는 방법이다. 하지만 대부분 넓은 범위는 어렵고, 하나의 specific real world에서만 잘 되는 경우들이 많다. 이 논문은 두 번재 방법을 adaptive method를 사용하여 풀고자 한다.

Abstract에서 말한 것과 같이 simulation을 통해 얻어진 states의 sequence는 real world에서도 유용하다. 직관적으로, simulation에서 얻어진 policy는 high-level gist(e.g., overall trajectory)를 주는 반면, lower-level datails(e.g., flutuating temperature or motor backlash)은 주지 못한다. 따라서 이 논문에서는 simulation에서는 trajectory에 관한 next state를 알려주고, 학습된 deep inverse dynamics model에서는 그 next state에 가장 적합한 real-world action을 내뱉어준다. <span style="color:red">결과적으로, simulation에서 얻어진 policy와는 다른 policy가 나올테지만, simluation과 real world에서는 동일한 next state를 가리키게 된다.</span> 이것이 이 논문의 핵심내용이다.

실험으로는 1) Simulation 1 to Simulation 2 Transfer: 하나의 simulation에서 다른 simulation으로 transfer하는 실험과 2) Simulation to Real Transfer with Fetch: 실제 로봇을 이용하여 simulation에서 real robot으로 transfer하는 실험을 하였다.

---

## 2. Method

### 2.1 Setting

- $s \in S$: a state in the state space
- $a \in A$: an action in the action space
- $o \in O$: an observation in the observation space
- $T(s,a) = s'$: a system forward dynamics
- $A_{source}$: the action space in the source environment
- $A_{target}$: the action space in the target environment
- $\tau = (o_1, a_2, o_2, a_2, ...)$: a trajectory
- $\tau_{H: H+k} = (o_H, a_H, ..., o_{H+k-1}, a_{H+k-1}, o_{H+k})$: the subsequence
- $\tau_{-k:}$: the most recent $k$ observations and $k-1$ actions in a trajectory
- $\pi$: a policy
- $a = \pi(\tau_{-k:})$: a mapping from observations to action, that depends on the last $k$ observations

이러한 setting을 가지고 목표로 하는 것은 <span style="color:red">target environment에서 잘 수행하는 policy ($\pi_{source}$가 아닌) $\pi_{target}$를 찾는 것</span>이다.

### 2.2 Transfer to the target environment

이 논문에서 핵심적으로 말하고자 하는 방법은 우리가 목표로 하는 target envionment에서 주어진 <span style="color:red">$\pi_{source}$의 high-level gist를 transfer하는 방법</span>이다. 아래의 그림을 보자.

<center> <img src='../../assets/images/inverse_dynamics/fig1.png' width="800"> </center>
<br/>
전체적인 과정을 보자.

1) 처음에 할 일은 history $\tau_{-k:}$를 얻는 일이다. 이 history $\tau_{-k:}$는 simulation이 아닌 real world system에서 얻어진다.

2) 이렇게 얻은 history를 이용하여 주어진 source environment의 source policy를 통해 action $a_{source} = \pi_{source} (\tau_{-k:})$를 얻는다.

3) 다음으로, 얻어진 action을 이용하여 source environment의 forward dynamics를 통해 next observation $o_{next} = o(T_{source} (\tau_{-k:}))$를 얻는다.

4) 마지막으로 history $\tau_{-k:}$와 source environment에서 얻어진 next observation $o_{next}$를 이용하여 target environment에서 학습된 inverse dynamics model $\phi$를 통해 action $a_{target} = \phi (\tau_{-k:}, o_{next})$를 얻는다.

모든 과정을 정리하면 아래와 같다.

$$\pi_{target} (\tau_{-k:}) = \phi \Big( \tau_{-k:}, o \big( T_{source} (\tau_{-k:}, \pi_{source} (\tau_{-k:})) \big) \Big)$$

만약 학습된 inverse dynamics model이 충분히 정확하다면, <span style="color:red">$\pi_{target} (\tau_{-k:})$를 취한 후의 next observation $o_{target}$은 $o_{next}$와 비슷할 것이다.</span>

이러한 접근이 의미가 있으려면, source environment와 target environment가 같은 degrees of freedom을 가지는 것으로 가정해야하지만, 이 논문의 방법론은 <span style="color:red">두 환경 사이의 다른 degrees of freedom에서도 가능하다.</span> 다시말해 $\pi_{source}$와 $\pi_{target}$의 action들은 서로 다를 수도 있을 뿐만 아니라 action space간의 dimensionality가 서로 다를 수도 있다. 왜냐하면 하고자 하는 것은 결국 <span style="color:red">$\pi_{source}$와 $\pi_{target}$의 action들을 같게 하는 것이 아닌 next observation $o_{target}$과 $o_{next}$를 같게 만들고 싶기 때문이다.</span> 하지만 반대로 두 환경 사이의 observation의 dimensionality는 같아야 할지도 모른다.

### 2.3 Training of the inverse dynamics model

Inverse dynamics model의 input과 output에 대해 살펴보자. 먼저 input에는 이전 k만큼의 history $\tau_{H:H+k}$와 next observation $o_{H+k+1}$이고, output으로는 neural network $\phi$가 내뱉어주는 action $a_{H+k}$이다. 정리하면 다음과 같다.

$$\phi : (o_H, a_H, o_{H+1}, a_{H+1}, ..., a_{k+H-1}, o_{k+H}, o_{k+H+1}) \mapsto a_{k+H}$$

여기서 history $\tau_{H:H+k}$는 왜 같이 넣어주어야할까? 같이 넣으면 무슨 역할을 할까?

질문에 대한 답변으로 논문에서는 neural network $\phi$가 dynamics에 존재하는 중요한 latent factors or temporal dependencies 암묵적으로 추론할 수 있다고 한다. 쉽게 말해 real world에서 여러가지 latent factors가 존재하는 lower-level details(friction, stiction, backlash, hysteresis, precise measurements, precise deformation, etc.)을 추론하기 위해서는 단기간의 state로는 힘들다는 것이다. 따라서 real world에서의 최근 $k$ 동안의 states와 actions를 보면서 latent factors or temporal dependencies를 추론하여 lower-level details을 파악하고자 하는 것이다.

### 2.4 Data collection / Exploration

**Data collection**: Training을 위한 data를 모으기 위해 사전에 준비해놓은 preliminary inverse dynamics model $\phi$을 통해 preliminary policy $\pi_{target}$을 사용한다. preliminary policy $\pi_{target}$를 이용하여 $\tau_{H:H+k}$를 생성한다. 그리고 모은 history로 새로운 policy $\pi_{target}$를 학습하는 데에 사용된다.

**Exploration**: preliminary policy $\pi_{target}$를 이용하여 모은 history의 action에 exploration을 주기 위해 noise를 추가한다. noise를 주는 이유는 training data를 다양하게 만들어서 inverse dynamics model을 더 빠르고 robust하게 학습하도록 만들기 위함이다. 이 때 noise를 너무 많이 주게되면 target trajectories(preliminary policy $\pi_{target}$를 이용하여 모은 history)에서 너무 먼 data들이 모아지고, noise를 너무 적게 주게되면 불충분한 exploration 때문에 inverse dynamics model이 매우 느리게 향상된다. 추가로, 모든 time step에 noise를 추가하는 것은 좋지 않다. 추가적으로, noise를 추가한 target trajectories 실행이 source environment에서 발생할 수 있는 것으로부터 매우 멀리 벗어나기 시작하면 reset을 하는 것이 더 효율적이라고 한다.

## 3. Experiments

### 3.1 Simulated Environments – Sim1 to Sim2 transfer

<center> <img src='../../assets/images/inverse_dynamics/fig2.png' width="700"> </center>

<center> <img src='../../assets/images/inverse_dynamics/fig3.png' width="600"> </center>

### 3.2 Physical interaction – Sim to Real transfer

<center> <img src='../../assets/images/inverse_dynamics/fig4.png' width="700"> </center>
