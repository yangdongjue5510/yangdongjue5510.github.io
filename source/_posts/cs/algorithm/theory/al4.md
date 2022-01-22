---
title: 4.일반적인 탐색
date: 2021-07-28 19:42:14
tags:
category:
    - Computer Science
    - Algorithm
    - Theory
---
# 일반적인 선택 문제

## n개의 값중 k 번째 작은 수 찾기 - QuickSelction

1. #### 배열 중 임의의 수 p를 고른다.(이 p는 pivot)
2. #### p보다 작은 값을 A에 따로 모으고, p보다 큰 값을 B에 따로 모으고, p와 같은 값을 M에 따로 모은다.
3. #### 만약 A의 원소가 k보다 작으면 A의 k번째 작은 값이 전체 데이터의 k번째 작은 값이다.
4. #### 이제 A에서 k보다 작은값 찾기는 재귀적으로 해결 가능하다.
5. #### 만약 A와 M의 원소 갯수 합이 k보다 작을 때는 B에서 (k-A-M)번째 작은 값을 구하면 된다.
6. #### 나머지 경우는 그냥 p를 반환하면 된다.

```python
def quickSelet(L,k):
    #1.
    p=L[0]
    A,B,M=[],[],[]
    #2.
    for x in L:
        if p>x:A.append(x)
        elif p<x:B.append(x)
        else: M.append(x)
    #3-6.
    if len(A)>k : return quickSelect(A,k)
    elif len(A)+len(M)<k: return quickSelect(B,k)
    else: return p
```
> Worst case?

p를 첫 원소로 했는데 가장 큰 원소가 맨 끝 원소일 때.
T(n) = T(n-1)+n
     = 1+2+3+...+n = n(n+1) / 2  -> O(n^2)

평균적인 경우는 O(n)
