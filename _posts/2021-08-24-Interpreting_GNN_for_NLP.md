---
title: "Interpreting Graph Neural Network for NLP with differentiable Edge Masking"
excerpt: "XAI"

categories:
  - xai
tags:
  - xai
use_math: true
last_modified_at: 2021-08-24T15:21:00-05:00

---


# Interpreting Graph Neural Networks for NLP with Differentiable Edge Masking

## 서론

---

 최근 구조적인 Inductive Bias 개념을 NLP 모델에 적용하는 것이 인기가 많아지고 있다. 그런데 그래프의 어떤 부분이 예측에 기여하는지 해석에 대한 부분에는 아직 관심이 연구가 부족한 현황이다. 따라서 본 논문에서는 **GNN의 예측을 해석함에 있어서 불필요한 edge를 식별**하는 사후 방법에 대해 소개하고자 한다.

## 본론

---

본 논문에서 우리가 원하는 방법이 가져야 하는 목표는 **그래프의 어떤 Edge에 GNN 모델이 의존하고 그들이 어떤 Layer에서 사용되는지 식별하는 것**이다.

즉, 목표를 정리하면 3가지로 정리된다.

1. GNN 추리 패턴을 사용자에게 소개하는 가장 자연스러운 방법인 연관있는 path를 input graph에서 식별하기
2. 최근 GNN-based NLP 모델들에 적용가능하게 충분히 다루기 쉬워야함
3. 해당 모델이 어떻게 그 예측까지 갔는지에 대한 통찰력을 제공할 만큼 충분히 신뢰도가 높아야함.

## Erasure Search

---

- 모델 예측에 영향을 주지 않고 완전히 제거될 수 있는 최대한의 특징을 찾아 해석을 돕는 방법

    → 1,3번 만족하는데 2번 만족 x → NLP 모델에 적용하기 어려움

→ GraphMask의 목표 : Erasure Search와 같은 이익을 취하면서 1~3번 조건 만족시키는 것

## GraphMask 방법론 설명

---

 앞에서 얘기했듯이 이 방법의 목표는 모델 예측에 영향을 주지 않으면서 Layer k의 어떤 edge가 무시될 수 있는지 찾는거다.

Point ① GNN 모델에서의 문제 → d(v) >> n인 그래프에서 train된 GNN이 d(v)<<n에 적용되었을때 불안정할 수 있음, 많은 edge가 불필요할 수 있음 

→ 단순히 drop될 수 있는 edge를 찾는 것으로 불충분할 수 있기에 Binary choice $z_{u,v}^{(k)}$를 이용해 학습된 $b^{(k)}$로 대체되는 edge를 찾을 거임

Point ② Erasure Search에서의 문제 → 버려질 수 있는 모든 가능한 지원자들에 대해 검색하기에 다루기 쉽지 않음, 각각의 예시들에 대해 하나하나 검색이 이루어지기에 Hindsight bias의 위험이 있음

→ 앞에서 언급한 $z_{u,v}^{(k)}$를 다음과 같이 계산할거임

![Untitled](https://user-images.githubusercontent.com/43038405/130566155-b88201ed-8eae-47ad-81f3-3c0a6269997a.png)

- $z_{u,v}^{(k)}$: 각각의 Prediction에서 하나하나 계산 → 대응하는 GNN message(layer k에서의 노드 상태)를 계산할 때, Original Model에 이용 가능한 정보에만 의존
- $\pi$변수는 학습단계에서 보이지 않는 예시들에서의 예측을 설명하는데 사용 (Hoxy... Inductive Bias...?)
- g가 학습된 이후, original 모델을 실행해서 $h_u^{(k)}$,$h_v^{(k)}$,$m_{u,v}^{(k)}$를 얻음.

    ![Untitled 1](https://user-images.githubusercontent.com/43038405/130566154-29e154e0-2194-4329-b84f-be5a67f1b507.png)

- 첫번째 layer - $m_{u,v}$는 equation 3으로 계산

    ![Untitled 2](https://user-images.githubusercontent.com/43038405/130566151-4e148951-8be7-4103-879c-c934100ace7f.png)

    → **0과 1의 값을 가지는 $z_{u,v}^{(k)}$를 통해 Edge가 예측에 영향을 미치는지에 따라 사용할지를 선택**

- 이후 layer - Equation 2를 이용해 $h_v'^k$를 계산

    ![Untitled 3](https://user-images.githubusercontent.com/43038405/130566150-aa3e2104-d5e6-413a-8e40-70412577acb4.png)

## Parameter Estimation

---

- 목표
    - 정보를 가진 sub-graph들 $G_s$을 식별하는 것
    - $**f(G_s,X)$ ~ $f(G,X)$를 유지하는 Edge의 최소 수를 갖는 그래프를 찾는 것**
- 문제점
    - 일반적으로 $f$ 함수가 smooth function이라 $f(G_s,X)$ 와 $f(G,X)$ 가 비슷하다고 보장하는 것은 가능하지 않음
    - 그러므로 input 값에서의 최소한의 변화가 무조건 같은 결과를 만들어내지는 못한다
- 제안 (2가지 개념)
    - Divergence $D_*$ : 두 결과값이 얼마나 다른지 측정 → Original Model의 결과값 구조에 의존
    - Tolerance Level $\beta$ : 어떤 차이가 받아들여질 수 있는지 측정
- 실용적인 방안
    - 함수 $g$로 예측되는 영이 아닌 값들의 수를 최소화시키려면 $**L_0$ 'norm'을 최소화하는게 실용적**

        → **Objective function**을 정의

        ![Untitled 4](https://user-images.githubusercontent.com/43038405/130566149-24f008e7-dcb0-4049-b433-84c98c204a24.png)

        - $\lambda$ : Divergence와 Tolerance Level이 반영된 부분에 대해 얼마나 사용할지 결정
        - 문제
            - 이 함수는 미분불가능한 함수... → Gradient-based Optimization 사용 불가
                - $L_0$가 불연속, 거의 모든 곳에서 미분값 0
                - binary 값을 출력하려면 Step Function처럼 불연속적 활성화 필요
        - 해결 방안
            - **Objective를 기댓값으로 다룸**

                → 이전에 다루었던 여러 논문에서도 Objective function이 미분불가능해 기댓값을 이용했음

            - Score function으로 3가지 방법 중 선택
                - Estimation i.e. REINFORCE
                - biased straight-through estimators
                - sparse relaxation
    - 사용자의 질의에 관련된 문서를 검색하는 후보검색 단계 (candidate retrieval phase)
    - 검색된 문서 내에서 정답을 생성하는 정답추출 단계 (answer extraction phase)

## Question Answering

---

- 사용자의 질의에 대한 답변이 될 수 있는 정답을 문서 집합내에서 탐색하여 사용자에게 제시해주는 시스템 (Wiki)
- 주어진 질문 문장과 문서에서 그 질문에 가장 맞는 Entity를 문맥에서 찾는 것 (Paper)
- Edge Type (추측...)
    - MATCH(string match) → 해당 질문에 대한 답을 찾을 때, 여러 문서에서 공통적으로 등장하는 단어 및 어구.
    - DOC-BASED (document-level co-occurrence) → 해당 문서에서 관계를 찾지 못해 문서간 관계를 통한 연관성...?
    - COREF (coreference resolution) → 수식어구처럼 뭐 ex. 이런 종류는 어떤 특징을 갖고 있다~~ 아 그럼 이런 종류는 A겠네? 이런 느낌..?
    - COMPLEMENT (absence of any other edge) →보충설명인가...? 연관성이 없는 부분이라고 생각...
- 결과 (사실 이 부분은 제대로 이해하지 못했음, NLP는 어렵다... → 추후에 기회가 되면 제대로 다시 이해해보기)
    - 어떤 Layer에서 어떤 edge가 사용되는지 분석 결과

        ![Untitled 5](https://user-images.githubusercontent.com/43038405/130566146-09171231-20a8-4531-bf63-93dfbaa0a26a.png)

    - Layer가 거칠수록 질문에 대한 Edge 수가 극적으로 증가함

## Semantic Role Labelling

---

- 정의
    - 문장 성분의 의미역을 결정하는 것 → 문장의 각 성분이 다른 구조로 배열되어도 같은 의미일 수 있다 (Wiki)
    - 주어진 서술부의 argument를 식별하고 의미론적 역할을 부여하는 것 (Paper) - task
- 결과
    - 각 Edge Type별로 어떤 Edge가 남았는지 (불필요하지 않은지)

        ![Untitled 6](https://user-images.githubusercontent.com/43038405/130566138-c5dc6fa8-a724-4de3-94dd-ab2a238020fc.png)

        → 이 부분도 자세하게 다루지는 않겠음 → 추후에 자세하게 다루어보겠음...
