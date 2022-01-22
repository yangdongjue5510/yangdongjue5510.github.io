---
title: 9. Quick 정렬 알고리즘
date: 2021-08-04 15:10:36
tags:
category:
    - Computer Science
    - Algorithm
    - Theory
---
## 퀵 정렬 알고리즘
이론적으로는 빠르지 않지만(O(n^2)), 실제로는 가장 빠른 정렬 알고리즘
quick selection과 비슷한 원리임.

### not in-place quick sort algorithm
> 어떤 list A를 퀵 정렬 하려면...

1. 피벗을 A\[0]으로 잡고, S,M,L 세가지 배열을 선언함
2. A에 있는 모든 수들이 피벗보다 크면 L, 작으면 S, 같으면 M에 append =>n-1번 비교
3. 재귀적으로, quickSort(S)+M+quickSort(L)을 반환하면 끝이다.

``` python
def notInPlaceQuickSort(A):
    if len(A)<=1: return A
    pivot = A[0]
    S, M, L = [], [], [] #1.
    for x in A:#2.
        if x< pivot : S.append(x)
        elif x>pivot: L.append(x)
        else M.append(x)
    return notInPlaceQuickSort(S)+M+notInPlaceQuickSort(L)#3.
```

실행 시간은 T(n)= T(S)+T(L)+cn 
*#여기서 cn은 n-1번 비교를 상수번 했다는 의미.*

최악의 경우는 S, L 중 하나가 0이 되는것.
T(n)=T(n-1)+cn = O(n^2) => 이렇게 될 확률이 낮다!

최선의 경우는 S, L이 균등하게 나뉘는 것
T(n) = T(n/2)+T(n/2)+cn = O(n*log n) => 평균적으로 이렇게 시간이 걸림!

##### 그런데, 지금 하는 방식은 배열을 복사해서 하는 방식. 즉 not in-place이다.


### in-place quick sort algorithm
> 다른 방식으로 구현해보자.

1. 인수로 배열 A와, 퀵소트할 첫 인덱스와 끝 인덱스를 전달한다.

quickSort(A, first, last)

2. 새로운 배열을 사용하지 않고, A 안에서 분류하기 위해 left, right 변수를 도입한다.
3. left는 가장 왼쪽 값(피벗을 제외한), right는 가장 오른쪽 값을 가르킨다.

left, right = first+1, last

4. left가 가르키는 수가 피벗보다 더 작으면 다음 수로 넘어간다.
5. right는 가르키는 수가 피벗보다 더 크면 이전 수로 넘어간다.(이때 같은 경우도 넘어간다.)
6. 이제 두 변수가 멈췄고, left가 right보다 작거나 같다면 두 수가 가르키는 값을 바꾼다
7. 이제 left가 right보다 클 때까지 다시 4-6번을 진행한다.
###


8. 이제 first+1 ~ right은 S가 되고, left ~ last는 L이 된다.
9. 이제 S와 L 사이에 피벗을 집어넣으면 SML 분리가 끝난다.
10. 이제 S와 L을 재귀적으로 퀵소트하면된다.

```python
def quickSort(A, first, last):
    #1.~3.
    if first >= last : return
    left, right = first+1, last
    pivot = A[first]
    #4.~7.
    while left <= right: #7.
        while left <= last and A[left]<pivot: #4.
            left +=1
        while right > first and A[right] >=pivot: #5.
            right -=1
        if left <= right: #6.
            A[left], A[right] = A[right], A[left]
            left += 1
            right -=1
    #8.~10.
    A[first], A[right] = A[right], A[first]
    quickSort(A, first, right-1)
    quickSort(A, right+1, last)
```