---
title: "제발 되라 Grad-CAM"
excerpt: "제발..."

categories:
  - Blog
tags:
  - Blog
last_modified_at: 2019-04-13T08:06:00-05:00

---


# Grad-CAM: Visual explanations from Deep Networks via Gradient-based Localization

## Abstract

---

- CNN-based models 에서의 의사결정에 대한 Visual Explanations를 제공하는 기술을 제안함.
- Grad-CAM은 해당 Concept 예측에 중요한 영역을 표시하는 Localization Map을 만들기 위해 어떤 target concept의 Gradient 값을 이용함
- 이전 연구와 달리 수많은 CNN Model에 적용될 수 있음
    1. CNNs with fully-connected layers (e.g. VGG)
    2. CNNs used for structured outputs (e.g. captioning)
    3. CNNs used in tasks with multi-modal inputs (e.g. Visual Question Answering)
- 또한 high-resolution class-discriminative Visualization을 만들기 위해 Grad-CAM을 이미 존재하는 Fine-grained visualizations와 결합해 Guided Grad-CAM을 Image Classification, Imaged Captioning, VQA(Visual Question Answering)에 적용할 것임

## Introduction

---

- Zhou et al이 CAM(Class Activation Mapping) 을 제안함.
- CAM은 Fully-connected layer을 전혀 포함하지 않는 제한된 형태의 Image Classification CNN에서 사용되는 Discriminative한 영역을 식별함
- Complexity와 Performance는 trade-off(반비례..?) 관계에 있음

    → Grad-CAM은 일반화된 CAM → 훨씬 더 많은 CNN Model에 적용될 수 있음

- Good Visual Explanation의 조건
    - Class-discriminative (i.e. localize the category in the middle) =
    - High-resolution  (i.e. Capture Fine-grained detail) = 높은 해상도

    ![Grad-CAM%20Visual%20explanations%20from%20Deep%20Networks%20vi%20d9abbab5304e48248379a5bbb9ed70a8/Untitled.png](Grad-CAM%20Visual%20explanations%20from%20Deep%20Networks%20vi%20d9abbab5304e48248379a5bbb9ed70a8/Untitled.png)

    - (b), (h) → 아주 비슷한 결과를 보임 (Not Class-discriminative)
    - (c), (i) → (c)는 cat에 하이라이트, (i)는 dog에 하이라이트 (highly Class-discriminative)
    - (d), (j) →앞의 두 모델을 합쳐 Highly Class-discriminative & High-resolution한 이미지를 얻어냄.

→ 요약

1. Class-discriminative와 High-resolution 두 마리 토끼를 모두 잡는 Grad-CAM을 제안

    Localization과 Faithfulness로 평가할거임

2. 존재하는 Top-performing classification, captioning, VQA에 Grad-CAM을 적용
3. Image Classification에 적용된 ResNets를 시각화해볼거임
4. Human-studies 진행할거임

as

## Related Work (CAM만 설명할거임)

- Grad-CAM과 가장 밀접한 연관을 맺고 있는 모델 → CAM(Class Activation Mapping)
- CNN의 Fully-connected layers를 Convolution layers와 GAP(Global Average Pooling)으로 대체

    → Class-specific feature map을 얻을 수 있음

- 단점
    - Softmax layers에 바로 연결되는 feature map을 필요로 하기에 특정 구조를 가진 CNN에만 적용이 가능 → 특정 구조 : Conv feature map → Global Average Pooling → Softmax Layer

        → Image Classification과 같은 일반적인 task에서 좋지 않은 성능을 보임

        → Image Captioning이나 VQA와 같은 task에는 적용할 수 조차 없음

## About Grad-CAM

![Grad-CAM%20Visual%20explanations%20from%20Deep%20Networks%20vi%20d9abbab5304e48248379a5bbb9ed70a8/Untitled%201.png](Grad-CAM%20Visual%20explanations%20from%20Deep%20Networks%20vi%20d9abbab5304e48248379a5bbb9ed70a8/Untitled%201.png)

- $\alpha^c_k = (1/Z)\sum_i\sum_j(dy^c/dA^k_{ij})$

    $L^c_{Grad-CAM}=ReLU(\sum_k\alpha^c_kA^k)$

    - $y_c :$ score for class C, $A_{ij}^k :$Feature maps for convolution layer →$dy^c/dA^k_{ij} :$ Gradients via backprop, $(1/Z)\sum_i\sum_j :$ Global Average Pooling
    - $\alpha^c_k :$Partial Linearization of the deep network downstream from A

        → Neuron importance weight

        → Target class c를 위한 Feature map "k"의 중요도

    - ReLU를 쓴 이유 : Class에 긍정적인 영향을 미치는 Feature에만 관심이 있기 때문에
- CAM과 Grad-CAM의 간단한 차이

    ![Grad-CAM%20Visual%20explanations%20from%20Deep%20Networks%20vi%20d9abbab5304e48248379a5bbb9ed70a8/Untitled%202.png](Grad-CAM%20Visual%20explanations%20from%20Deep%20Networks%20vi%20d9abbab5304e48248379a5bbb9ed70a8/Untitled%202.png)

              <CAM VS Grad-CAM by [https://jsideas.net/grad_cam/](https://jsideas.net/grad_cam/)>

    - CAM의 $w^c_k$는 GAP으로 구조를 변경한 후 Fine-tuning을 통해 학습한 Softmax Layer의 Weight 횡벡터
    - Grad-CAM의 $\alpha^c_k$는 Softmax 함수의 Input 값의 Feature Map k에 대한 편미분값

        → Feature map k 이후에 GAP던, FC가 와도 관계없이 다시 Conv layer로 넘어오는 Gradient를 받아다가 GAP 방식으로 구한 값

        → 구조나 재학습을 하지 않아도 구할 수 있다

