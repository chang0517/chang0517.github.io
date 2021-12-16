---
title: "On pixel-wise explanations for non-linear classifier decisions by layer-wise relevance"
excerpt: "XAI"

categories:
  - xai
tags:
  - xai
use_math: true
last_modified_at: 2021-12-16T15:15:00-05:00

---

# On pixel-wise explanations for non-linear classifier decisions by layer-wise relevance propagation

## Abstract

---

- 머신러닝과 딥러닝이 Image Classification 모델에서 아주 좋은 성능을 보이고 있지만 왜 성능이 좋은지, 왜 오류가 나는지 등의 Black Box와 같은 단점이 존재
- pixel-wise decomposition of nonlinear classifiers에 의한 Classification decisions을 이해하는데에 있어 해결책을 하나 제안하고자 한다
    
    → Kernel-based classifiers over Bag of Words features와 Mulilayered neural networks에 대한 예측에 하나하나의 Pixel이 어떤 기여하는지 시각화하는 Methodology
    
- 그 Pixel의 기여도는 Heatmap으로 시각화할 수 있음
- Evaluation Method
    
    →PASCAL VOC 209 이미지, 기하학적 형태를 포함하는 Synthetic image data, MNIST 데이터로 train된 Classifiers, 미리 훈련된 ImageNet 모델
    

## Introduction

---

- Interpretability가 부족한 이유 → Raw image pixel을 Feature representation으로 처리하는 다양한 Mapping에서의 비선형성, 마지막 classifier function
- 해당 Classification 결정의 이유를 입증할 수 없다는 것은 상당히 큰 단점이자 문제
    
    → Multilayer neural networks와 BoW(Bag of Words) 모델에서 있는 Classification과 Interpretability 사이의 차이를 없애는 것을 목표로 함
    
    - Bow (Bag of Words)란?
        
        [NLP - 5. Bag of Words (BOW)](https://bkshin.tistory.com/entry/NLP-5-Bag-of-Words-BOW)
        
        - 단어들의 순서를 고려하지 않고 출현 빈도에만 집중하는 텍스트 데이터의 표현기법
        - 

## Pixel-wise Decomposition as a General Concept

---

![xai 1](https://user-images.githubusercontent.com/43038405/146371050-528d090c-e40b-451f-a101-fd8e858d070f.png)

- Classification
    - 고양이가 고양인지 아닌지 식별하는 모델
    - 이미지에서 각 픽셀의 RGB값을 Input 값으로 받아 특징을 추출해내 Classifier 점수를 계산
- Pixel-wise Explanation
    - Classification과는 반대로 식별된 Class 점수에서 Back-propagation 과정을 거치면서 각 픽셀이 그 Classificaiton에 어떤 영향력을 줬는지 Relevance Score를 계산
        - $f(x) = \sum_{d=1}^VR_d$ → Relevance Score의 총합은 f(x)와 같다
    - 그 계산결과를 Image에 색깔로 표시
- Pixel-wise Explanation을 설명하는 2가지 방법
    - Layer-wise relevance
    - Taylor Decomposition

## Layer-wise Relevance

---

- layer마다 Relevance score를 계산하고 그 총합은 보존된다.
- $f(x)=...=\sum_{d\subseteq l+1}R_d^{l+1}=\sum_{d\subseteq l}R_d^{l}=...=\sum_{d\subseteq d}R_d^{1}$
    
    → f(x)는 마지막 Layer에서의 Relevance score라고 볼 수 있음
    
    → Back-propagation을 하면서 나오는 Relevance score의 합은 보존이 된다
    
- (x)가 Linear Classifier일 때 : $f(x) = b+\sum_d\alpha_d\oslash_d(x_d)$
    
    ![xai 2](https://user-images.githubusercontent.com/43038405/146371048-c029b1e9-dba3-4f9a-b7ef-0f1f7693ff15.png)
    
    - 해당 Layer에서 weight($\alpha_d$)와 Feature 점수($\oslash_d$)를 곱한 값에 따라 relevance score에 f(x) 값을 분배하는 형태
    - 특징
        - 중요도에 따라 relevance score이 높다.
        - 총합은 f(x)로 유지가 되겠죠?
    - Relevance score가 절댓값으로 계산이 되기에  f(x)의 부호가 relevance score의 부호로 유지가 됨
        
        → 수많은 Classification 문제에서 실제 해석과는 다름
        
        → relevance score을 재정의할거임
        
    - $R_d^{(1)}=b/V+\alpha_d\oslash_d(x_d)$ → 앞에서 정의한 score보다 훨씬 그럴듯한 해석
        
        → layer-wiser relevance propagation이 feature space mapping $\oslash_d$와 같은 비선형성도 어느정도 다룰 수 있다는것을 보여줌.
        
        → Feature Mapping에서 어떠한 정규적인 가정도 필요하지 않음. 즉, 불연속이고, LeBesgue measure로 측정할 수 없어도 가능함
        
    
    ![xai 3](https://user-images.githubusercontent.com/43038405/146371046-15bbe29c-49bd-437c-ad14-c9561a372597.png)
    
    - Relevance 보존
        - $R_7^{(3)} = R_4^{(2)}+R_5^{(2)}+R_6^{(2)}$
        - $R_4^{(2)}+R_5^{(2)}+R_6^{(2)} = R_1^{(1)}+R_2^{(1)}+R_3^{(1)}$
            
            → 위 () 첨자는 몇번째 Layer인지 / 아래 첨자는 몇번째 Node인지 나타냄
            
- Summary
    1. Total relevance는 각 Layer 별로 유지가 되도록 제한된다
    2. 어떤 Node로 들어가는 Relevance score는 그 노드에서 나오는 Relevance score와 같다.

## Taylor-type decomposition

---

![xai 4](https://user-images.githubusercontent.com/43038405/146371035-927587fe-e77c-476f-8f08-f6d0c1d19f40.png)

- Blue dot - Negatively / Green dot - Positively
- Left - Local gradient of the classification function
- Right - Taylor approximation relative to a root point on the decision boundary