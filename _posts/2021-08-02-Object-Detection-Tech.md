---
title: "Object Detection 기술동향"
excerpt: "computer_vision"

categories:
  - computer_vision
tags:
  - 
use_math: true
last_modified_at: 2021-08-02T16:45:00-05:00

---


# Object Detection 기술 (발전 동향)

## 먼저 읽어보면 좋아요

---

[[Object Detection] 1. Object Detection 논문 흐름 및 리뷰](https://nuggy875.tistory.com/20)

[(논문리뷰) R-CNN 설명 및 정리](https://ganghee-lee.tistory.com/35)

## Object Detection (객체 인식) 이란?

- 컴퓨터 비전과 이미지 처리와 관련된 컴퓨터 기술로서, 디지털 이미지와 비디오로 특정한 계열의 시맨틱 객체 인스턴스(ex. 인간, 건물, 자동차)를 감지하는 일을 다룬다. (from. Wikipedia)
- 여러 물체에 대해 어떤 물체인지 분류하는 **Classification 문제**와 그 물체가 어디 있는지 박스(Bounding Box)를 통해 위치 정보를 나타내는 **Localization 문제**를 둘 다 해내야 하는 분야
- **Object Detection** = 여러가지 물체에 대한 Classification + 물체의 위치정보를 파악하는 Localization

![Image 6](https://user-images.githubusercontent.com/43038405/127822510-7dce38a8-0f55-44d0-9be0-3486ddd6ccfb.png)


- Deep Learning을 이용한 Object Detection은 크게 1-stage Detector와 2-stage Detector로 나눌 수 있다.
    - **1-stage Detector** : Classification과 Localization을 동시에 수행 (ex.YOLO, SSD 계열)

        → RoI 영역을 먼저 추출하지 않고 전체 이미지에서 CNN으로 Classification, Localization 수행

    - **2-stage Detector** : 두 개를 순차적으로 수행 (ex. R-CNN 계열)
        1. Selective Search, RPN(Region Proposal Network)와 같은 알고리즘 미 네트워크를 통해 Object가 있을만한 영역을 뽑아냄 =Roi(Region of Interest)
            - Selective Search란?

            - RPN(Region Proposal Network)란?
        2. 각 영역들을 CNN을 통해 Classification, Localization을 수행

    → 속도 : 1-stage > 2-stage

    → 정확도: 1-stage < 2-stage

    ![Image 7](https://user-images.githubusercontent.com/43038405/127822520-d7ddaa92-ad9c-4b44-8772-23b368e7e05c.png)
                                                            <1-stage & 2-stage Detector기술 흐름 동향>   

## Object Detection 기술발전 동향

→ 자 이제 하나하나 짚어봅시다!

- 2012년 AlexNet이 세상에 공개되면서 CNN이 이미지 분류(Classification)에서 표준처럼 다루어짐.
- Classification은 한 개의 객체(Object)가 그려져 있는 이미지가 있을 때, 이 객체가 무엇인지 알아내는 문제
- 객체 인식(Object Detection) - 이미지 내 관심 있는 객체의 위치에 Bounding Box를 그리고, 다수의 Bounding Box를 다양한 Object 종류에 대해 찾아야 함 → Classification보다 훠~얼씬 복잡

    ![Image 7](https://user-images.githubusercontent.com/43038405/127822520-d7ddaa92-ad9c-4b44-8772-23b368e7e05c.png)

                                                              <Classification vs Object Detection>

- 2014년에 R-CNN의 등장으로 CNN을 Object Detection 분야에 최초 적용

    [R-CNN](https://chang0517.github.io/computer_vision/R-CNN/)

    → 이전의 방법보다 30%가 넘는 성능향상을 보임

- R-CNN의 단점을 보완하고자 SPP-Net이 등장

    [SPP-Net](https://chang0517.github.io/computer_vision/SPP-Net/)

- 이전 SPP Net이 가지는 한계점들을 극복하고자 하는 시도에서 출발한 Fast R-CNN이 등장 (대폭 개선)

    [Fast R-CNN](https://chang0517.github.io/computer_vision/Fast-R-CNN/)

- Real Time Object Detection의 포문을 연 논문인 Faster R-CNN이 등장

    → R-CNN Object Detection의 마지막
    -> Faster_RCNN (작성예정)

- YOLO

- CenterNEt


