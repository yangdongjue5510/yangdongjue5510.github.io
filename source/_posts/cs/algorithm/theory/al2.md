---
title: 2.분할정복법
date: 2021-07-28 19:05:36
tags:
category:
    - Computer Science
    - Algorithm
    - Theory
---
# 분할정복법
큰 문제를 작은 문제로 분할해 재귀적으로 해결

> a와 n 입력받아 a^n 값 구하는 power1(a,n)을 분할 정복법으로 해보자.


power1(a, n) = power1(a, n-1)*a 이다.

```python
def power1(a, n):
    if n==1: return a
    return a*power1(a, n-1)
```
power1의 걸리는 시간 T(n)은
T(n) = T(n-1)+c = O(n)

> 다른 방법은 없을까? power2를 보자

```python
def power2(a, n):
    if n==1 : return a
    if n==0 : return 1
    if n%2==0:
        power2(a, n//2)*power2(a, n//2)
    else : return power2(a, n//2) * power2(a, n//2)*a
```
power2의 걸리는 시간 T(n)은
T(n) = 2T(n/2)+c=O(n)

> 자세히 보면 power2에는 중복 사용되는 메소드가 있다.
이걸 좀 더 개선해보자

```python
def power3(a, n):
    if n==0: return 1
    x = power3(a, n//2)
    if n%2==0: return x*x
    else return x*x*a
```
T(n) = T(n/2)+c = O(logn)