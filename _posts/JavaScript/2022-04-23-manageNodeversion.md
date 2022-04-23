---
title: JavaScript &#35;6
date: 2022-04-23 09:00
description: n을 활용한 nodeJs 버전관리
category: JavaScript
icon: js
tags: [JavaScript]
---

`n` 을 활용하면 여러 버전의 node.js와 npm을 관리할 수 있다. 

```
$ brew install n
```

```
$ sudo n

    node/10.16.0
    node/10.23.3
  o node/16.14.2

```

터미널에서 `n`을 입력하면 설치되어 있는 노드버전의 목록이 등장하고 이 중에서 선택하면 된다.

선택한 버전에 맞춰서 npm의 버전도 설정된다.

하지만 brew로 node를 인스톨할 경우 npm의 버전이 고정되는 이슈가 생겼다.

```
ERROR: npm is known not to run on Node.js v10.23.3
You'll need to upgrade to a newer Node.js version in order to use this
version of npm. You can find the latest version at https://nodejs.org/	
```

```
$ which npm

/opt/homebrew/bin/npm
```

`which npm`의 결과가 위와 같다면 아래와 같은 설정을 추가한다.

- Node 환경 변수 추가

```
$ vim ~/.zshrc

--- 다음을 추가
# Nodejs
$ export PATH=/usr/local/bin:$PATH
---
```

- 결과

![img1](/assets/images/post/img-2022-04-23-01.png)


