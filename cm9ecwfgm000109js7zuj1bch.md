---
title: "[Docker] 컨테이너 vs 도커"
datePublished: Sat Jun 14 2025 15:00:00 GMT+0000 (Coordinated Universal Time)
cuid: cm9ecwfgm000109js7zuj1bch
slug: docker-vs
tags: docker

---

> 목표 : 컨테이너 기술과 Docker를 명확히 구분하여 설명하세요. 컨테이너 기술이 Docker 이전에도 존재했던 개념임을 언급하고, Docker가 컨테이너 기술을 구현한 하나의 도구라는 관점에서 설명해주세요. 또한, Docker 외에 컨테이너 기술을 구현한 다른 도구의 예시를 들어보세요.

## ❓1. 컨테이너 란?

컨테이너(Container) 기술은 실행 환경(라이브러리, 종속성 등)을 하나의 패키지로 묶어서, **어디서나 일관되게 실행할 수 있도록 지원하는 기술**입니다. 컨테이너라는 개념과 기술은 Docker 이전부터 존재했습니다.

* ex)`LXC (Linux Containers)`
    

## 🐳 2. Docker란?

Docker 는 이러한 컨테이너 기술을 **일반 개발자 및 엔지니어들이 쉽게 사용할 수 있게 도와주는 도구**입니다.

* Docker 는 **컨테이너 기술의 하나의 구현체**입니다.
    
* Docker 이전의 컨테이너 기술은 설정이 복잡하고 사용이 어려웠습니다.
    
* Docker 는 이를 **명령어 기반으로 간소화**하고,**이미지 관리**, **배포**, **버전 관리**, **네트워크 구성**, **스토리지 관리** 등을 쉽게 할 수 있도록 도와줍니다.
    
* Docker Hub 같은 이미지 저장소를 통해 **컨테이너 이미지 배포**가 매우 간편합니다.
    

## ❓+) Docker 외 다른 컨테이너 ?

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1744470813183/ccd83d66-38a0-467f-8b2a-25a7a23afc44.png align="center")

출처

[https://www.samsungsds.com/kr/insights/220222\_kubernetes1.html](https://www.samsungsds.com/kr/insights/220222_kubernetes1.html)