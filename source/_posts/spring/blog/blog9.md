---
title: 9. [블로그 프로젝트] update, delete 테스트
date: 2021-09-06 20:35:28
tags:
category:
    - Spring
    - Project
---
### update


#### 1. save 메소드를 활용한 update
```java
    @PutMapping("/dummy/user/{id}")
    public User updateUser(@PathVariable int id, @RequestBody User requestUser){
        System.out.println("id :"+id);
        System.out.println("username: "+requestUser.getUsername());
        System.out.println("password:"+requestUser.getPassword());
        System.out.println("email:"+requestUser.getEmail());
        User user = userRepository.findById(id).orElseThrow(()->{
            return new IllegalArgumentException("수정 실패하였습니다.");
        });
        user.setUsername(requestUser.getUsername());
        user.setPassword(requestUser.getPassword());
        user.setEmail(requestUser.getEmail());
        userRepository.save(user);
        return user;
    }
```
Body에서 json 객체를 가져오기 위한 @RequestBody 어노테이션을 사용해서 객체에 할당한다.


일단 save메소드의 특징을 알아보자.
객체를 DB에 저장할 때, 해당 id가 이미 있으면, 그 객체 그대로 update한다!
즉 일부 속성을 변경하려 하면, 해당 id에 맞는 객체를 DB에서 찾아오고, 그 객체 필드를 수정하고 save해야 올바른 업데이트다.*(이거 은근 귀찮다.)*


> save 메소드

id 없이 사용되면 insert
id를 넘겨주고, DB에 해당 데이터 있으면 update
id를 넘겨주고, DB에 해당 데이터 없으면 insert


#### 2. @Transactional으로 update(더티 체킹)
save하지 않아도 업데이트 된다. 객체를 찾아서 값만 변경하면 반영된다!
```java
    @Transactional
    @PutMapping("/dummy/user2/{id}")
    public User updateUser2(@PathVariable int id, @RequestBody User requestUser){
        System.out.println("id : "+id);
        System.out.println("username: "+requestUser.getUsername());
        System.out.println("password : "+requestUser.getPassword());
        System.out.println("email: "+requestUser.getEmail());

        User user = userRepository.findById(id).orElseThrow(()->{
            return new IllegalArgumentException("update fail!!!!!!!");
        });
        user.setUsername(requestUser.getUsername());
        user.setPassword(requestUser.getPassword());
        user.setEmail(requestUser.getEmail());
        return user;
    }
```
[더티체킹_좀더알기](https://yangdongjue5510.github.io/2021/09/06/Spring/boot/boot4/)

### delete
삭제는 참 쉽다!
```java
    //delete
    @DeleteMapping("/dummy/user/{id}")
    public String delete(@PathVariable int id){
        try{
            userRepository.deleteById(id);
        }catch(EmptyResultDataAccessException e){
            return "삭제 실패했습니다. 해당 id는 데이터베이스에 없습니다.";
        }

        return "삭제됐습니다! id: "+id;
    }
```