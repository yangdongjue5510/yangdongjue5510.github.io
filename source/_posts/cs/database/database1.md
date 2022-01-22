---
title: 1. MongoDB로 NoSQL 찍먹하기
date: 2021-09-09 20:05:01
tags:
category:
    - Computer Science
    - Database
---
#### 데이터베이스와 파일시스템의 가장 큰 차이는?
**자기기술성**
파일 시스템은 파일을 특정 응용프로그램이 있어야 열 수 있다.
db는 sql만 있으면 어떤 응용프로그램이든 열 수 있다.
즉 sql은 자기기술성을 가진다.


#### NoSQL
NoSQL = Not only SQL = 데이터 접근 방식이 sql말고 다른 거도 가능함
Document 타입 : key : value에서 value에 또다른 key value 집합이 들어올 수 있음.(중첩)
![](/img/database/database1-1.png)


확장 가능성이나 스키마 없는 데이터 모델에 유리
(데이터의 형태가 일정하면 RDB, 아니면 NoSQL)
join을 지원하지 않음(reference 등으로 구현)
트랜잭션이 없음.
수평확장에 용이

> NoSQL 사례

key: value 형식 - Redis
Document = MongoDB
Column = Cassandra


#### MongoDB
BSON(binary json)기반 key-value store
json 형태 문서
Collection -> Document -> key:value data


> MongoDB 사용하기(파이썬 기준)

pip install pymongo
pip install requests
pip install jupyter
[DB인스턴스생성]cloud.mongodb.com - db인스턴스생성


**DB연결**
```python
from pymongo import MongoClient
client = MongoClient(url, connect = False)
client.DB이름.list_collection_names()
```

**DB, collection 할당**
```python
db = client.{DB이름}
new_Collection = db.{CollectionName}
```


**INSERT data**
```python
data = {
    "key1" : "value1",
    "key2" : "value2",
}
new_Collection.insert_one{data}
#여러개를 넣으려면, insert_many.
```


**SELECT**
쿼리에 넣은 기준에 따라 데이터 찾음
```python
query = {"key1" : "value1"}
list(new_Collection.find_one(query))
#key1이 value1인 데이터를 찾음
```


**Operator**
key1 = value1 or key2 = value2인 경우..
```python
query = {
    $or : [
        {"key1" : "value1"},
        {"key2" : "value2"},
    ]
}
new_Collection.find(query)
```
![연산자](/img/database/database1-2.png)