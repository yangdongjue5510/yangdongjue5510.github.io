---
title: 2. 배열과리스트
date: 2021-07-28 19:59:57
tags:
category:
    - Computer Science
    - Data Structure
---
### **a. 배열(array)**

데이터를 연속적인 메모리 공간에 저장

주소를 통해 매우 빠르게 접근

배열의 시작 주소,  저장된 값의 종류, 인덱스 세가지 정보로 원하는 값의 주소를 계산 가능.

읽기와 쓰기 연산에 O(1)시간이 걸림

고정된 길이를 가지며, 읽기와 쓰기만 지원하는 경우가 많다.

### **b. 리스트 (파이썬)**

c의 배열에는 실제 데이터가 저장된 형식이지만,

python의 리스트에는 데이터가 저장된 주소가 저장된다.

항상 객체의 주소만 저장하기 때문에 셀의 크기를 8바이트(혹은 4바이트)로 고정.

모든 셀의 크기가 같기 때문에 index에 의해 O(1)시간 접근이 가능

읽기/쓰기 외에 여러 연산들 지원

<table style="border-collapse: collapse; width: 100%; height: 160px;" border="1" data-ke-align="alignLeft" data-ke-style="style4"><tbody><tr style="height: 20px;"><td style="width: 50%; height: 20px;">a.append(val)</td><td style="width: 50%; height: 20px;">맨 뒤에 val 삽입</td></tr><tr style="height: 40px;"><td style="width: 50%; height: 40px;">a.pop(i)</td><td style="width: 50%; height: 40px;">a[i]값을 지운 후 리턴<br>pop()은 가장 오른쪽 값 지움</td></tr><tr style="height: 20px;"><td style="width: 50%; height: 20px;">a.insert(i, val)</td><td style="width: 50%; height: 20px;">a[i]=value연산(원래 값들은 한 칸씩 이동)</td></tr><tr style="height: 20px;"><td style="width: 50%; height: 20px;">a.remove(val)</td><td style="width: 50%; height: 20px;">val을 찾아 제거</td></tr><tr style="height: 20px;"><td style="width: 50%; height: 20px;">a.index(val)</td><td style="width: 50%; height: 20px;">val이 처음 등장하는 index 리턴</td></tr><tr style="height: 20px;"><td style="width: 50%; height: 20px;">a.count(val)</td><td style="width: 50%; height: 20px;">val이 몇 번 등장하는지 리턴</td></tr><tr style="height: 20px;"><td style="width: 50%; height: 20px;">a[i:j]</td><td style="width: 50%; height: 20px;">a[i]...a[j-1]까지 복사해 새 리스트로 반환</td></tr></tbody></table>

#### 리스트는 동적배열(파이썬)

append나 insert로 메모리가 부족해지면, 더 큰 메모리를 할당받아 새로운 리스트에 이전 리스트의 값을 모두 이동한다.

반면 pop이나 remove로 메모리가 너무 널널해지면, 더 작은 메모리에 이전 리스트를 할당한다.

따라서 사용자가 배열의 크기를 신경쓰지 않아도 된다.
