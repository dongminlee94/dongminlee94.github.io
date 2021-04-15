---
title: "Forward KL vs Reverse KL"
toc: true
toc_sticky: true
categories:
  - Math
  - ML
---

## 1. Abstract

- Foward KL divergence
  - Zero-avoiding, mean-seeking, inclusive, spreading
  - 핵심 수식: $\log \frac{p(x)}{q(x)}$
  - 만약 학습시켜야 할 parameterized 분포 $Q$가 target 분포 $P$와 매우 가까울 수 있는 경우(최소화시켰을 때 모양이 매우 비슷할 수 있는 경우), 또는 target 분포의 모든 부분을 커버해야하는 경우에 사용
  - 따라서 분포 $P$에서 $p(x)$가 0이 되는 지점에서의 예측값을 크게 신경쓰지 않는다. (전체 숲을 보는 것)
- Reverse KL divergence
  - Zero-forcing, mode-seeking, exclusive, sharp
  - 핵심 수식: $\log \frac{q(x)}{p(x)}$
  - 만약 학습시켜야 할 parameterized 분포 $Q$가 target 분포 $P$와 가까울 수 없는 경우(최소화시켰을 때 모양이 비슷하게 나오지 않을 수 있는 경우), 또는 target 분포의 일부 부분을 캡쳐해도 좋은 경우에 사용
  - 따라서 분포 $Q$에서 $q(x)$가 0이 되는 지점에서의 예측값을 크게 신경쓰지 않는다. (각각의 나무 중 하나의 나무를 보는 것)
- 기본적으로 KL divergence라고 하면 forward 방식을 가리키며, Variational AutoEncoder(VAE)에는 reverse 방식을 사용한다.

---

## 2. KL Divergence

Definition은 다음과 같다.

For probability distribution $P$ and $Q$ defined on the same probability space, $\mathcal{X}$, the relative entropy from $Q$ to $P$ is defined to be

$$\begin{align} \notag D_{KL}(P \parallel Q) &:= H(P,Q) - H(P)
\\ \notag &= \big(- \sum_{x \in \mathcal{X}} p(x) \log q(x)\big) - \big(- \sum_{x \in \mathcal{X}} p(x) \log p(x)\big)
\\ \notag &= \sum_{x \in \mathcal{X}} p(x) \big( \log p(x) - \log q(x) \big)
\\ \notag &= \sum_{x \in \mathcal{X}} p(x) \big( \log \frac{p(x)}{q(x)} \big)
\end{align}$$

- 분포 $P$에서 샘플링된 데이터를 이용한 분포 $Q$에서의 정보량 - 분포 $P$에서 샘플링된 데이터를 이용한 분포 $P$에서의 정보량
- 데이터의 분포인 $P$ 대신 다른 분포 $Q$를 사용해서 인코딩했을 때 필요한 추가 bit수를 나타낸다
- $D_{KL} (P \parallel Q) \neq D_{KL} (Q \parallel P)$, symmetric하지 않다
- $D_{KL} (P \parallel Q) \geq 0$, ($P=Q$일 때 $0$)

---

## 3. Forward KL Divergence

Forward KL의 Definition은 다음과 같다.

$$D_{KL} (P \parallel Q) := \sum_x p(x) \big( \log \frac{p(x)}{q(x)} \big)$$

두 확률분포간 거리를 최소화시키고자 할때 보통 $P$를 target, true 분포가 들어가고, $Q$는 최적화시키고자 하는 paramterized 분포가 들어가고 $P$와 가깝도록 만들고자 한다.

Forward KL은 **zero-avoiding, mean-seeking, inclusive, spreading**등의 이름으로 불린다. 그 이유는 다음과 같다.

Forward KL의 핵심 수식은 $\log \frac{p(x)}{q(x)}$이다. 이때 $q(x)$가 0인 지점에서 만약 $p(x)$의 값이 0보다 크다면 어떻게 될까? $0.0001 / 0 = \infty$이므로, 그 지점에서의 길이비는 무한대가 될 것이다.

따라서 Forward KL을 minimize하려면, 이러한 케이스를 무조건 막아야만 한다. 다시 말해 $p(x)$의 값이 조금이라도 존재하는 지점이 있으면, <span style="color:red">무조건 모델의 분포 $Q$로 해당 범위를 커버해서 $Q$가 0이 되는 케이스를 반드시 막아야한다.</span> 그러려면 최대한 분포 $Q$가 0이 되는걸 피해야하기 때문에 분포 $P$를 포함해야하고, 그러기 위해 넓게 퍼지게 된다. 결국 zero-avoiding, mean-seeking, inclusive, spreading의 속성이 되는 것이다.

그 결과 분포는 아래와 같이 fitting하게 된다.

<center> <img src='../../../assets/images/kl/forward_kl.png' width="700"> </center>

최소화된 이후의 KLD 값은 상당히 클 수가 있다. 왜냐하면 $p(x) > 0$인 전체 범위를 커버하려고 하기 때문에 $q(x)$를 정확하게 모델링하지 않으면 위와 같은 문제가 생긴다. 그래서 위의 경우에는 두 개의 Gaussian의 mixture model로 정하면 깔끔하게 맞을 것이다.

---

## 4. Reverse KL Divergence

Reverse KL의 Definition은 다음과 같다.

$$D_{KL} (Q \parallel P) := \sum_x q(x) \big( \log \frac{q(x)}{p(x)} \big)$$

Forward KL과 마찬가지로, 두 확률분포간 거리를 최소화시키고자 할때 보통 $P$를 target, true 분포가 들어가고, $Q$는 최적화시키고자 하는 paramterized 분포가 들어가고 $P$와 가깝도록 만들고자 한다.

Reverse KL은 **zero-forcing, mode-seeking, exclusive, sharp**등의 이름으로 불린다. 그 이유는 다음과 같다.

Reverse KL의 핵심 수식은 $\log \frac{q(x)}{p(x)}$이다. 이 경우에도 분모 $p(x)$가 0인 지점에서, $q(x)$의 값이 0보다 크다면 어떻게 될까? 위와 동일한 원리로 $0.0001 / 0 = \infty$이므로, 그 지점에서의 길이비는 무한대가 될 것이다.

마찬가지로 Reverse KL을 minimize하기 위해서는 이러한 케이스를 피해야만 한다. 그런데 우리는 분포 $P$를 건드릴 수 없다. 우리는 모델의 분포인 $Q$만 학습할 수 있다. <span style="color:red">결국 $p(x)$가 0인 지점에 대해서 우리가 취할 수 있는 행동은 오직 그 지점의 $q(x)$ 값 또한 0이 되도록 만들어야 한다.</span> 즉, 여기서는 반대로 $p(x)$가 0인 지점에서 반드시 $q(x)$가 0이 되어야한다. 따라서 $P$ 분포를 절대 퍼트려서는 안되고, 샤프하게 한 곳으로 모아야한다.

그러면 분포 $Q$를 어디에 모으는 것이 가장 좋을까? $\log \frac{q(x)}{p(x)}$을 보면, $p(x)=q(x)$일 때 이 값이 $\log 1 = 0$이 된다. 즉, $Q$를 최대한 샤프하게 모은 다음 $P$ 분포와 $Q$ 분포를 최대한 똑같이 맞춰야하는 것이다. 이러한 원리로 <span style="color:red">$P$ 분포에 존재하는 여러 개의 mode들 중, 하나의 mode에만 집중해서 $Q$ 분포를 맞추게 하는 것이다.</span> 이러한 이유로 zero-forcing, mode-seeking, exclusive, sharp의 속성을 갖게 된다.

<center> <img src='../../../assets/images/kl/reverse_kl.png' width="700"> </center>

마지막으로 예를 하나 들어보자. 위의 그림처럼 분포 $P$의 왼쪽 분포가 남자 사진, 오른쪽 분포가 여자 사진이라고 하자. 그 때 생성 모델을 만든다고 하면 forward KL을 사용한 $Q$가 좋을까? 아니면 reverse KL을 사용한 $Q$가 좋을까? 당연히 reverse KL을 사용한 분포를 원할 것이다. 우리는 남자, 여자 사이에 있는 이상한 사진을 생성하는 것이 아닌 남자면 남자, 여자면 여자인 어떠한 명확한 사진을 생성하고 싶기 때문이다.

---

## References

- [Agustinus Kristiadi's Blog - KL Divergence: Forward vs Reverse?](https://wiseodd.github.io/techblog/2016/12/21/forward-reverse-kl/)
- [Jaeyoung's Blog - KL Divergence](https://en.wikipedia.org/wiki/Entropy_(information_theory))
- [Information Theory, Distance Metric on PDF](https://en.wikipedia.org/wiki/Cross_entropy)
- [KL Divergence, 순서가 중요할까요?](https://www.youtube.com/watch?v=c5nTnvGHG4E)
