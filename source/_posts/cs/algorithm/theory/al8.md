---
title: 8. 기본 정렬 알고리즘
date: 2021-08-04 13:56:38
tags:
category:
    - Computer Science
    - Algorithm
    - Theory
---
> 파이썬에서 제공하는 정렬
```python
A = [3,1,2,-5]
A.sort() #[-5,1,2,3]
A.sort(reverse = True) #[3,2,1,-5]
```

### 정렬 알고리즘의 두 가지 특성
1. stable vs unstable
만약 \[2,5,2,7]을 오름차 정렬했을 때 ,\[2,2,5,7]로 정렬이 될 텐데, 이 두 개의 2가 입력 순으로 정렬되면 stable하다고 한다.(첫번째 2가 정렬해도 첫번째 2가 되는지)
2. in-place vs not in-place
상수 개의 변수를 사용하는지 여부를 말한다.
추가 메모리 사용을 O(1) 사용하는 in-place 알고리즘이고, O(n)이면 not in-place이다.

정렬 알고리즘을 만들 때, stable & in-place를 추구해야 한다.

## 간단하지만 느린 알고리즘 : 기본 알고리즘
n-1번의 라운드를 통해 일일히 비교해 자리 바꾸는 방식의 알고리즘
**selection, bubble, insertion** 이 있다.

### selection
- 매 라운드마다 정렬되지 않은 수 중 가장 큰 값을 찾는다.
- 그 값을 정렬되지 않은 수 중 가장 끝 수와 swap한다.
- 이 과정을 n-1번 한다.
- n*(n-1)/2 비교 + (n-1) 교환

```python
def selection_sort(A,n):
    for i in range(n-1, 0, -1):
        m = A.index(max(A[0:i+1])) #정렬되지 않은 수 중 가장 큰 값의 인덱스
        A[i], A[m]=A[m], A[i]
```
### bubble
- 매 라운드마다 맨 뒤부터 두 개의 수를 비교하여, 앞 수가 뒷 수보다 크면 둘을 swap한다.
- 이 비교를 끝까지 하면, 가장 작은 수는 맨 앞으로 가게 된다.
- 이 과정을 n-1번 한다.
- n*(n-1)/2 비교 + n*(n-1)/2 교환

```python
def bubble_sort(A,n):
    for i in range(n):
        for j in range(n-1, i, -1):
            if A[j-1] > A[j]:
                A[j-1], A[j] = A[j], A[j-1]
```
### insertion
- 맨 처음에는 앞 두개를 비교해서 정렬한다.
- 그 다음 라운드부터는 다음 수와 앞에 정렬한 수들과 비교해서 해당 수가 들어가야할 자리를 찾아 사이에 넣는다.
- 이런식으로 하면 처음에는 2개의 수가 정렬, 그 다음엔 3개의 수, 4개의 수....
- 이 과정을 n-1번 한다.
- n*(n-1)/2 비교 + n*(n-1)/2 교환

```python
def insertion_sort(A,n):
    for i in range(1,n):
        m=0
        for j in range(i-1, -1, -1):
            if A[j]<A[i]:
                m=j+1
                break
        for k in range(i, m, -1):
            A[k], A[k-1] = A[k-1], A[k]
```

> 이 세 알고리즘 모두 O(n^2)!
모두 in-place하고, selection만 unstable하다!