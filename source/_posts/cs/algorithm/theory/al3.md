---
title: 3.재귀 알고리즘
date: 2021-07-28 19:38:20
tags:
category:
    - Computer Science
    - Algorithm
    - Theory
---
# 재귀 알고리즘

재귀 알고리즘 = 알고리즘 내부에서 한번 이상 자신의 함수를 호출
sum(n) = 1+2+..+n
sum(n) = sum(n-1)+n

```python
def sum(n):
    if n==1 : return 1
    return sum(n-1) + n
```

## 수행시간

T(n) = sum(n)의 수행시간이라 할 때
T(n) = T(n-1)+ c (상수 번의 기본 연산) => 점화식!
     = (T(n-2)+c)+c = T(n-1)+2c
     ...
     =T(1)+(n-1)c (바닥조건)
     =c*n
     따라서 BigO -> O(n)
점화식을 구해 바닥조건까지 전개해가자!!

## 좀 더 복잡한 예제

sum(a,b) = a + (a+1) + .... + (b-1) + b
sum(3,8) = 3+4+5+6+7+8
         = sum(3,5) + sum(6,8)
         ...

```python
def sum(a, b):
    if a == b : return a
    if a>b : return 0
    m = (a+b)//2
    return sum(a,m)+sum(m+1, b)
```
n=2^k라고 가정
T(n) = 2*T(n/2)+c 
     ....
     =2^k * T(n/2^k) + c(1+2+2^2+2^3....+2^(k-1)) T(1)=c이고 등비수열 정리하면
     =2^k*c + c(2^k-1)
     =c*2^k + c * 2^r - c
     = 2cn - c = O(n)

## reverse 함수 예제

reverse(A) = reverse(A\[1:])+A\[:1]
즉 T(n) = T(n-1) + c => O(n)

reverse(A, start, stop)= A\[strat]와 A\[stop-1] 바꾸고, reverse(A, start-1, stop-1)
즉 T(n) = T(n-2) + c = T(1)+(n/2)*c =>O(n)
