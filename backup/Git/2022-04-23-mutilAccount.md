---
title: Git &#35;1
date: 2022-04-23 10:00
description: Github 여러 계정을 한 컴퓨터에서 사용하기
category: Github
icon: git
tags: [Git,Github]
---

회사에서 새로운 깃허브계정과 Mac을 발급받았다. 

이전에 쓰던 개인 Github 계정을 회사 계정과 동시에 사용하기 위해서 아래의 설정이 필요하다.

1. SSH key-gen 생성

```
$ ssh-keygen -t rsa -b 4096 -C "account@email.com" (Github 이메일 입력)
```

입력하면 키페어 저장 위치를 묻는다.

```
Generating public/private rsa key pair.
Enter file in which to save the key (/${userPath}/id_rsa):
```

여기서 바로 엔터를 입력하면 id_rsa 라는 이름으로 생성이 되므로, 구별될 수 있는 이름을 입력한다.

```
Enter file in which to save the key (/${userPath}/id_rsa): id_rsa_personal
```

다음은 비밀번호를 입력하라고 나오는데

```
Enter passphrase (empty for no passphrase): 
```

비밀번호를 입력한다면 매번 커밋할때마다 비밀번호를 입력해야하므로 엔터를 쳐서 넘어간다.

2. SSh key 등록

```
$ ssh-add ~/.ssh/id_rsa_personal
```

위 명령어를 통해 로컬에 키를 등록한다.

그 다음 깃허브에 추가한다.

```
$ cat id_rsa_personal.pub
ssh-rsa ..... account@email.com
```

ssh-rsa부터 끝까지 모두 복사해서 등록한다.

3. config 파일로 다수의 계정을 관리

`config` 파일을 만들고 관리할 계정을 추가한다.

```
touch ~/.ssh/config

vi ~/.ssh/config

#  github personal
#  -------------------
Host github.com-personal
  HostName github.com
  IdentityFile ~/.ssh/id_rsa_personal
  User zerochae_personal

# github office
#  -------------------
Host github.com-office
  HostName github.com
  IdentityFile ~/.ssh/id_rsa_office
  User zerochae_office

```

4. git 레포 remote 설정

github SSH 주소를 보면 `git@github.com:account/repo.git` 형식으로 되어있다.

여기서 `:` 앞 부분을 위에서 설정한 Host로 변경해야 한다.

```
git remote set-url origin git@github.com-personal:account/repo.git
```

5. 레포 계정 격리

이제 해당 레포는 해당 호스트의 키를 가져올 것이므로 글로벌 계정과 충돌 되지 않게 `local` 설정을 한다.

```
git config --local user.name "Your Name"
git config --local user.email "Your Email"
```

이제 push 하면 정상적으로 된다.

![img1](/assets/images/post/img-2022-04-23-02.png "3번째 테스트에 성공했다.")