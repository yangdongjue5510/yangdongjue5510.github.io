---
title: 깃 사용법(브랜치 활용 등)
date: 2021-12-12 22:35:00
tags:
    - git
---

# git을 잘 다루기

## 브랜치 생성, 브랜치 이동

```terminal
깃 초기화
git init

최초 커밋하기
git add .
git commit -m "first commit"

브랜치 생성하기
git branch issue1

브랜치 이동하기
git checkout issue1

브랜치 생성하고 이동하기
git checkout -b issue2
```

## 브랜치 합치기

```terminal
다른 브랜치와 합치기
(현재 위치에 다른 브랜치를 합침.)
(빨리 감기 병합.)
git merge issue1
```

## 브랜치 삭제하기

```terminal
git branch -d issue2
```

## 브랜치 병합 시 충돌 해결

issue1 브랜치에는 다음과 같이 text.txt가 커밋되어 있고.

```
hello
whats up
```

issue2 브랜치에는 다음과 같이 text.txt가 커밋 되어 있다고 하자.

```
hello 안녕
```

이 상태에서 두 브랜치를 병합하려하면 문제가 생긴다.

```terminal
현재 HEAD가 issue1에 있을 때를 가정.
git merge issue2
Auto-merging text.txt
CONFLICT (content): Merge conflict in text.txt
Automatic merge failed; fix conflicts and then commit the result.
```

그리고 text.txt를 보면 다음과 같다.

```
<<<<<<< HEAD
hello
whats up!~~~
=======
hello 하이
>>>>>>> issue2
```

이 충돌된 내용 ( <<<... , ===... , >>>...)을 잘 처리해주고 다시 커밋하면 된다.
다음과 같이 수정하고 커밋해보자.

```
hello
whats up!~~~
hello 하이
```

## rebase로 통합하기

rebase는 다른 브랜치의 내용을 모두 받아들이고, 자신의 모든 커밋을 그 다음에 이어서 진행하도록 하는 명령이다.

이전에 했던 merge와 다르게 다른 브랜치를 합치면 원래의 커밋기록이 변경된다.

```terminal
아까 했던걸 취소한다.
git reset --hard HEAD~

rebase로 통합해보자.
git rebase issue2

당연히 문제가 생긴다. (충돌)
First, rewinding head to replay your work on top of it...
Applying: dkdk
Using index info to reconstruct a base tree...
M       text.txt
Falling back to patching base and 3-way merge...
Auto-merging text.txt
CONFLICT (content): Merge conflict in text.txt
error: Failed to merge in the changes.
Patch failed at 0001 dkdk
hint: Use 'git am --show-current-patch' to see the failed patch
Resolve all conflicts manually, mark them as resolved with
"git add/rm <conflicted_files>", then run "git rebase --continue".
You can instead skip this commit: run "git rebase --skip".
To abort and get back to the state before "git rebase", run "git rebase --abort".
```

이전 챕터에서 했던 것처럼 다시 수정해서 커밋하면 충돌을 해결할 수 있다.

## cherry-pick 다른 브랜치의 특정 커밋만 가져오기 

```terminal
git cherry-pick [commit 아이디]
```

## git add, commit 취소하기

```terminal
add 취소하기.
git reset HEAD [FILE이름]

commit 취소하기.
1. commit을 취소하고, 파일들이 staged된 상태로 취소(add된 상태)
$ git reset --soft HEAD^

2. commit을 취소하고, 파일들은 unstaged 상태로 워킹 디렉터리에 보존(디폴트 값.)
$ git reset --mixed HEAD^
$ git reset HEAD^ // 위와 동일
$ git reset HEAD~2 // 마지막 2개의 commit을 취소

3. commit을 취소하고, 파일들은 unstaged 상태로 워킹 디렉터리에서 삭제
$ git reset --hard HEAD^
```

## commit message 수정

```terminal
git commit --amend
```

## fetch

fetch는 원격 저장소의 최신 내용을 새로운 브랜치로 가져온다.
이때 fetch의 브랜치는 이름없지만,  FETCH_HEAD라는 이름으로 checkout할 수 있다.

```terminal
git fetch [리모트이름] [브랜치이름]
```

## git ~와 ^

~는 동일한 브랜치의 상위 커밋을 표현하고자 할 때 쓴다.
HEAD~1은 HEAD에서 같은 브랜치의 1개 전 커밋을 표현하다.

^는 브랜치로 분기 될 때 상위 커밋을 표현하고자 할 때 쓴다.
HEAD^2는 HEAD에서 다른 브랜치의 상위 커밋을 표현한다.
