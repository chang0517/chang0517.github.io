---
title: "자료구조개론"
excerpt: "Basic Knowledge"

categories:
  - basic_knowledge
tags:
  - basic_knowledge
use_math: true
last_modified_at: 2021-12-16T15:15:00-05:00

---


## 자료구조란?

- 데이터를 사용하기 쉽게 조직하는 방법
- 배열 형식
    - Sequential Access(static) - 정적 배열
        - 크기가 고정되어 있으며, 한번 생성되면 크기를 변경할 수 없음.
        - 주로 C언어에서 사용 (크기를 할당, 해당 크기만큼의 연속된 메모리 공간을 할당)
    - Dynamic
        - 크기를 지정하지 않고 자동으로 사이즈를 조절
        - 미리 초기값을 작게 잡아 배열을 생성 → 데이터가 추가되면 그 크기를 늘려주고 복사하는 방식
        - 늘려주는 양은 언어마다 다름
- Types of Data Structure
  
    ![자료 1](https://user-images.githubusercontent.com/43038405/146341790-c280f65e-60e7-4f93-8a69-95a535a637ea.png)
    - Linear Data Structure
      
        ![자료 2](https://user-images.githubusercontent.com/43038405/146342095-5b4b0ba3-5408-4298-ab80-1124793d5e21.png)
        
        - 자료를 구성하는 원소들을 순차적으로 나열시킨 형태
        - 종류
            - Stack
                - 리스트 한 쪽 끝으로만 자료의 Insert, Delete 작업이 이루어짐.
                - LIFO(Last in, First Out) 방식 - 가장 마지막으로 들어온 데이터부터 빠져나감.
                  
                    ![자료 3](https://user-images.githubusercontent.com/43038405/146342093-c0075ac2-9900-4aca-92de-13bfaf350c7b.png)
                    
                - 활용
                    - Arithmetic Expression (In-fix, Post-fix, Pre-fix 표현)
                      
                        ![자료 4](https://user-images.githubusercontent.com/43038405/146342091-7b3c335e-9829-473f-80c7-4d99ac37c5a2.png)
                
            - Queue
                - 선형 리스트의 한쪽에서는 Insert 작업이, 다른 한쪽에서는 Delete 작업이 이루어짐
                - FIFO(First in, First Out) - 가장 먼저 삽입된 자료가 가장 먼저 출력
                  
                    ![자료 5](https://user-images.githubusercontent.com/43038405/146342088-a1cbd5ad-d7f3-491e-a234-6b1def982d7b.png)
                    
                - 용어
                    - Rear - 삽입 작업 이루어지는 쪽
                    - Front - 삭제 작업 이루어지는 쪽
                - Type
                    - Simple Queue
                    - Circular Queue
                        - Front와 Rear가 동일
                    - Priority Queue
                        - 들어간 순서에 상관없이 우선순위가 높은 데이터가 먼저 나오는 구조
                        - Heap으로 구현할 수 있음.
            - Linked List
                - 자료들을 임의의 기억공간에 기억시키되 자료 항목의 순서에 따라 포인터를 이용하여 연결시킨 자료구조
                  
                    ![자료 6](https://user-images.githubusercontent.com/43038405/146342087-a99259e9-892f-45ae-ac29-696341aacceb.png)
                    
                - 장점 : Pointer를 활용한 조작으로 중간에 Insert / Delete가 빠르고 용이
                - 단점 : 검색 속도, 접근 속도가 느림
    - Non-Linear Data Structure
      
        ![자료 7](https://user-images.githubusercontent.com/43038405/146342085-e3ef7469-bf65-4b6d-862b-e3bca10962ab.png)
        - 하나의 자료 뒤에 여러 개의 자료가 존재할 수 있는 형태
        - 종류
            - Tree
              
                ![자료 8](https://user-images.githubusercontent.com/43038405/146342072-49d7996a-c549-496a-abdd-2c26088500b7.png)
                
                - 정점(Node)와 선분(Branch)를 이용하여 자료 간 계층적 관계를 표현한 자료구조
                - 용어
                    - Root Node: 부모가 없는 노드
                    - Leaf Node : 자식이 없는 노드
                    - Degree : 하위 트리 개수, 각 노드가 지닌 가지의 수
                    - Degree of Tree : 트리의 최대 차수
                    - Height : Root Node에서 가장 깊숙히 있는 노드의 깊이
                - 종류
                    - Binary Tree (이진 트리)
                        - 각 노드가 최대 2개의 자식을 갖는 Tree
                        - 순회 종류
                            - in-order traversal (중위 순회) : 왼쪽 가지 → 현재 노드 → 오른쪽 가지
                            - pre-order traversal (전위 순회) : 현재 노드 → 왼쪽 가지 → 오른쪽 가지
                            - post-order traversal (후위 순회) : 왼쪽 가지 → 오른쪽 가지 → 현재 노드
                    - Binary Search Tree (이진 탐색 트리)
                        - 모든 노드가 특정 순서를 따르는 속성이 있는 Binary Tree
                        - 모든 왼쪽 자식들 ≤ n < 모든 오른쪽 자식들
                          
                            → 모든 Node 중 최소가 좌측 끝에 위치
                            
                        - Successor : 삭제 대상 노드의 오른쪽 서브트리 중 최소값채ㅡ
                        - 복잡도 = O(log n) → 삽입, 삭제, 검색 모두 같음.
                    - 공부 출처
                      
                        [[자료구조] 트리(Tree)란 - Heee's Development Blog](https://gmlwjd9405.github.io/2018/08/12/data-structure-tree.html)
                        
                        - 자료구조개론 (나브라티색서나 교수님) 강의안
            - Graph
                - 개념
                    - 정점(Vertex)과 간선(Edge)로 이루어진 자료구조
                    - 정점 간의 관계를 표현하는 조직도 → Tree는 그래프의 일종
                    - 그래프와 달리 Root 노드, 부모와 자식 등의 개념이 존재 X
                    - 알고리즘에서 굉장히 활용도가 높기 때문에 중요 (DFS, BFS)
                - 구현 방법
                    - 인접행렬 (Adjacency Matrix)
                      
                        ![자료 10](https://user-images.githubusercontent.com/43038405/146342069-15146a3d-8c4f-48b9-963c-4b49ca1820ec.png)
                        
                        → 장점 : 두 점에 대한 연결 정보를 조회할 때 O(1)의 시간복잡도면 가능 / 구현이 간단
                        
                        → 단점 : 입력할때 O($n^2$)의 시간복잡도 소요 / 무조건 2차원 배열의 필요로 공간 낭비
                        
                    - 인접리스트 (Adjacency List)
                      
                        ![자료 11](https://user-images.githubusercontent.com/43038405/146342058-3b59c758-ade6-4c0d-befd-88a93d5c946b.png)

                        
                        → 장점 : 정점들의 연결 정보를 탐색할 때, O(n)의 시간 복잡도면 가능 / 공간 낭비 x
                        
                        → 단점 : 특정 두 점의 연결을 확인할 때, 인접행렬보다 오래 걸림 / 구현이 어려움
                    
                - DFS (Depth First Search) - 깊이 우선 탐색
                  
                - BFS (Breadth First Search) - 너비 우선 타
    
- Hashing
- Heaps
- Heapsort

