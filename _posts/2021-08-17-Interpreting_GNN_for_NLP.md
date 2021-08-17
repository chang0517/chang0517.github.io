---
title: "Interpreting Graph Neural Network for NLP with differentiable Edge Masking"
excerpt: "XAI"

categories:
  - xai
tags:
  - xai
use_math: true
last_modified_at: 2021-08-17T15:21:00-05:00

---



# Interpreting Graph Neural Networks for NLP with Differentiable Edge Masking

## Abstract

---

GNN이 NLP 모델에 구조적인 Inductive bias를 통합시키는데 인기있는 접근이 되어왔음. 하지만 그것의 해석이나 그래프의 어떤 부분이 예측에 영향을 주는지의 부분에는 많은 노력이 있지 않았음. 따라서 본 논문에서는 GNN의 예측을 해석하기 위해 불필요한 Edge를 식별하는 Post-hoc 방법을 제안함.

## Introduction

---

- 목표
    - 그래프의 어떤 edge에 GNN이 의존하고 그들이 어떤 layer에 사용되는지 식별하는 것
    1. GNN 추리 패턴을 사용자에게 소개하는 가장 자연스러운 방법인 연관있는 path를 input graph에서 식별하기
    2. 최근 GNN-based NLP 모델들에 적용가능하게 충분히 다루기 쉬워야함
    3. 해당 모델이 어떻게 그 예측까지 갔는지에 대한 통찰력을 제공할 만큼 충분히 신뢰도가 높아야함

    ![int    1](https://user-images.githubusercontent.com/43038405/129679127-7e4e9180-561c-493a-9fc1-0547380bf7b8.png)

    - 해석에 가장 간단한 방법 → Erasure Search
        - 모델 예측에 영향을 주지 않고 완전히 제거될 수 있는 최대한의 특징들을 찾는 것
        - discrete attribution은 path들 사이의 연관도 비교를 간단하게

            →continuous attribution은 중요한 path들을 추출하고 시각화하는데 직접적 연관 x

        - 목표 1번이랑 3번은 만족하지만 2번 만족 x

    - GraphMask의 목표 → erasure search의 크기에서의 똑같은 이익을 취하면서 위의 목표를 이루는것
    - $G_s^{k}$ 밖의 어떤 edge도 모델 예측에 영향을 주지 않도록 k개의 각 layer에서 subgraph $G_s^{k}$를 return
    - erasure search에서 optimisation은 각각의 example에서 각각 이루어짐 → Overfitting 우려

        →불필요하지 않은 edge들도 같이 prune될 수 있음 → hindsight bias라고 함

        → 판독 병목과 비슷한 절차를 통해 training 데이터셋을 배우는 변수를 상환함으로써 해결하고자 함

- Contributions
    - GNN을 구성요소로 갖는 어떠한 end-to-end neural model에도 적용가능하도록 GNN을 위한 해석 방법을 제공
    - 인공적인 데이터를 활용해 가장 비슷한 현존하는 방법의 단점들을 설명하고 논문에서 제시하는 방법론이 그 단점들을 어떻게 다루고 신뢰도를 높이는지 보여주고자 함
    - Semantic role labelling & Multi-hop question answering, 의 두 가지 NLP task에서 GNN model을 분석하기 위해 GraphMask를 사용할 거임

## Method

---

### 1. Graph Neural Networks

- 모든 layer k에서 이전 layer의 노드들의 representation를 기초로 하여 각각의 노드에서  node representation $h_u^{k}$를 계산
- k>0에서 GNN은 Message function M과 aggregation function A를 통해 정의

![int 2_1](https://user-images.githubusercontent.com/43038405/129679491-0b9c454e-ff19-43b4-8f1b-31ec11662965.png)

    → $r_{u,v}$: node u와 v사이의 연관관계, $N(v)$: v의 이웃 노드들

### 2. GraphMask

- 목표
    - 모델 예측에 영향을 주지 않으면서 layer k의 어떤 edges(u,v)가 무시될 수 있는지 찾는것
- 문제 (1) - GNN에서의 문제
    - $d(v)$>>n인 그래프에서 train된 GNN이 $d(v)$<<n에 적용되었을 때 불안정할 수 있음.
    - 많은 edge가 불필요할 수 있음

        → binary choice인 $z_{u,v}^{(k)}$를 통해 학습된 baseline  $b^{(k)}$로 대체된 edge들을 검색할거임

- 문제 (2) - Erasure search에서의 문제
    - 버려질 수 있는 모든 가능한 지원자들에 대해 검색하기에 다루기 쉽지 않음
    - 각각의 예시들에 대해 하나하나 검색이 이루어지기 때문에 hindsight bias의 위험 있음

        → hindsight bias : 불필요하지 않은 edge들도 같이 prune 되는 위험

        → $z_{u,v}^{(k)}$를 Data Point들로 모든 task에 대해 학습된 간단한 함수로 계산

        - About $z_{u,v}^{(k)}$, parameter $\pi$

            ![int 2_2](https://user-images.githubusercontent.com/43038405/129679599-44703c7b-e992-467d-a463-63e93884b1e4.png)
            - 특징
                - $\pi$ : 함수 g의 변수 / single-layer neural network로 보충
                - Gate value $z_{u,v}^{(k)}$는 각각의 prediction에서 하나한 계산 됨, but $\pi$ 변수는 여러 Datapoint를 이용해 학습되고 학습 단계에서 보이지않는 예시들에서의 예측을 설명하는데 사용됨
                - 각각의 $z_{u,v}^{(k)}$는 대응하는 GNN message (layer k에서의 노드 상태)를 계산할때 original model에 이용 가능한 정보에만 의존

                    → $z_{u,v}^{(k)}$와 $\pi$ 두 변수의 이런 전략을 amortisation이라고 함 / hindsight bias를 예방 

                    → 이 전략 대신 $\pi$ 를 gate 간에 어떠한 변수 공유 없이 각각의 gate에 독립적으로 선택

                    → 이 경우, 최적화는 분석된 예시에 직접 적용됨

                    → 그 전략을 GraphMask의 non-amortized 버전이라고 말할거임 

- g가 학습된 후, $h_u^{(k)}$,$h_v^{(k)}$,$m_{u,v}^{(k)}$를 얻기 위해 original model 실행
- 모든 layer의 모든 edge에서 gate를 계산
- Figure 1에서 보여줬던 모델의 sparsified version을 실행

    ![int 12](https://user-images.githubusercontent.com/43038405/129679704-f9d81f09-549f-4992-a2fc-f452fdbaf3c5.png)
- 첫번째 layer - original model의 message는 equation 3로 계산
- 이후 layer - mask된 message들을 합쳐 Equation 2를 이용해 $h_v'^k$를 계산 → 다음 mask된 message 구할때 사용

### 3. Parameter Estimation

- 목표
    - 정보를 가진 sub-graph들 $G_s$을 식별하는 것
    - $f(G_s,X)$ ~ $f(G,X)$를 유지하는 Edge의 최소 수를 갖는 그래프를 찾는 것
- 문제점
    - 일반적으로 $f$ 함수가 smooth function이라 $f(G_s,X)$ 와 $f(G,X)$ 가 비슷하다고 보장하는 것은 가능하지 않음
    - 그러므로 input 값에서의 최소한의 변화가 무조건 같은 결과를 만들어내지는 못한다
- 제안 (2가지 개념)
    - Divergence $D_*$ : 두 결과값이 얼마나 다른지 측정 → Original Model의 결과값 구조에 의존
    - Tolerance Level $\beta$ : 어떤 차이가 받아들여질 수 있는지 측정
- 실용적인 방안
    - 함수 $g$로 예측되는 영이 아닌 값들의 수를 최소화시키려면 $**L_0$ 'norm'을 최소화하는게 실용적**

        → Objective function을 정의

        ![int 1231](https://user-images.githubusercontent.com/43038405/129679776-595f16ec-9567-466b-8a53-1fded6b293b6.png)


        - 문제
            - 이 함수는 미분불가능한 함수... → Gradient-based Optimization 사용 불가
                - $L_0$가 불연속, 거의 모든 곳에서 미분값 0
                - binary 값을 출력하려면 Step Function처럼 불연속적 활성화 필요
        - 해결 방안
            - Objective를 기댓값으로 다룸
            - Score function으로 3가지 방법 중 선택
                - Estimation i.e. REINFORCE
                - biased straight-through estimators
                - sparse relaxation
                - 

## Synthetic Experiment

---

- 목표
    - 완전히 진실여부가 알려져있는 곳에 Graph Mask의 적용
    - Real-world에 적용되기 전에 Faithfulness에 대한 평가의 필요성
    - x로 할당된 edge 수가 y로 할당된 edge 수보다 많은지 예측
- 실행
    - 6개~12개의 Leaf를 가진 예시들을 랜덤하게 생성, 간단한 1-layer R-GCN 적용

        → 모든 예시를 완벽하게 Classify / 예측에 영향을 주는 Edge들을 모두 세야함.

    → Faithfulness를 위한 기준을 세움

    → x>y인 type x와 y의 모든 edge들은 유지되어야함 / 다른건 다 버려져야 함

- GraphMask VS Erasure search VS Integrated Gradients ... (비교)

    ![int a](https://user-images.githubusercontent.com/43038405/129680056-836f2512-6926-48f0-b2b3-f6edcae2eb67.png)

    - task가 x>y인지를 예측하는 것이기에 모델은 type x만 유지된 상태로 완벽한 점수를 보임
    - x≤y인 곳에서는 모든 edge가 drop된 상태로 완벽한 점수를 보임

        → Amortization이 overfitting 막음

## Question Answering

---

- 목표
    - GraphMask를 실제 모델에 적용하여 분석해볼거임
- 조건
    - 어떠한 Human Gold Standard도 모델설정에 사용되지 않음
    - multi-hop QA를 위한 GNN-based model을 선택
    - 불필요한 Edge의 선택이 Random Seed의 결과가 아니라는것을 보장하기 위해 Fleiss' Kappa score 적용 (안정성 증명)
    - 
- Task
    - 주어진 질문 문장과 문서에서 그 질문에 가장 맞는 Entity를 문맥에서 찾는 것

+ GNN 그래프에서 Nodes → 질문이나 글에서 Entity / Edge 종류(4) : MATCH, DOC-BASED, COREF...

- 어떤 edge type이 모델의 3개의 layer에서 사용되었는지 확인 (Table 2)

    ![int b](https://user-images.githubusercontent.com/43038405/129680046-c617e4b6-d99a-40f1-81a8-b0fc6dc9c86a.png)
    → DeCao 논문에서는 COREF edge가 모델에 좋은 이득을 준다고 했는데 우리 결과론 아님

    → COREF는 MATCH와 2.3%만 중복됨.

    →COMPLEMENT edge가 Bottom layer의 불필요하지 않은 edge의 대부분을 차지 / 이후 layer에서는 항상 불필요

    → 

## Semantic Role Labeling

---

- Task
    - 주어진 서술부의 argument를 식별하고 의미론적 역할을 부여하는것
    - 문장 성분의 의미역을 결정하는 것 → 문장의 각 성분이 다른 구조로 배열되어도 같은 의미일 수 있다.(wiki)

        ![int c](https://user-images.githubusercontent.com/43038405/129680042-b23aec53-e145-4d3c-9d5a-8d638d914361.png)
- Step 1
    - 이 GNN은 모든 message에서 스칼라, 곡선형 gates를 사용
    - 해석을 위한 순진한 방법은 그들의 value값을 검사하는것 → 각각의 message의 중요도를 필연적으로 반영 x

        → 오히려 모델에서 구성요소가 되도록 scaling을 제공

    → 이 gate들이 message들의 중요도보다는 scaling으로 작용한다는 것을 알게됨

- Step 2
    - GNN이 어떤 type에 의존하는지 조사
    - 행동은 형용사, 동사적 표현에 따라 강하게 달랐음 (NMOD는 명사를, SBJ,OBJ는 동사적 술부에서 큰 영향을 가짐)

    ## Conclusion

    ---

    - 어떠한 GNN model에도 적용가능한 사후 해석방법인 GraphMask를 제시
    - 모든 message에 대한 end-to-end 미분가능한 hard gate들을 학습하고, training data를 amortize하면서 GraphMask는 현대 GNN model에 맞출 수있는, 어떻게 edge와 path가 예측에 영향을 미치는지 식별하는데 사용될 수 있는, 학습된 모델에 충실함.
    - 2가지 NLP model에 적용 (SRL, QA)
    - 어떤 edge type에 이 모델이 의존하는지, 예측할때 path를 어떻게 고용하는지 알아냄

