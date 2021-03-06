---
title: "Explainability methods for GCNNs"
excerpt: "XAI"

categories:
  - xai
tags:
  - 
use_math: true
last_modified_at: 2021-08-01T23:13:00-05:00

---



## Abstract

---

- GCNN(Graph Convolutional Neural Networks)의 성장에 따라 이에 대한 설명 가능여부도 중요해짐.
- GCNNs를 위한 여러 Explainability Method에 대해 소개하고자 함
    - Contrastive gradient-bbased saliency maps(CG)
    - Class Activation Mapping(CAM)
    - Excitation Backpropagation(EB)

        +  Grad-CAM(Gradient-weighted CAM)

        +  c-EB(Contrastic EB) 

- 3 desirable Properties
    - Their Importance to classification measured by the impact of occlustions (중요성)
    - Their contrastivity with respect to different classes (다른 Class에 관한 대비)
    - Their sparseness on a graph (희소성)
- Evaluation metrics
    - Fidelity (충실도)
    - Contrastivity (대립성)
    - Spasity (희소성)

## Introduction

---

- Deep CNNs의 등장에 따라 Computer Vision 분야는 엄청난 발전을 하고 있다.
- 이와 동시에 CNN으로부터 나온 결정에 대한 Explainability, Interpretability가 가려지고 있었음.

    → CNN의 내부에 대한 연구가 활발하게 이루어지고 있다

- However, Deep CNNs는 grid structured data(좌표계)에 맞게 디자인되어 있다.
- 그렇다면 Graph-structured data는? → Convolution operation을 Graph로 확장하여 CNN을 일반화시킬수 있음.

    → Specific Contribution

    1. Adaptation of explainability methods for CNNs to GCNNs
    2. Demonstration of the explainability techniques on two graph classification problems: visual scene graphs & molecules
    3. Charateriztion of each method's trade-offs

## Methods

---

### Defined for signals on uniform square grid (for CNNs)

1. **CG (Contrastive gradient-based) saliency maps**
    - 모델의 Output을 input에 관해 쉽게 미분할 수 있음.
    - 결과값에 긍정적인 영향력을 가지고 있는 변수들만 유지됨 (Negative value는 버려짐)
    - 해석하기 쉽고 계산하기 편하지만 CAM, Grad-CAM과 같은 최근 방법보다는 성능이 안 좋음.
    - Noise를 많이 보임
- $L^c_{Gradient}= ||ReLU(dy^c/dx)||$

    → $x :$ Input , $y^c :$ Score for class c before the softmax layer

1. **CAM (Class Activation Mapping)**
    - 마지막 Convolution layer 전에 Important하고 Class-specific한 Feature들을 식별
    - 단점
        - Softmax layer (output layer) 바로 전에 GAP(Global Average Pooling)에 의한 Convolution layer이 필요

            → GAP : 각 Feature Map상의 노드값들의 평균을 뽑아냄

            → 결과값은 크기가 감소된 Feature map

        - Complex하고 Heterogeneous한 네트워크를 배제 (several Fully-connected layer)
    - $e_k = (1/Z)\sum_{i}\sum_{j}F_{k,i,j}$,    $y_c = \sum_{k}(w^c_ke_k)$

        → $F_k :$ $k^{th}$ feature map of the convolutional layer,  $e_k :$  GAP of $F_k$

        →  $w^c_k :$  Importance of feature k for predicting class c

    - $**L^c_{CAM}[i,j] = ReLU(\sum_{k}w^c_kF_{k,i,j})**$

![image 4](https://user-images.githubusercontent.com/43038405/127798660-b83cc610-5574-49f9-b412-240a433e7dcd.png)


2. Grad-CAM(Gradient-weighted CAM)
    - CAM Method에서 특정 layer이 Convolutional해야 하는 구조적 제한을 해결
    - $w^c_k$  에서 $\alpha^c_k$ 로 변경 (based on Back-propagated gradients)
    - $\alpha^c_k = (1/Z)\sum_i\sum_j(dy^c/dF_{k,i,j})$
    - $L^c_{Grad-CAM}[i,j] = ReLU(\sum_k\alpha^c_kF_{k,i,j})$
3. Excitation Backpropagation
    - Network를 통한 backward-pass에서의 Nonlinearities를 무시

        → 특정 Class를 예측하는데에 있어 네트워크에 반대하는 증거를 보존하는 Heat-map을 만듬

    - $P(a^{l-1}_j|a^l_i)=Z^{l-1}_iy^{l-1}_jW^{l-1}_{ji}$        ( if $W^{l-1}_{ji}>=0$ )
    - $Z^{l-1}_i = (\sum_jy_j^{l-1}W_{ji}^{l-1})^{-1}$

### Graph Convolutional Neural Networks

- Task : To classify individual graphs with potentially different number of nodes
- GAP layer로부터 나온 graph convolutional layers를 몇 개 이용할 것
- 모든 graph는 fixed size vector로 표현될 것
- GAP features는 Classifier에서 이용됨

### Designed for GCNNs

1. Gradient-based heat-maps over nodes n

    → $L^c_{Gradient}[n]= ||ReLU(dy^c/dX_n)||$

2. CAM heat-maps

    → $L^c_{CAM}[n] = ReLU(\sum_{k}w^c_kF_{k,n}^L(X,A))$

3. Grad-CAM heat-map

    → $L^c_{Grad-CAM}[l,n] = ReLU(\sum_k\alpha^{l,c}_kF_{k,n}^l(X,A))$

    → $\alpha^{l,c}_k = (1/N)\sum_{n=1}^{N}dy^c/dF^l_{k,n}$  (Class specific weights for class c)

4. Excitation Backpropagation heat map

    → Calculated via backward passes through the softmax classifier

    → $**L^c_{EB}[n] = 1/d_{in}\sum_{k=1}^{d_{in}}p(F^0_{k,n})**$

## Evaluation

---

1. Fidelity
    - 두드러진 특징의 폐색이 classification 정확도를 낮춤
    - 0.01보다 큰 saliency value로 모든 nodes를 차단했을 때 나오는 정확도에서의 차이
    - Contrastive Gradient method가 가장 높은 Fidelity 보임
2. Contrastivity
    - 다른 Classes 사이에서 class-specific features가 어떻게 다른지
    - Grad-CAM이 가장 높은 Contrastivity 보임
3. Sparsity
    - Localization of an explanation
    - c-EB가 가장 높은 Spasity 보임

→ Grad-CAM이 가장 잘 맞는다고 결론 내림 (Contrastivity가 가장 중요한 조건이라고 판단)

→ 각 방법이 가지고 있는 특징이 있으니 본인의 방법에 맞게 이용하는것이 중요

ex) large graph를 해석할때에는 c-EB가 가장 좋은 방법


