---
title: "Information Theory"
toc: true
toc_sticky: true
categories:
  - Math
  - ML
---

## 1. Entropy

### 1.1 Definition

Given a discrete random variable $X$, with possible outcomes $x_1, ..., x_n$, which occur with probability $p(x_1), ..., p(x_n)$, the entropy of $X$ is formally defined as:

$$\begin{align} \notag H(X) &:= - \sum_{i=1}^n p(x_i) \log_2 p(x_i)
\\ \notag &= \mathbb{E}_{X \sim P} [-\log_2 (P(X))]  
\end{align}$$

- **어떠한 시스템 내 정보의 불확실성 정도를 나타내는 지표이다**
- 어떤 분포로부터 생성되는 정보를 인코딩할 때 평균적으로 차지하는 bit수를 나타낸다
- 자주 일어나지 않는 사건은 자주 발생하는 사건보다 정보량이 많다
- 발생 가능 확률 $P$가 낮을수록 불확실하게 되고, 이 때 '정보가 많다', '엔트로피가 높다'고 표현한다
- 독립 사건은 추가적인 정보량을 가짐. 예를 들어 $n$개의 선을 사용해서 데이터를 전송하면 한 개의 선을 사용하는 보다 $n$배의 정보를 전달할 수 있다

$H(X)$는 다음의 조건을 만족해야한다.

1. $H$는 연속이다
2. $H$는 균등분포에서 가장 크다
3. 두 개의 개별 사건을 관측하는 것은 각각의 불확실성의 정도를 합하는 것이다

---

## 2. Cross Entropy

### 2.1 Definition

The cross entropy of the distribution $Q$ relative to a distribution $P$ over a given set is defined as follows:

$$\begin{align} \notag H(P,Q) &:= - \sum_x p(x) \log q(x)
\\ \notag &= \mathbb{E}_{X \sim P} [-\log (Q(X))]
\end{align}$$

- **분포 $P$로부터 샘플링된 데이터를 분포 $P$ 대신 분포 $Q$를 사용해서 인코딩할 때 필요한 평균 bit수이다**
- $H(P) \leq H(P,Q)$를 항상 만족한다
- 샘플랭된 데이터의 정보량이 다른 분포 $Q$에서 어느 정도인지를 측정할 수 있다. 정보량이 비슷하면 값이 작게 나오고, 다르면 값이 높게 나온다
- 딥러닝에서 Loss function으로 사용 시, 고정되어 있는 분포 $P$가 타겟이며 분포 $Q$를 예측 값으로 사용하여 현재 예측 값이 얼마나 타겟에 가까운지를 나타낸다

---

## 3. KL Divergence

### 3.1 Definition

For probability distribution $P$ and $Q$ defined on the same probability space, $\mathcal{X}$, the relative entropy from $Q$ to $P$ is defined to be

$$\begin{align} \notag D_{KL}(P \parallel Q) &:= H(P,Q) - H(P)
\\ \notag &= \big(- \sum_{x \in \mathcal{X}} p(x) \log q(x)\big) - \big(- \sum_{x \in \mathcal{X}} p(x) \log p(x)\big)
\\ \notag &= -\sum_{x \in \mathcal{X}} p(x) \big( \log q(x) - \log p(x) \big)
\\ \notag &= \sum_{x \in \mathcal{X}} p(x) \big( \log p(x) - \log q(x) \big)
\\ \notag &= -\sum_{x \in \mathcal{X}} p(x) \big( \log \frac{q(x)}{p(x)} \big)
\\ \notag &= \sum_{x \in \mathcal{X}} p(x) \big( \log \frac{p(x)}{q(x)} \big)
\\ \notag &= \mathbb{E}_{X \sim P} \big[ -\log(Q(X))- \big( -\log(P(X)) \big) \big]
\\ \notag &= \mathbb{E}_{X \sim P} \big[ \log(P(X))- \log(Q(X)) \big]
\end{align}$$

- 분포 $P$에서 샘플링된 데이터를 이용한 분포 $Q$에서의 정보량 - 분포 $P$에서 샘플링된 데이터를 이용한 분포 $P$에서의 정보량
- **데이터의 분포인 $P$ 대신 다른 분포 $Q$를 사용해서 인코딩했을 때 필요한 추가 bit수를 나타낸다**
- 두 분포 사이의 거리를 측정할 수 있다
- $D_{KL} (P \parallel Q) \neq D_{KL} (Q \parallel P)$, symmetric하지 않다
- $D_{KL} (P \parallel Q) \geq 0$, ($P=Q$일 때 $0$)

> Classification 할 때 왜 loss 함수는 cross entropy 일까?
> 분포 $P$를 근사하기 위해 분포 $Q$를 만들었고, 엔트로피 $H(P)$는 분포 $Q$와 무관하다. 즉, 분포 $Q$의 parameter로 미분하면 $H(P)$는 사라진다.
> 그래서 $H(P,Q)$를 loss 함수로 쓴다. 이는 KL Divergence를 쓰는 것과 같다.

---

## 4. Conditional Entropy

### 4.1 Definition

The conditional entropy of $Y$ given $X$ is defined as

$$\begin{align} \notag H(Y|X) &:= H(X,Y) - H(X)
\\ \notag &= - \sum_x \sum_y p(x,y) \log \frac{p(x,y)}{p(x)}
\end{align}$$

- **어떤 정보가 주어졌을 때 인코딩 하는데에 평균적으로 필요한 추가 bit수를 나타낸다**
- 여기서 $H(X,Y)$는 cross entropy가 아닌 joint entropy
- Conditional entropy는 joint entropy $H(X,Y)$와 marginal entropy $H(X)$의 차

### 4.2 Derivation

$X$와 $Y$가 독립이 아니고 $X=x$라는 사실을 안다면, $P(Y \mid X=x) \neq P(Y)$이다. 이를 수식으로 나타내면 다음과 같다.

$$H(Y|X=x) =-\sum_{y \in Y} (y|x) \log p(y|x)$$

이를 모든 $X$에 평균을 내면 conditional entropy가 나온다.

$$\begin{align} \notag H(Y|X) &= \sum_x p(x) H(Y|X=x)
\\ \notag &= - \sum_x p(x) \big( \sum_y p(y|x) \log p(y|x) \big)
\\ \notag &= - \sum_x \sum_y \big( p(x) p(y|x) \log p(y|x) \big)
\\ \notag &= - \sum_x \sum_y p(x,y) \log p(y|x)
\\ \notag &= - \sum_x \sum_y p(x,y) \log \frac{p(x,y)}{p(x)}
\\ \notag &= - \sum_x \sum_y p(x,y) \big( \log p(x,y) - \log p(x) \big)
\\ \notag &= - \sum_x \sum_y p(x,y) \log p(x,y) + \sum_x \sum_y p(x,y) \log p(x)
\\ \notag &= H(X,Y) + \sum_x \sum_y p(x,y) \log p(x)
\\ \notag &= H(X,Y) + \sum_x \log p(x) \sum_y p(x,y)
\\ \notag &= H(X,Y) + \sum_x p(x) \log p(x)
\\ \notag &= H(X,Y) - H(X)
\end{align}$$

---

## 5. Mutual Information

### 5.1 Definition

Let $(X, Y)$ be a pair of random variables with values over the space $\mathcal{X} \times \mathcal{Y}$. If their joint distribution is $P_{(X,Y)}$ and the marginal distributions are $P_X$ and $P_Y$, the mutual information is defined as

$$\begin{align} \notag I(X;Y) &:= H(Y) - H(Y|X)
\\ \notag &= H(X) - H(X|Y)
\\ \notag &= D_{KL} \big( P_{(X,Y)} \parallel P_X P_Y \big)
\end{align}$$

- **$X$와 $Y$ 두 변수 사이에 서로 얼마나 연관이 있는 지를 알 수 있다**
- $H(Y) - H(Y \mid X)$는 $X$와 $Y$가 **공유하고 있는 정보가 얼마나 큰 지**를 나타낸다
- $H(Y \mid X)$에서 $X$가 $Y$에 대해 충분한 정보를 제공한다면 $H(Y \mid X)$는 매우 작을 것이다
- 만약 X와 Y가 독립이라면, $H(Y) = H(Y \mid X)$일 것이고, 위의 값은 0이 될 것이다
- 반대로 $X = Y$이라면, $H(Y) - H(Y \mid X) = H(Y) - 0 = H(Y)$와 같이 $Y$의 엔트로피와 같아질 것이다
- 마지막 equation처럼 joint probability $P_{(x,y)}$와 두 marginal의 곱 $P_X P_Y$ 사이의 KL divergence를 측정하는 것으로도 설명 가능하다

### 5.2 Derivation

$$\begin{align} \notag I(X;Y) &= H(Y) - H(Y|X)
\\ \notag &= - H(Y|X) + H(Y)
\\ \notag &= - \sum_x p(x) H(Y|X=x) - \sum_y p(y) \log p(y)
\\ \notag &= \sum_x p(x) \big( \sum_y p(y|x) \log p(y|x) \big) - \sum_y \log p(y) \big( \sum_x p(x,y) \big)
\\ \notag &= \sum_x \sum_y p(x) p(y|x) \log p(y|x) - \sum_x \sum_y p(x,y) \log p(y)
\\ \notag &= \sum_x \sum_y p(x,y) \log \frac{p(x,y)}{p(x)} - \sum_x \sum_y p(x,y) \log p(y)
\\ \notag &= \sum_x \sum_y p(x,y) \log \frac{p(x,y)}{p(x) p(y)}
\\ \notag &= D_{KL} \big( P_{(X,Y)} \parallel P_X P_Y \big)
\end{align}$$

---

## References

- [Information Theory](https://en.wikipedia.org/wiki/Information_theory)
- [Entropy](https://en.wikipedia.org/wiki/Entropy_(information_theory))
- [Cross Entropy](https://en.wikipedia.org/wiki/Cross_entropy)
- [KL Divergence](https://en.wikipedia.org/wiki/Kullback%E2%80%93Leibler_divergence)
- [Conditional Entropy](https://en.wikipedia.org/wiki/Conditional_entropy)
- [Mutual Information](https://en.wikipedia.org/wiki/Mutual_information)
- [정보이론 기초](https://ratsgo.github.io/statistics/2017/09/22/information/)
- [Information Theory (하성주 박사님)](https://shurain.net/personal-perspective/information-theory/)
