---
title: 3. 양방향리스트
date: 2021-07-28 20:00:09
tags:
category:
    - Computer Science
    - Data Structure
---
## **a. 왜 양방향 연결 리스트가 필요한가?**

한방향 연결 리스트는 다음 노드를 연결하는 링크만 존재, 이전 노드를 알려면 head부터 다시 탐색을 해야된다...

이런 불필요한 연산을 줄이고자 이전 노드를 연결하는 링크가 있는 양방향 연결 리스트를 구현하고자 한다.

#### 양방향 연결 리스트의 주요 개념은 다음과 같다.

1.  이전 노드로의 링크(prev)를 포함해 이전 노드로 이동 가능
2.  마지막 노드와 첫 노드가 서로 연결된 원형 리스트를 가정
3.  첫 노드(head)는 항성 dummy 노드가 되어야 함  
    (dummy 노드는 리스트의 처음을 구분해주는 마커 기능을 하는 특별한 노드다.)

![img](/img/ds/doublylinkedlist.png)

## **b. 노드 클래스**

```python
class Node:
    def __init__(self, key=None):
        self.key = key
        self.next = self.prev = self #두개의 링크가 자기 자신에게 향함
        
    def __str__(self):    #print(node)했을 경우 출력할 문자열
        return str(self.key)
```

## **c. 양방향 연결 리스트 클래스**

```python
class DoublyLinkedList:
    def __init__(self):
        self.head = Node()
        self.size = 0
    #아래 함수들은 한방향 리스트와 같다.
    def __iter__(self):
    def __str__(self):
    def __len__(self):
```

### c-1. splice(a,b,x) 연산 \*중요\*

노드 a부터 노드 b까지를 떼어내 노드 x 뒤에 붙여 넣는 연산

이때, 두 가지 조건이 만족되어야 한다.

-   조건 1 : a와 b가 동일하거나 a 다음에 b가 나타나야 함
-   조건 2 : head 노드(dummy)와 x는 a와 b 사이에 포함되면 안됨.

```python
def splice(self, a, b, x):
    if a == None or b == None or x == None :
        return
    
    ap = a.prev
    bn = b.next
    
    #자르는 연산
    ap.next = bn
    bn.prev = ap
    
    #x뒤에 a~b를 삽입
    xn = x.next
    xn.prev = b
    b.next = xn
    a.prev = x
    x.next = a
```

### c-2. 탐색 및 기본 연산

search(key) : key 값 가지는 노드 리턴, 없으면 None 리턴

isEmpty() : 빈 리스트면 True, 아니면 False

first(), last() : 처음과 마지막 노드를 리턴, 빈 리스트면 None 리턴

```python
def search(self, key):
    v = self.head
    while v.next !=self.head:
        if v.key==key:
            return v
        v=v.next
    return None

def isEmpty(self):
    v = self.head
    if v.next ==self.head:
        return True
    else:
        return False
        
def first(self):
    v = self.head
    if v.next!=self.head:
        return v.next
    else:
        return None
        
def last(self):
    v = self.head
    if v.prev != self.head:
        return v.prev
    else:
        return None
```

### c-3. 이동과 삽입 연산

\*splice 함수가 매우 빈번하게 사용된다!!!!\*

```python
def moveAfter(self, a, x): #노드 a를 노드 x 뒤로 이동
    self.splice(a, a, x) #a를 떼어내어 x뒤로 붙인 것과 같다

def moveBefore(self, a, x): #노드 a를 노드 x 앞으로 이동
    self.splice(a, a, x.prev)

def insertAfter(self, x, key): #key값을 갖는 노드를 x뒤에 삽입
    moveAfter(Node(key), x)

def insertBefore(self, x, key): #key값을 갖는 노드를 x앞에 삽입
    moveBefore(Node(key), x)
    
def pushFront(self, key): # key값을 갖는 노드를 헤드 뒤에 삽입
    insertAfter(self.head, key)

def pushBack(self, key): # key값을 갖는 노드를 헤드 앞에 삽입
    insertBefore(self.head, key)
```

### c-4. 삭제 연산

remove(x) : 노드 x를 제거

_만약 key값만 알고 노드 이름을 모르면 search(key)로 하여 노드 이름을 찾는다._

popFront() : head 다음에 있는 노드의 데이터 값 리턴. 빈 리스트면 None

popBack() : head 이전에 있는 노드의 데이터 값 리턴. 빈 리스트면 None

```python
def remove(self, x):
    if x == None or x == self.head:
        return
    x.prev.next, x.next.prev = x.next, x.prev
    
def popFront(self):
    if self.isEmpty(): return None
    key = self.head.next.key
    self.remove(self.head.next)
    return key
    
def popBack(self):
    if self.isEmpty(): return None
    key = head.prev.key
    self.remove(head.prev)
    return key
```

### c-5. 연산의 시간 복잡도

<table style="border-collapse: collapse; width: 100%;" border="1" data-ke-align="alignLeft" data-ke-style="style4"><tbody><tr><td style="width: 50%;">moveAfter / moveBefore</td><td style="width: 50%;">O(1)</td></tr><tr><td style="width: 50%;">pushFront / pushBack</td><td style="width: 50%;">O(1)</td></tr><tr><td style="width: 50%;">insertAfter / insertBefore</td><td style="width: 50%;">O(1)</td></tr><tr><td style="width: 50%;">popFront / popBack</td><td style="width: 50%;">O(1)</td></tr><tr><td style="width: 50%;">remove</td><td style="width: 50%;">O(1)</td></tr><tr><td style="width: 50%;">search</td><td style="width: 50%;">O(n)</td></tr></tbody></table>

moveAfter(Before) , pushFront(Back), insertAfter(Before)는 splice의 시간복잡도와 같다. O(1)