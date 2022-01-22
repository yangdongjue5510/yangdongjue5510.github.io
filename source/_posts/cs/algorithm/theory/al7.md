---
title: 7.피보나치
date: 2021-07-28 19:43:13
tags:
category:
    - Computer Science
    - Algorithm
    - Theory
---
# 피보나치 수

> ### 피보나치 수열이란

F(0) = 0, F(1) = 1, F(n) = F(n-1) + F(n-2)로 정의되는 수열!

> ### 구현 방법

1. #### 피보나치수 정의를 그대로 재귀함수로 구현

간단하지만..O(g^n) 지수 시간이 걸림..(g는 황금비율 1.1618...)

2. #### 이차원 배열을 재귀적으로 곱하기

(1  1)(F(n-1))

(1  0)(F(n-2))

이렇게 이차원 배열을 곱하면

(F(n))

(F(n-1))

을 구할 수 있다.

따라서 곱해준 이차원 배열을 여러번 곱해주면 피보나치 수열의 값을 알 수 있다!

이때 여러번 곱해주는 연산 power(a, n)로 구해주면, O(logn)이다!


```python
def fibo_rec(n):
	if n <= 1: return n
	return fibo_rec(n-1)+fibo_rec(n-2)

def fibo_array(n):
	F = [0, 1]
	for i in range(2, n+1):
		F.append(F[i-1] + F[i-2])
	return F[n]

def fibo_three(n):
	f1 = 0
	f2 = 1
	for i in range(2, n+1):
		f3 = f1 + f2
		f1 = f2
		f2 = f3
	return f2

n = int(input("n = "))
print(fibo_rec(n), fibo_array(n), fibo_three(n))
```