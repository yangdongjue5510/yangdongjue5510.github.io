---
title: 1.이진탐색
date: 2021-07-28 19:04:00
tags:
category:
    - Computer Science
    - Algorithm
    - Theory
---

# 이진탐색

오름차순으로 정렬된 n개의 숫자가 저장된 배열 A에서 어떤 값 x가 배열에 있는지 없는지 O(log n) 비교에 알 수 있는 탐색법.

1. 현재 탐색 범위가 A[i]...A[j]라면 가운데값(A[(i+j)/2]) 비교에 알 수 있는 탐색법.
2. 한 번의 비교로 탐색 범위가 반씩 줄어들어 (log n +1)번 이하의 비교로 x 존재 여부 판별.
3. 주어진 배열을 반으로 나눠서 재귀적으로 해결하는 **분할정복 알고리즘**

```python
def binary_search(A, i, j, x):
    if i>j: return -1
    m = (i+j)//2
    if x == A[m]:
        return m
    elif x< A[m]:
        return binary_search(A, i, m-1, x)
    elif x> A[m]:
        return binary_search(A, m+1, j, x)
A = [2*i for i in range(11)]
print(A)

x = int(input("x = "))
index = binary_search(A,0,len(A)-1, x)
if index == -1: print('cannot find')
else : print(index)
```