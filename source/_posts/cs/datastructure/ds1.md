---
title: 1. 연결리스트
date: 2021-07-28 15:54:58
tags:
category:
    - Computer Science
    - Data Structure
---
### **a. 연결리스트 기본 개념**

연결리스트는 노드가 링크에 의해 기차처럼 연결된 순차 자료구조.

노드는 실제 값을 가지고 있는 data 정보와 인접 노드로 향하는 link 정보로 구성된 클래스로 구현한다.

다른 값에 접근하려면 링크에 따라 원하는 노드의 데이터에 접근한다.

다만, 배열처럼 index로 접근할 수 없다.

어떤 값을 찾으려면 처음부터 순차적으로 찾아야한다.

배열과 연결리스트의 차이를 그림으로 파악해보자!

![img](/img/ds/linkedlist.jpeg)

### **b. 한방향 연결리스트**

노드들이 한 방향으로 연결된 리스트

1.  가장 앞에 있는 노드를 head 노드라고 부르자.
2.  가장 마지막 노드는 다음 노드가 없으므로 마지막 노드의 next 링크는 None이다.

#### b-1. 노드 클래스 구현하기

```python
class Node:
    def __init__(self, key=None, value=None):
        #노드를 구분하는 key, 필요할 경우 추가 데이터 value
        self.key = key
        self.value = value
        self.next = None
    def __str__(self):
        #print(node)인 경우 출력할 문자열
        return str(self.key)
```

#### b-2. 한방향 연결 리스트 클래스 구현하기

```python
class SinglyLinkedList:
    def __init__(self):
        self.head = None
        self.size = 0
    def __iter__(self): #반복하게 만드는 반복자. yield가 있는 generator
        v = self.head
        while v != None:
            yield v  #return과 비슷
            v = v.next
    def __str__(self):
        return "->".join(str(v) for v in self)
    def __len__(self):
        return self.size #len(a) = a의 노드 개수 리턴
```

### **c. 지원하는 연산 (삽입, 삭제, 탐색 등...)**

<table style="border-collapse: collapse; width: 100%; height: 120px;" border="1" data-ke-align="alignLeft" data-ke-style="style4"><tbody><tr style="height: 20px;"><td style="width: 50%; height: 20px;">pushFront(key)</td><td style="width: 50%; height: 20px;">key값을 갖는 새 노드를 만들어 head 앞에 삽입</td></tr><tr style="height: 20px;"><td style="width: 50%; height: 20px;">pushBack(key)</td><td style="width: 50%; height: 20px;">key값을 갖는 새 노드를 마지막 노드 뒤에 삽입</td></tr><tr style="height: 20px;"><td style="width: 50%; height: 20px;">popFront()</td><td style="width: 50%; height: 20px;">첫 노드(head)를 삭제 후 key값을 리턴</td></tr><tr style="height: 20px;"><td style="width: 50%; height: 20px;">popBack()</td><td style="width: 50%; height: 20px;">마지막 노드를 삭제 후 key값을 리턴</td></tr><tr style="height: 20px;"><td style="width: 50%; height: 20px;">search(key)</td><td style="width: 50%; height: 20px;">key값을 갖는 노드를 찾아 리턴</td></tr><tr style="height: 20px;"><td style="width: 50%; height: 20px;">remove(v)</td><td style="width: 50%; height: 20px;">노드 v를 제거</td></tr></tbody></table>

#### c-1. pushFront vs pushBack

pushFront는 현재 head 앞에 새 노드를 생성해 앞에 삽입한다.

```python
def pushFront(self, key, value=None):
    new_node = Node(key, value)
    new_node.next = self.head
    self.head = new_node
    self.size += 1
```

pushBack은 마지막 노드의 next 링크가 새로운 노드로 연결되도록 해야한다. 만약 마지막 노드가 없다면(즉 리스트가 비었다면) 새 노드가 리스트의 head가 되어야 한다.

```python
def pushBack(self, key, value=None):
    new_node = Node(key, value)
    if self.size == 0:
        self.head = new_node
    else :
        tail = self.head
        while tail.next != None: #마지막 노드 찾기
            tail = tail.next
        tail.next = new_node
    self.size += 1
```

#### c-2. popFront vs. popBack

popFront는 빈 리스트라서 head를 지울 수 없는 경우와 그렇지 않은 경우로 나눠 구현한다.

```python
def popFront(self):
    if self.size == 0
        return None
    else :
        x = self.head
        key = x.key
        self.head=x.next
        self.size=self.size-1
        del x	#메모리 상에서 x를 제거
        return key
```

popBack은 마지막 노드를 찾고 지우고 그 전 노드의 링크를 수정해야 하므로, 마지막 전 노드를 알아야 한다.

(다시 말하면 마지막 노드는 next링크가 None인 노드이다.

마지막 전 노드를 마지막 노드로 하고 싶으면 해당 노드의 next링크를 None으로 해줘야 된다는 의미다.)

연결리스트는 인덱스로 접근이 안되니, 일일히 처음부터 마지막 전 노드까지 찾아가야 한다.(O(n))

총 세가지로 나눠 구현해야 한다.

1.  빈 리스인 경우
2.  리스트에 노드가 하나만 있는 경우
3.  리스트에 두 개 이상의 노드가 있는 경우

```python
def popBack(self):
    if self.size ==0
        return None
    else :
        prev, tail = None, self.head
        while tail.next != None:
            prev = tail
            tail = tail.next
        if prev == None:
            self.head = None
        else :
            prev.next = tail.next
        key = tail.key
        del tail
        self.size -= 1
        return key
```

#### c-3. search(key)

key 값을 저장한 노드를 찾아 리턴하거나 없으면 None을 반환하는데, 두가지 방법이 있다.

1.  head부터 next 링크를 따라가면서 찾기
    
    ```python
    def search(self, key):
        v = self.head
        while v != None:
            if v.key == key:
                return v
            v = v.next
        return v​
    ```
    
2.  for 루프를 이용하는 방법(\_\_iter\_\_(self)에 의해 가능)
    
    ```python
    def search(self, key):
        for v in self:
            if v.key == key:
                return v
        return None​
    ```
    

#### c-4. remove(v)

노드 v를 리스트에서 제거하는 함수. 세가지 경우를 고려해 구현한다.

1.  리스트가 비어있거나, 노드 v가 None인 경우 -> do nothing
2.  노드 v가 head인 경우 -> popFront 호출
3.  그 외의 경우 -> v의 전 노드 w를 찾은 후 w.next = v.next로 w의 링크 수정

```python
def remove(self, x):
		# 노드 x를 제거한 후 True리턴. 제거 실패면 False 리턴
		pre=None
		now=self.head
		if self.size==0:
			return False
		elif v==self.head:
			popFront()
			return True
		else :
			while now!=x:
				pre=now
				now=now.next
			pre.next=now.next
			del now
			self.size-=1
			return True
```