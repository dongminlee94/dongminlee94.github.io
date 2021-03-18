---
title: "Data Structures & Algorithms Review"
use_math: true
toc: true
toc_sticky: true
categories:
  - CS
---

## Outline

- Sort
- Search
- Array vs Linked list
- Tree vs Graph
- Heap
- Map
- Spanning Tree
- Kruskal vs Prim
- Kruskal & Prim vs Dijkstra

[알고리즘 참고 링크들 정리](https://dongminlee94.github.io/algorithms_references/)

---

## Sort

- Insertion Sort (삽입 정렬)
  - 자리를 순회할 때마다 현재 위치에서 **그 이하의 값들을 순서**대로 정렬
  - 최악 시간복잡도: $O(n^2)$
  - 평균 시간복잡도: $O(n^2)$
  - 공간복잡도: $O(1)$
- Selection Sort (선택 정렬)
  - 자리를 순회할 때마다 **최솟값을 찾아** 현재 위치에 정렬
  - 최악 시간복잡도: $O(n^2)$
  - 평균 시간복잡도: $O(n^2)$
  - 공간복잡도: $O(1)$
- Bubble Sort (버블 정렬)
  - 계속 **두 개씩 비교**하면서 정렬
  - 최악 시간복잡도: $O(n^2)$
  - 평균 시간복잡도: $O(n^2)$
  - 공간복잡도: $O(1)$
- Merge Sort (합병 정렬)
  - **반으로 계속 쪼개가면서** 가장 맨 아래부터 정렬
  - 최악 시간복잡도: $O(n \log n)$
  - 평균 시간복잡도: $O(n \log n)$
  - 공간복잡도: $O(n)$
- Quick Sort (퀵 정렬)
  - **Pivot point**를 정해서 앞뒤로 정렬
  - 최악 시간복잡도: $O(n^2)$
  - 평균 시간복잡도: $O(n \log n)$
  - 공간복잡도: $O(\log n)$
- Heap Sort (힙 정렬)
  - **힙을 이용**하여 최대 힙 또는 최소 힙을 만들어서 정렬
  - 최악 시간복잡도: $O(n \log n)$
  - 평균 시간복잡도: $O(n \log n)$
  - 공간복잡도: $O(1)$

---

## Search

- Sequential Search
  - 리스트에서 찾고자 하는 값을 맨 앞에서부터 끝까지 차례대로 찾아 나가는 것
  - 검색 방법 중 가장 단순하여 구현이 쉽고 정렬되지 않은 리스트에서도 사용할 수 있음
  - 검색할 리스트의 길이가 길면 비효율적
- Binary Search
  - 오름차순으로 정렬된 리스트에서 특정한 값의 위치를 찾는 것
  - 처음 선택한 중앙값이 만약 찾는 값보다 크면 그 값은 새로운 최댓값이 되며, 작으면 그 값은 새로운 최솟값이 됨
  - 검색이 반복될 때마다 목표값을 찾을 확률은 두 배가 되므로 속도가 빠름
  - 검색 원리상 정렬된 리스트에만 사용할 수 있음

---

## Array vs Linked list

### Array

- 논리적 순서와 물리적 순서가 일치
- 해당 값에 접근 용이, 구현이 쉬움
- 삽입 / 삭제 시 순서를 맞춰주어야 함

### Linked list

- 논리적 순서는 array와 동일하나 물리적 순서가 다름
- 해당 값에 접근 시 0번부터 차례대로 접근해야 함으로 비효율적
- 삽입 / 삭제 시 용이

---

## Tree vs Graph

### Tree

- 그래프의 하나의 종류, DAG의 한 종류
- 방향성 & 사이클
  - DAG (Directed Acyclic Graph, 방향이 있는 비순환적 그래프)
  - 사이클 (cycle)이 없는 하나의 연결된 그래프 (Connected Graph)
  - Loop나 circuit이 없음. Self-loop도 없음
- 하나의 루트 노드를 가짐
- 부모-자식 관계
- 계층 모델
- Node가 $N$개인 트리는 <U>항상 $N-1$개의 edge를 가짐</U>

### Graph

- Node와 edge를 하나로 모아 놓은 자료 구조
- 방향성 & 사이클
  - Directed (방향이 있는) & Undirected (방향이 없는) / Cyclic (순환적) & Acyclic (비순환적) 모두 존재
  - Self-loop도 가능
- 루트 노드가 없음
- 부모-자식이 없음
- 네트워크 모델
- 그래프에 따라 edge의 수가 다름

---

## Heap

- **완전 이진 트리**의 일종으로 **우선순위 큐**를 위하여 만들어진 자료구조
  - 완전 이진 트리란?
    - <U>마지막 레벨을 제외</U>하고 모든 레벨이 꽉 채워져 있어야함
    - 마지막 레벨에서는 <U>왼쪽에서 오른쪽</U>으로 채워져야 함
- 부모 노드의 키 값이 자식 노드의 키 값보다 항상 큰 (or 작은) 완전 이진 트리
- 힙 트리에서는 **중복된 값을 허용**한다. (이진 탐색 트리에서는 중복된 값을 허용하지 않는다.)

---

## Map

- [Map vs Dictionary](https://h2hyun37.tistory.com/15)
- [What is the difference between a map and a dictionary?](https://stackoverflow.com/questions/2884068/what-is-the-difference-between-a-map-and-a-dictionary/2884200#2884200)
- [Map, HashMap, TreeMap and HashTable](https://tazkaz.tistory.com/252)
- [HashMap과 HashTable의 차이 1](https://odol87.tistory.com/3)
- [HashMap과 HashTable의 차이 2](https://knoc-story.tistory.com/21)

---

## Spanning Tree

- 그래프의 최소 연결 부분 그래프
  - 최소 연결 = 최소 edge의 수
  - Node가 $N$개이면 edge는 항상 $N-1$개
  - 즉, <U>graph에서 일부 edge를 선택해서 tree 형태가 됨</U>
  - ex) DFS, BFS

### Minimum Spanning Tree

- Spanning tree 중에서 사용된 edge들의 weight 합이 최소인 트리

---

## Kruskal vs Prim

### Kruskal

- **Edge 중심**
- Greedy method를 이용하여 네트워크의 모든 노드를 최소 비용으로 연결하는 edge들을 구하는 것

### Prim

- **Node 중심**
- 시작 노드 지점에서부터 출발하여 spinning tree를 단계적으로 만들어나가는 방법

---

## Kruskal & Prim vs Dijkstra

### Kruskal & Prim

- MST만 찾음
- Undirected graph에서만 사용 가능
- 음수인 edge weight도 다룰 수 있음
- 여러 지점들을 서로 연결하는 도로를 건설하는 데 재료비를 최소화하고 싶을 때 사용 (도착 지점이 없음)

### Dijkstra

- 최단 경로만 찾음
- Directed & Undirected graph 둘 다 사용 가능
- 음수인 edge weight가 하나라도 있을 경우 정확한 계산이 어려울 수 있음
- 출발 지점에서 도착 지점으로 이동하는 경우 시간과 연료를 절약하고 싶을 때 사용
