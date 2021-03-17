---
title: "DialoGPT: Large-Scale Generative Pre-training for Conversational Response Generation"
excerpt: "pre-trained language model을 conversational response generation에 활용하는 방법에 관한 논문"
toc: true
toc_sticky: true
categories:
  - Paper
tags:
  - Conversation
  - Dialogue
---

## 0. Abstract

> ***We present a large, tunable neural conversational response generation model, DialoGPT (dialogue generative pre-trained transformer).*** *Trained on 147M conversation-like exchanges extracted from Reddit comment chains over a period spanning from 2005 through 2017, DialoGPT extends the Hugging Face PyTorch transformer to attain a performance close to human both in terms of automatic and human evaluation in single-turn dialogue settings. We show that conversational systems that leverage DialoGPT generate more relevant, contentful and context-consistent responses than strong baseline systems. The pre-trained model and training pipeline are publicly released to facilitate research into neural response generation and the development of more intelligent open-domain dialogue system.*

## 1. Introduction

본 논문에서는 기존의 GPT-2를 확장하여 conversational neural response generation을 다루는 DialoGPT를 제안한다.

> *We introduce DialoGPT, a tunable gigaword-scale neural network model for generation of conversational response, trained on Reddit data*

neural response generation은 text generation의 하위분야 중 하나로 training dataset에 있는 instance와 비슷한 text를 generation을 하는 것이 아니라 natural-looking text를 generation하는 task이다. 기존에 연구된 open-domain neural response generation 방법론들은 아래와 같은 문제점이 있다.

- content or style inconsistency
- lack of long-term contextual information
- blandness 

상기한 위의 문제점들은 information content를 잘 담을 수 있는 architecture (e.g. GPT-2)를 활용하면 경감시킬 수 있으며, 본 논문에서 제안하는 DialoGPT는 GPT-2의 architecture를 확장한 전형적인 auto-regressive language model이지만 학습에 활용하는 training dataset의 형태가 다르다.

> *DialoGPT is trained on large-scale dialogue pairs/sessions extracted from Reddit discussion chains.*

위와 같은 training dataset을 활용하면, 학습된 DialoGPT는 아래와 같을 것이다.

> *Our assumption is that this should enable DialoGPT to capture the joint distribution of $P(\text{Target, Source})$ in conversational flow with finer granularity.*

Reddit dataset에 pre-training한 DialoGPT를 다른 dataset들에 fine-tuning해서 결과는 아래와 같았다.

> *We have evaluated the pre-trained model on a public benchmark dataset (DSTC-7), and a new 6k multi-reference test dataset extracted from Reddit posings. DialoGPT achieves state-of-the-art results in both automatic and human evaluation, lifting performance to near-human response quality.*

## 2. Dataset

DialoGPT를 pre-training 하는 데 활용한 Reddit dataset은 2005년부터 2017년까지의 comment를 수집, Reddit의 아래와 같은 특징을 이용해 training instance를 구성함.

> *Reddit discussions can be naturally expanded as tree-structured reply chains, since a thread replying to one thread forms the root node of subsequent threads. We extract each path from the root node to the leaft node as a tining instance containing multiple turns of dialogue.*

아래와 같은 rule로 training instance를 filtering하여 제거함.

1. source, target에 URL이 있는 경우 해당 instance 제외
2. target에 word가 세 번 이상 반복되는 instance 제외
3. target (respons)에 top-50 most frequent english에 속하는 word가 아예 등장하지않는 instance 제외
4. target에 "[", "]"이 들어가는 instance 제외 
5. source, target을 합쳐서 200 word를 넘어가는 instance 제외
6. target에 offensive language가 있는 instance 제외
7. offensive한 content를 담고있는 instance 제외
8. bland한 instance를 제외

위와 같은 rule들을 적용하여 만든 Reddit dataset은 아래와 같음.

> *After filtering, the dataset comprises 147,116,725 dialogue instances, in total 1.8 billion words.*

## 3. Method

### 3.1 Model Architecture

GPT-2의 architetcure를 그대로 따르되, multi-turn dialogue session을 하나의 long text로 보고, generation task를 language modeling의 문제로 학습함.

- 하나의 dialogue session의 모든 dialogue turn을 concatenate하고, 마지막에 end-of-text를 가리키는 token을 concatenate ($x_1,x_2,...,x_N$, $N$의 길이를 가짐.)

- dialogue session이 single-turn일 경우, 다음과 같이 modeling 되는 것이라 볼 수 있음.
  
$$ P(T \mid S)=\prod_{n=m+1}^{N}p(x_n \mid x_1,...,x_{n-1}) $$

$$ S=x_1,...,x_m,T=x_{m+1},...,x_N $$

- dialogue session이 mult-turn일 경우 ($T_1,...T_K$), 다음과 같이 modeling 되는 것이라 볼 수 있음.

$$ p(T_K,...,T_2 \mid T_1)=p(T_2 \mid T_1) \cdot p(T_3 \mid T_2,T_1) \cdot ... \cdot p(T_K \mid T_{K-1},...,T_1) $$

### 3.2 Mutual Information Maximization

generated text가 bland하고 uninformative한 것을 방지하기위해, maximum mutual information (MMI) scoring function을 활용함.

- MMI scoring function으로 pre-trained backward model (pre-trained language model을 의미함)을 활용, $p(S \mid T)$를 계산하여, 이 값이 높은 $T$를 generated text로 활용

  > *We first generate a set of hypotheses using top-K sampling. Then we use the probability of $P(S \mid H)$ to rerank all hypotheses.*

- bland한 hypothesis일 경우, $P(S \mid H)$값이 상대적으로 낮음.
  - bland한 hypothesis는 많은 source가 가능한므로 특정 source에 대해서 $P(S \mid H)$가 높은 경우가 존재하지않을 것임.

## 4. Result

### 4.1 Experimental Details

세 종류의 DialoGPT를 Reddit dataset에 대하여 pre-training, vocabulary에 존재하는 token의 개수는 50,257개이며 NVLink를 이용 16 Nvidia V100으로 pre-training함.

![table_1](https://raw.githubusercontent.com/aisolab/aisolab.github.io/9-DialoGPT/_posts/_DialoGPT/table_1.png)

#### 4.1.0 Speeding up training

- training dataset을 lazy-loading style로 활용
- dynamic batch strategy를 활용

### 4.2 DSTC-7 Dialogue Generation Challenge

DSTC (Dialog System Technology Challenges) 7 track은 end-to-end conversational modelling task로 사전에 정의된 goal이 없는 task.

> *Instead, it targets human-like interactions where the underlying goal is often ill-defined or unknown in advance, of the kind seen in work and other productive environments (e.g. brainstorming meetings) where people share information.*

DialoGPT가 generate한 text를 automatic evalaution (e.g. BLEU, METEOR, NIST )으로 평가한 결과는 아래의 table과 같음.

![table_2](https://raw.githubusercontent.com/aisolab/aisolab.github.io/9-DialoGPT/_posts/_DialoGPT/table_2.png)

DialoGPT의 경우 automatic evalaution의 결과가 사람의 generate한 text보다 높은 점수를 기록함. 

> *This does not mean that the generation is more "realistic" than human, but is probably attributable to the one-to-many nature of conversation.*

![fig_1](https://raw.githubusercontent.com/aisolab/aisolab.github.io/9-DialoGPT/_posts/_DialoGPT/fig_1.png)

### 4.3 A New Reddit Multi-reference Dataset

DialoGPT를 Reddit postings에서 가져온 6k multi-reference test dataset에 대해 아래의 두 가지 세팅으로 평가함. (table 3)

- training from scratch
- fine-tuning using GPT-2 as the pre-trained model

### 4.4 Re-ranking The Response Using MMI

DialoGPT 345M을 기준으로 MMI를 사용 (pre-trained backward model로도 역시 DialoGPT 345M)하는 결과와 사용하지 않은 결과를 비교해보면, MMI를 쓰는 경우가 BLEU를 제외하고 다른 지표에서 수치가 상승, MMI를 사용하는 것이 좀 더 diverse한 response를 얻을 수 있음을 알 수 있음.

![table_3](https://raw.githubusercontent.com/aisolab/aisolab.github.io/9-DialoGPT/_posts/_DialoGPT/table_3.png)

### 4.5 Generation Examples

![table_4](https://raw.githubusercontent.com/aisolab/aisolab.github.io/9-DialoGPT/_posts/_DialoGPT/table_4.png)

![table_5](https://raw.githubusercontent.com/aisolab/aisolab.github.io/9-DialoGPT/_posts/_DialoGPT/table_5.png)

### 4.6 Human Evaluation

#### 4.6.0 Human evaluations

6k multi-reference test dataset에서 2000개를 뽑아 각 시스템 별 아웃풋에 대한 평가를 crowd-sourcing을 통해 진행함. relevance, informativeness, human-like라는 관점에서 리커트척도로 평가됨. DialogGPT MMI가 Human response보다 더 높은 선택을 받은 이유는 아래로 추측됨.

> *probably because of many of the true human responses are erratic or idiosyncratic, or are tied to internet memes that happend to be unfamiliar to the judges.*

![table_7](https://raw.githubusercontent.com/aisolab/aisolab.github.io/9-DialoGPT/_posts/_DialoGPT/table_7.png)

## 5. Related Work

중략 (논문 참고)

## 6. Limitations and risks

> *DialoGPT retains the potential to generate output that may trigger offense. Output may reflect gender and other historical biases implicit in the data.*

## 7. Conclusion

> ***We have released an open-domain pre-trained model, DialoGPT, trained on massive real-world Reddit dataset.*** *The package consists of a distributed training pipeline and several pre-trained models that can be fine-tuned to obtain a conversation model on a moderately-sized cutomized dataset in few hours. DialoGPT is fully open-sourced and easy to deploy, allowing users to extend the pre-trained conversational system to bootstrap training using various datasets. It serves as a building block to novel applications and methodologies. Detection and control of toxic output will be a major focus of future investigation. We will investigate leveraging reinforcement learning to further improve the relevance of the generated responses and prevent the model from generating eregious responses.*