---
title: 5. 스택과큐
date: 2021-07-28 20:00:30
tags:
category:
    - Computer Science
    - Data Structure
---
### **a. 스택 Stack**

차례대로 삽입하고 최근에 저장된 값을 삭제(FILO)

<table style="border-collapse: collapse; width: 100%; height: 100px;" border="1" data-ke-align="alignLeft" data-ke-style="style4"><tbody><tr style="height: 20px;"><td style="width: 50%; height: 20px;">push</td><td style="width: 50%; height: 20px;">스택에 값 추가</td></tr><tr style="height: 20px;"><td style="width: 50%; height: 20px;">pop</td><td style="width: 50%; height: 20px;">가장 나중에 push된 값을 스택에서 제거하고 반환</td></tr><tr style="height: 20px;"><td style="width: 50%; height: 20px;">top</td><td style="width: 50%; height: 20px;">가장 나중에 push된 값을 제거하지 않고 반환</td></tr><tr style="height: 20px;"><td style="width: 50%; height: 20px;">__len__</td><td style="width: 50%; height: 20px;">스택의 저장된 요소 갯수를 반환</td></tr><tr style="height: 20px;"><td style="width: 50%; height: 20px;">isEmpty</td><td style="width: 50%; height: 20px;">스택에 요소가 존재하는지 참거짓</td></tr></tbody></table>

```python
# stack_queue.py 에 저장
class Stack:
	def __init__(self):
		self.items = []	# 데이터 저장을 위한 리스트 준비
	def push(self, val):
		self.items.append(val)
	def pop(self):
		try:	# pop할 아이템이 없으면
			return self.items.pop()
		except IndexError:	# indexError 발생
			print("Stack is empty")
	def top(self):
		try:
			return self.items[-1]
		except IndexError:
			print("Stack is empty")
	def __len__(self):	# len()로 호출하면 stack의 item 수 반환
 		return len(self.items)
	def isEmpty(self):
		return self.__len__() == 0

# for test
S = Stack()
S.push(10)
S.push(2)
print(S.top())
print(S.pop())
print(len(S))
print(S.isEmpty())
```

### **b. 스택 활용**

#### b-1. 괄호짝 맞추기

입력 값 : 괄호로 이뤄져 있는 문자열 ex. ()()()

반환 값 : 괄호 짝이 맞는지 참 거짓 ex. True

```python
class Stack:
	def __init__(self):
		self.item=[]
	def push(self, a):
		self.item.append(a)
	def pop(self):
		try:
			return self.item.pop()
		except IndexError:
			print('Stack is empty.')
	def top(self):
		try:
			return self.item[-1]
		except IndexError:
			print('Stack is empty.')
	def __len__(self):
		return len(self.item)
	def isEmpty(self):
		return len(self.item)==0
	
# pseudo code
def parChecker(parSeq):
	S=Stack()
	for i in parSeq:
		if i =='(':
			S.push('(')
		elif i ==')':
			if S.isEmpty():
				print(False)
				return False
			else : 
				S.pop()
	if S.isEmpty():
		print(True)
		return True
	else : 
		print(False)
		return False
parSeq=list(input())
parChecker(parSeq)
```

#### b-2. infix 수식을 postfix로 바꾸기

```python
class Stack:
	def __init__(self):
				self.items = []
	def push(self, val):
				self.items.append(val)
	def pop(self):
		try:
			return self.items.pop()
		except IndexError:
			print("Stack is empty")
	def top(self):
		try:
			return self.items[-1]
		except IndexError:
			print("Stack is empty")
	def __len__(self):
		return len(self.items)
	def isEmpty(self):
		return self.__len__() == 0
def infix_to_postfix(infix):
	opstack = Stack()
	outstack = []
	token_list = infix.split()
			# 연산자의 우선순위 설정
	prec = {}
	prec['('] = 0
	prec['+'] = 1
	prec['-'] = 1
	prec['*'] = 2
	prec['/'] = 2
	prec['^'] = 3
	for token in token_list:
		if token == '(':
			opstack.push(token)
		elif token == ')':
			while True:
				if opstack.top()=='(':
				    opstack.pop()
				    break
				else :
					outstack.append(opstack.pop())
		elif token in '+-/*^':
			while True:
				if opstack.isEmpty():
					opstack.push(token)
					break
				elif prec[opstack.top()]>=prec[token]:
					    outstack.append(opstack.pop())
				else:
					opstack.push(token)
					break
		else: # operand일 때
			outstack.append(token)
			# opstack 에 남은 모든 연산자를 pop 후 outstack에 append
	for i in range(opstack.__len__()):
		outstack.append(opstack.pop())
	return " ".join(outstack)

infix_expr = input()
postfix_expr = infix_to_postfix(infix_expr)
print(postfix_expr)
```

#### b-3. Postfix 계산

```python
class Stack:
	def __init__(self):
		self.items=[]
	def push(self, val):
		return self.items.append(val)
	def pop(self):
		try:
			return self.items.pop()
		except IndexError:
			print('stack is empty')
	def top(self):
		try:
			return self.items[self.items.len()-1]
		except IndexError:
			print('stack is empty')
	def __len__(self):
		return self.items.len()
def compute_postfix(postfix):
	opstack = Stack()
	token_list = postfix.split()
	for i in token_list:
		if i in '+-*/^':
			a=opstack.pop()
			b=opstack.pop()
			if i =='+':
				opstack.push(a+b)
			elif i =='-':
				opstack.push(b-a)
			elif i =='*':
				opstack.push(a*b)
			elif i =='/':
				opstack.push(b/a)
			elif i =='^':
				opstack.push(b**a)
		else:
			opstack.push(int(i))
	print('%.4f'%opstack.pop())

postfix=input()
compute_postfix(postfix)
```

### **c. 인터뷰 문제**

1\. 스택을 하나 혹은 두개 사용해 push, pop, min 세 연산 모두 O(1) 시간에 수행되도록 하려면?

2\. 스택 두 개를 써서 큐를 구현해라.(enqueue, dequeue를 구현하라)

### **a. 큐 Queue**

가장 최근에 저장된 값 다음에 저장.

반환은 가장 먼저 저장된 값부터. FIFO(First in First out)원칙.

<table style="border-collapse: collapse; width: 100%;" border="1" data-ke-align="alignLeft"><tbody><tr><td style="width: 50%;">enqueue</td><td style="width: 50%;">큐의 오른쪽에 삽입(push와 같음)</td></tr><tr><td style="width: 50%;">dequeue</td><td style="width: 50%;">가장 왼쪽에 저장된 값을 삭제 후 리턴</td></tr><tr><td style="width: 50%;">front</td><td style="width: 50%;">가장 왼쪽에 저장된 값을 삭제하지 않고 리턴</td></tr><tr><td style="width: 50%;">isEmpty</td><td style="width: 50%;">큐가 비어져있는지 참거짓</td></tr><tr><td style="width: 50%;">len</td><td style="width: 50%;">큐의 요소 갯수 반환</td></tr></tbody></table>

```python
class Queue:
     def __init__(self):
         self.items=[]
         self.front_index=0 #다음 dequeue될 값의 인덱스
     def enqueue(self, val):
         self.items.append(val)
     def dequeue(self):
         if len(self.items)==0 or self.front_index==len(self.items):
             print("Queue is empty")
         else : 
             x = self.items[self.front_index]
             self.front_index +=1
             return x
     def front(self):
         if len(self.items) ==0 or self.front_index == len(self.items):
             print("queue is empty")
         else:
             return self.items[self.front_index]
     def __len__(self):
         return len(self.items)-self.front_index
     def isEmpty(self):
         return len(self)
```

**_dequeue를 상수시간에 실행하기 위해선 dequeue가 될 값의 인덱스를 저장하고 관리해야 한다._**

\-> dequeue가 되면, 그 값을 실제로 지우는 것이 아닌 front\_index값을 하나 늘려가며 다음 dequeue 될 예정 값의 인덱스를 가르키도록 관리한다.

실제로 dequeue될 때마다 값을 삭제시키면, 모든 값들을 한 칸씩 왼쪽으로 이동하는 시간이 소요되게 된다.O(n)

### **a-1. 큐 활용 : Josephus game**

```python
import Queue #큐 클래스 import. 이 부분은 달라질 수 있음
def Josephus(n, k):
    Q=Queue()
    for v in range(1, n+1):
        Q.enqueue(v)
    while len(Q)>1:
        for i in range(1, k):
            Q.enqueue(Q.dequeue())
        Q.dequeue() #k번째 수 제거
    return Q.dequeue()
```

### **b. Dequeue**

왼쪽과 오른쪽에서 모두 삽입과 삭제가 가능한 큐

두 가지 버전의 pop과 push 연산을 구현

python collections 모듈에 deque 클래스로 구현되어 있음(덱으로 발음)

오른쪽 push : append / 왼쪽 push : appendleft

오른쪽 pop : pop / 왼쪽 pop : popleft
