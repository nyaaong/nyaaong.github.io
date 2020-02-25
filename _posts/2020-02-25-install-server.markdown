---
layout: post
title:  "웹서버 만들기"
date:   2020-02-20 23:51:19 +0900
categories: centos
---
## 개인 웹서버 만들기

#### 개요
매번 구글 찾아가며 설치하기가 번거로워서 기록으로 남기게 되었다. CentOS8 의 minimal install을 기준으로 작성되었다.


1. yum의 저장소(repository) 변경  
    패키지들을 다운받을 때 국내 미러 서버를 이용하도록 변경한다.
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
 
    >[AppStream]  
    >name=CentOS-\$releasever - AppStream  
    >baseurl=http://mirror.kakao.com/\$contentdir/\$releasever/AppStream/\$basearch/os/  
    >gpgcheck=1  
    >enabled=1  
    >gpgkey=http://mirror.kakao.com/centos/RPM-GPG-KEY-CentOS-Official
    >
    >[BaseOS]  
    >name=CentOS-\$releasever - Base  
    >baseurl=http://mirror.kakao.com/\$contentdir/\$releasever/BaseOS/\$basearch/os/  
    >gpgcheck=1  
    >enabled=1  
    >gpgkey=http://mirror.kakao.com/centos/RPM-GPG-KEY-CentOS-Official
    >
    >[extras]  
    >name=CentOS-\$releasever - Extras  
    >baseurl=http://mirror.kakao.com/\$contentdir/\$releasever/extras/\$basearch/os/  
    >gpgcheck=1  
    >enabled=1  
    >gpgkey=http://mirror.kakao.com/centos/RPM-GPG-KEY-CentOS-Official
    
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
    >...  
    >NAME="eth0"  
    >DEVICE="eth0"  
    >...

    저장하고 재부팅한 다음 ```ip addr```로 제대로 바꼈는지 확인한다.


3. 유틸 설치하기  
    minimal install이어서 ```ifconfig```이나 ```netstat``` 같은 몇몇 명령어를 쓸 수 없다. 네트워크 장애가 의심될 때 원인 파악에 유용하게 사용할 수 있는 유틸을 몇개 설치한다.
    ```
    # dnf install net-tools traceroute telnet
    ```


4. git 설치하기
    현재 날짜(20.02.25)를 기준으로 yum repository에는 2.18.2 버전이 올라와있다. 최신 버전은 2.25.1이다. 편리하게 yum으로 설치할까 했지만, 어차피 컴파일해야할 프로그램이 몇 개 더있으므로 최신버전을 받아 컴파일한다.
    git 최신버전은 [여기(https://mirrors.edge.kernel.org/pub/software/scm/git/)](https://mirrors.edge.kernel.org/pub/software/scm/git/)에서 받을 수 있다.
    ```
    # mkdir /home/build
    # cd /home/build
    # curl -o git.2.25.1.tar.gz https://mirrors.edge.kernel.org/pub/software/scm/git/git-2.25.1.tar.gz
    # tar -zxvf git.2.25.1.tar.gz
    # cd git-2.25.1
    ```
    