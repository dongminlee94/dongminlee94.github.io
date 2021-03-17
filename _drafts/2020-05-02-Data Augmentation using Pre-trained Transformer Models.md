---
title: "Data Augmentation using Pre-trained Transformer Models"
excerpt: "Text data augmentation에 관한 논문"
toc: true
toc_sticky: true
categories:
  - Paper
tags:
  - Natural Language Processing
  - Data augmentation
---

## 0. Abstract

> *Language model based pre-trained models such as BERT have provided significant gains across different NLP tasks. In this paper, we study different types of pre-trained transformer based models such as auto-regressive models (GPT-2), auto-encoder models (BERT), and seq2seq models (BART) for conditional data augmentation. **We show that prepending the class labels to text sequences provides a simple yet effective way to condition the pre-trained models for data augmentation. On three classification benchmarks, pre-trained Seq2Seq model outperforms other models.** Further, we explore how different pretrained model based data augmentation differs in terms of data diversity, and how well such methods preserve the class-label information.*

## 1. Introduction

data augmentation은 training data의 size를 키우는 방법으로 model의 overfitting을 방지하고, robustness를 확보하는 방법 중 하나이며, 특히 low-data regime task에 유용함.  
natural language processing에서 word replacement를 이용하여 data augmentation을 아래와 같은 방법들이 존재함.

- WordNet과 같은 knowledge base를 이용하여, word의 synonym으로 치환하는 방법
  - word의 synonym이 적기 때문에, 위 방법으로 생성된 sentence가 pattern이 다양하지 못하다는 단점이 존재함.
- language model을 이용, word와 pardigmatic relation이 있는 word로 치환하는 방법
  - 생성된 sentence가 원 sentence의 class label과 상응하지 않는 문제가 종종 발생함. (label compatibility)
-  label compatibility와 관련된 문제를 해결하기위해, [Conditional BERT Contextual Augmentation](https://arxiv.org/abs/1812.06705)에서 제안한 conditional BERT (CBERT)와 같이 label condition을 줘서 augmentation을 하는 방법이 존재함.
  - CBERT에서 label condition을 주는 방법은 BERT의 segment embedding을 task-specific dataset의 label에 맞추어 label embedding으로 활용하기 때문에, segment embedding이 없는 다른 pre-trained language model에 해당 방법을 활용하기가 힘든 단점이 존재함.

본 논문에서는 다양한 종류의 pre-trained transformer에서 data augmentation을 하는 데 활용할 수 있는 unified approach를 제안함. 세 가지 유형의 pre-trained model, 세 가지 유형의 task, 하나의 scenario에 대해서 unified approach를 검토함.

- pre-trained models
  1. a pre-trained auto-regressive (AR) LM (e.g. GPT-2)
  2. a pre-trained auto-encoder (AE) LM (e.g. BERT)
  3. a pre-trained seq2seq model (e.g. BART)

- tasks
  1. sentiment classification
  2. intent classification
  3. question classification

- scenario
  1. a low-resource data scenario (only 1% of the exsiting labeled dataset)

검토 결과 pre-trained seq2seq model이 아래의 이유로 가장 좋은 performance를 보임.

> due to its ability to generate diverse data while retaining the label information.

본 논문의 contribution은 아래의 세 가지로 볼 수 있음.

  1. implementation of a seq2seq pre-trained model based data augmentation.
  2. experimental comparision of different conditional pre-trained model based data augmentation models.
  3. a unified data augmentation approach with practical guidelines for using different types of pre-trained models.

## 2. DA using Pre-trained Models

### 2.0 DA Problem formulation

여러 pre-trained language model (e.g. GPT-2, BERT, BART)이 text classification task의 성능을 높이기위해, 아래의 algorithm처럼 data augmentation으로 활용됨.

![alg_1](https://raw.githubusercontent.com/aisolab/aisolab.github.io/master/_posts/_Data%20Augmentation%20using%20Pre-trained%20Transformer%20Models/alg_1.png)

> *Given a training dataset $D_{train}=\{ x_i, y_i \}^{1}_{n}$ where $x_i=\{ w_j \}^{1}_{m}$ is a sequence of $m$ words, and $y_i$ is the associated label, and pre-trained model $G$, we want to generate a dataset of $D_{synthetic}$. Alogirthm 1 describes the data generation process. For all augmentation methods, we generate $s=1$ synthetic example for every example in $D_{train}$. Thus, the augmented data is same size as the size of the original data.*

### 2.1 Conditional DA using Pre-trained LM

conditional data augmentation을 위해서는 pre-trained model $G$가 task-specific dataset의 class label 정보를 반영하게하기위해서 fine-tuning 과정이 필요함. 본 논문에서는 모든 pre-trained model에 적용할 수 있는 unified approach로 아래의 두 가지를 제안함.

1. prepend: prepending label $y_i$ to each sequence $x_i$ in the training data without adding $y_i$ to model vocabulary.
   - 이 경우 model vocabulary에 label $y_i$ (e.g. Positive, Negative)를 추가하는 형태가 아니므로 subword로 label이 쪼개질 수 있음.
2. extend: prepending label $y_i$ to each sequence $x_i$ in the training data and adding $y_i$ to model vocabulary.
   - 이 경우 model vocabulary에 label $y_i$ (e.g. Positive, Negative)가 추가되고, random initialization이 되므로 fine-tuning이 1보다 오래걸림. (학습해야할 weight가 늘어나는 것)

#### 2.1.1 Fine-tuning and generation using AE LMs

- AE LM의 model로 BERT를 활용
- fine-tuning 시 기존의 masked language model을 그대로 활용

#### 2.1.2 Fine-tuning and generation using AR LMs

- AR LM의 model로 GPT-2를 활용
- fine-tuning을 위하여 training dataset을 다음과 같이 생성
  - $D_{train}=y_1SEPx_1EOSy_2...y_nSEPx_nEOS.$
  - $SEP$ token을 label과 sentence 사이에 넣고, sentence가 끝나면 $EOS$ token을 concatenate
- data augmentation 시, fin-tuned model $G$에 에 $y_iSEP$를 넣고 $EOS$ token이 나올 때까지 생성
  - label compatibility를 충족시키기위하여 $y_iSEPw_1...w_k$를 넣을 수 있음. ($w_1...w_k$는 $x_i$의 첫 k개의 words, GPT2 context) 

### 2.2 Conditional DA using Pre-trained Seq2Seq model

- pre-trained seq2seq model로 BART를 활용함. (T5를 사용하기에는 computation cost 때문에)

#### 2.2.1 Fine-tuning and generation using Seq2Seq BART

class label $y_i$을 sequence $x_i$에 prepend하고 아래의 두 가지 방법으로 masking하여 fine-tuning함 (masking되는 word의 ratio는 대략 20%). 이 때 fine-tuning task는 encoder에서 masked sequence를 받고, decoder에서 이를 원래 sequence로 reconstruction하는 것임.

- $\text{BART}_{word}$
  - replace a word $w_i$ with a mask token $\lt mask \gt$ 
- $\text{BART}_{span}$
  - replace a continuous chunk of $k$ words $wi,w_{i+1},...,w_{i+k}$ with a single mask token $\lt mask \gt$

hyper-parameter setting의 경우 각 task-specific dataset의 validation dataset에 의하여 best model로 결정됨.

### 2.3 Pre-trained Model Implementation

#### 2.3.1 BERT based models

비조 대교 실험을 위해서 huggingface의 transformer 패키지에서 bert-base-uncased를 활용, prepend 세팅인 지 expand 세팅인 지에 따라 학습시킨 epoch 수가 다름.

- prepend
  - 10 epochs
- expand
  - 150 epochs
  - embeddings for labels are randomly initialized.

#### 2.3.2 GPT2 model implementation

비조 대교 실험을 위해서 huggingface의 transformer 패키지에서 GPT2-Small을 활용

- seperate token으로 $SEP$
- end of sequence token으로 $\lt\mid endoftext\mid\gt$를 활용
- generation 시, [nucleus sampling](https://arxiv.org/abs/1904.09751)을 활용함.

#### 2.3.3 BART model implementation

비조 대교 실험을 위해서 fairseq toolkit에 구현된 BART large model을 활용함.

- BART model vocabulary에 있는 $\lt mask \gt$를 word (또는 token)을 masking하는 데 활용함.
- fine-tuning 시 encoder에 20% 정도의 word (또는 token)을 masking해서 넣고, decoder에서 이를 reconstruction하는 task를 수행함.
  > *Note that label $y_i$ is prepended to each sequence $x_i$, and decoder also produces the $y_i$ like any other token in $x_i$* 
- label smoothing을 활용
- generation 시 beam search (size=5)를 활용

## 3. Experimental Setup

### 3.1 Baseline Approahces for DA

unified approach와의 비교를 위한 baseline으로 아래의 두 가지 방법론을 활용함.

1. [EDA](https://arxiv.org/abs/1901.11196)
2. [CBERT](https://arxiv.org/abs/1812.06705)

### 3.2 Data Sets

세 가지의 text classification dataset을 활용함. 또한 사용한 pre-trained model (BERT, GPT2, BART)는 모두 다른 byte pair encoding을 활용하고 있으므로, prepend의 경우, 같은 dataset의 같은 label이라도 subword token으로 split 되는 방식이 다름. 각 dataset의 label은 아래와 같음.

![table_1](https://raw.githubusercontent.com/aisolab/aisolab.github.io/master/_posts/_Data%20Augmentation%20using%20Pre-trained%20Transformer%20Models/table_1.png)

#### 3.2.1 Low-resourced data scenario

low-resourced data scenario를 위하여, train dataset에 10%를 sampling하고 class label 당 5개의 example을 dev dataset에서 sampling함.

![table_2](https://raw.githubusercontent.com/aisolab/aisolab.github.io/master/_posts/_Data%20Augmentation%20using%20Pre-trained%20Transformer%20Models/table_2.png)

### 3.3 Evaluation

아래의 두 가지 evaluation을 수행함.

1. extrinsic evaluatio
   > *we added the generated examples into low-data regime training data for each task and evaluated the performance on the test set discussed in Section 3.2.*

2. intrinsic evaluation
   - semantic fidelity: generated text가 얼마나 원래 text의 의미와 class label 정보를 보존하는 지 평가
   - text diversity: generated text가 얼마나 diverse한 지 type token ratio를 이용하여 평가
     > *Type token ratio is calculated by dividing the number of unique $n$-grams by the number of all $n$-grams in the generated text.*  

#### 3.3.1 Classifiers for intrinsic evaluation

intrinsic evaluation을 위해서 각 task-specific dataset에 대해서 bert-base-uncased를 아래와 같이 training함.

- task-specific dataset의 train과 test를 전부 합쳐서 training에 활용함.
- dev dataset으로 best hyper-parameter의 model을 선정
- best model로 generated text를 inference

![table_3](https://raw.githubusercontent.com/aisolab/aisolab.github.io/master/_posts/_Data%20Augmentation%20using%20Pre-trained%20Transformer%20Models/table_3.png)

## 4. Results and Discussion

### 4.1 Generation by Conditioning on Labels

extrinsic evaluation 결과 아래와 같음.
> *Since the labels in the corpora are well-associated with the mearning of the class (e.g. SearchCreative-Work), prepending tokens allows the model to leverage label information for conditional word replacement.*

![table_4](https://raw.githubusercontent.com/aisolab/aisolab.github.io/master/_posts/_Data%20Augmentation%20using%20Pre-trained%20Transformer%20Models/table_4.png)

### 4.2 Pre-trained Model Comparision

table 4를 통해서 비교해보면, BART 기반의 augmentation 방법론이 다른 방법론 보다 효과적인 걸 알 수 있음. 또한 GPT2의 경우 context를 줘서 augmentation하는 방법론이 효과적임을 알 수 있음.

#### 4.2.0 Generated Data Fidelity

![table_5](https://raw.githubusercontent.com/aisolab/aisolab.github.io/master/_posts/_Data%20Augmentation%20using%20Pre-trained%20Transformer%20Models/table_5.png)

#### 4.2.1 Generated Data Diversity

![table_6](https://raw.githubusercontent.com/aisolab/aisolab.github.io/master/_posts/_Data%20Augmentation%20using%20Pre-trained%20Transformer%20Models/table_6.png)

### 4.3 Guidelines For Using Different Types of Pre-trained Models For DA

AE model은 label 정보를 잘 보존할 수 있으나, 비슷한 길이의 sequence를 생성하는 것에 제약되어 있고, AR model은 AE model에 비해서 길이의 제약도 없고 생성된 sequence가 더 diverse하지만 label 정보를 보존하기가 힘듦. 반면에 Seq2Seq model의 경우 AE와 AR 사이에 있는 경우라고 볼 수 있음.
> *Seq2Seq models lie between AE and AR by providing a good balance between dicersity and semantic fidelity. Further, in Seq2Seq models, dicersity of the generated data can be controlled by varying the length of span masking.*

## 5. Conclusion And Future Work
> *We show that **AE, AR, and Seq2Seq pre-trained models can be conditioned on labels by prepending label information and provide an effective way to augment training data.** These DA methods can be easily combined with other advances in text content manipulation such as co-training the data generator and classifier. We hope that unifying different DA methods would inspire new approahces for universal NLP data augmentaiton.*
