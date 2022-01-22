---
title: 운영체제 스터디 및 실습! 1주차!
date: 2021-11-09 06:50:37
tags:
category:
    - Java
---

# 시스템콜이란 무엇일까?

위키백과에 따르면,

운영 체제의 커널이 제공하는 서비스를 응용 프로그램의 요청에 따라 커널에 접근하기 위한 인터페이스를 의미한다.
쉽게 풀어쓰면 응용 프로그램에서 커널의 서비스를 사용하는 방법이 시스템콜이다.

운영체제의 커널이 제공하는 함수를 어플리케이션이 요청에 따라 적절한 커널 함수를 실행시켜주는 역할을 한다.
어플리케이션은 커널 자원에 맘대로 접근하면 안되니, 시스템콜을 호출해서 커널 함수를 호출시키고, 커널 함수로 자원을 요청할 수 있다.



## 시스템콜 분석하기

커널 소스를 분석하려면 원래 vim에 ctags와 cscope를 붙여서 분석하는게 정석이라고 한다.

하지면 이번엔 간단하게 다음 사이트에서 리눅스 소스코드를 확인해보자

[Linux sorce code](https://elixir.bootlin.com/linux/latest/source)

이 사이트를 들어가보면 여러 폴더들이 있는데 폴더의미는 다음과 같다.

Documentation : 리눅스 커널 내용 문서화
Licenses : 라이센스 관련 내용
Arch : 아키텍처에 종속된 내용
Block : 블록 IO 처리 관련 내용
Certs : 인증관련 내용
Crypto : 암호화 관련 내용
Drivers : 디바이스 드라이버 관련 내용
fs : 파일시스템 관련 내용
Include : 커널 헤더 관련 내용
Init : 부팅 과정에서 사용되는 초기화 내용
ipc : ipc관련 내용 (메시지큐, 세마포어, 쉐어메모리 등..)
kernel : 중요 커널 내용(fork, exec, exit, ire, DMA...)
Lib : 커널에서 사용되는 라이브러리(압축이나 자료구조 관련.)
Mm: 메모리 관리 관련 내용
Net : 네트워크 스택 관련 내용
Samples : 커널 기술을 응용한 샘플
Scripts : 커널 개발에 사용하는 스크립트
Security : 보안관련 내용
Sound :  소리 출력 관련
tools : 커널 검증 도구 관련
virt : kvm 가상화 관련 내용

**물론 이걸 굳이 모두 세세히 알 필요는 없다!**

예를 들어서 x86 계열의 시스템콜은 **/arch/x86/entry/syscalls/**에서 볼 수 있다.

```ass
#
# 64-bit system call numbers and entry vectors
#
# The format is:
# <number> <abi> <name> <entry point>
#
# The __x64_sys_*() stubs are created on-the-fly for sys_*() system calls
#
# The abi is "common", "64" or "x32" for this file.
#
0	common	read			sys_read
1	common	write			sys_write
2	common	open			sys_open
3	common	close			sys_close
4	common	stat			sys_newstat
5	common	fstat			sys_newfstat
6	common	lstat			sys_newlstat
7	common	poll			sys_poll
8	common	lseek			sys_lseek
9	common	mmap			sys_mmap
10	common	mprotect		sys_mprotect
11	common	munmap			sys_munmap
12	common	brk			sys_brk
```

여기서 재밌는건 예를 들어 read 시스템콜을 호출하면 커널 함수 sys_read가 매핑된다.

```c
asmlinkage long sys_read(unsigned int fd, char __user *buf, size_t count);
asmlinkage long sys_write(unsigned int fd, const char __user *buf,
			  size_t count);
asmlinkage long sys_readv(unsigned long fd,
			  const struct iovec __user *vec,
			  unsigned long vlen);
asmlinkage long sys_writev(unsigned long fd,
			   const struct iovec __user *vec,
			   unsigned long vlen);
```

Sys_read(...)는 프로토타입으로 나와있는데 이건 커널함수가 독특한 정의 방식을 따르기 때문이다.



### SYSCALL_DEFINEn 정의방식

n은 자연수라고 할때,

SYSCALL_DEFINE1 , SYSCALL_DEFINE2... 이런식으로 표현가능하다.

이때 n은 해당 함수의 함수 갯수를 의미한다.

다시 sys_read의 경우를 보자.

```c
asmlinkage long sys_read(unsigned int fd, char __user *buf, size_t count);
```

이렇게 프로토타입이 선언되어 있으면,

다음과 같다고 봐야한다.

```c
sys_read(read, unsigned int fd, char _user *buf, size_t, count){
	....
}
```

결국 do_sys_read()함수를 호출해서 read루틴을 처리한다.



### 다양한 시스템콜 분석해보기

