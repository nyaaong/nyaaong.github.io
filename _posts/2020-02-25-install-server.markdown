---
layout: post
title:  "웹서버 만들기"
date:   2020-02-20 23:51:19 +0900
categories: rrd
---
## 개인 웹서버 만들기

#### 개요
매번 구글 찾아가며 설치하기가 번거로워서 기록으로 남김
CentOS8 의 minimal install을 기준으로 작성되었다.

1. yum의 저장소(repository) 변경
패키지들을 다운받을 때 국내 미러 서버를 이용하도록 변경

기존 저장소들을 압축해서 백업하려니 minimal install이라서 tar가 없다. 우선 tar부터 설치한다.
```
# dnf install tar
```

설치를 끝마쳤으면 yum 저장소들을 명시한 파일들이 있는 폴더로 이동하고 백업부터 한다.
```
# cd /etc/yum.repos.d/
# tar -zcvf original_repos.tar.gz ./*
```

백업(압축)이 완료 되었다면 기존의 파일을 모두 날리고
```
# rm -f ./*.repo
```

국내 미러 사이트들로 새 파일을 작성한다.
```
# vi CentOS.repo
```

```
[AppStream]
name=CentOS-$releasever - AppStream
baseurl=http://mirror.kakao.com/$contentdir/$releasever/AppStream/$basearch/os/
gpgcheck=1
enabled=1
gpgkey=http://mirror.kakao.com/centos/RPM-GPG-KEY-CentOS-Official

[BaseOS]
name=CentOS-$releasever - Base
baseurl=http://mirror.kakao.com/$contentdir/$releasever/BaseOS/$basearch/os/
gpgcheck=1
enabled=1
gpgkey=http://mirror.kakao.com/centos/RPM-GPG-KEY-CentOS-Official


[extras]
name=CentOS-$releasever - Extras
baseurl=http://mirror.kakao.com/$contentdir/$releasever/extras/$basearch/os/
gpgcheck=1
enabled=1
gpgkey=http://mirror.kakao.com/centos/RPM-GPG-KEY-CentOS-Official
```

업데이트를 하면 1차 작업 끝.
```
# dnf update
```

2. 네트워크 인터페이스 이름 변경하기
설치 과정에서 네트워크 인터페이스의 이름 변경이 안되어서 설치 후 진행하기로 했다. 기존의 이름 그대로 쓴다고 문제가 생기는건 아닌데, 종종 네트워크 인터페이스 이름을 사용할 일이 있으므로 간단한게 좋다.
```
# cd /etc/sysconfig/network-scripts/
```
이름을 바꿀 인터페이스 파일 이름부터 변경하고, 에디터로 열어 name과 device 를 수정해준다.
```
# mv ifcfg-enpxxxx ifcfg-eth0
# vi ifcfg-eth0
```
```
...
NAME="eth0"
DEVICE="eth0"
...
```
저장하고 재부팅한 다음 ip addr로 제대로 바꼈는지 확인한다.

3. net-tools 설치하기
minimal install이어서 몇몇 명령어가 없다. 그래서 우선 net-tools부터 설치한다.
```
# dnf install net-tools
```


