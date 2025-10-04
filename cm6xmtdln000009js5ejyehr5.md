---
title: "[Spring] Spring Framework란?"
datePublished: Sat Feb 22 2025 15:00:00 GMT+0000 (Coordinated Universal Time)
cuid: cm6xmtdln000009js5ejyehr5
slug: spring-spring-framework
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1739108899965/582f6e67-da48-409e-a3e7-351c0ea0b810.png
tags: spring

---

> 목표 : Spring Framework가 탄생하게 된 배경과 이를 통해 해결하고자 했던 문제점에 대해 확인하기.  
> 출처 : ***위니브 - 이승주 강사님 강의***

# 1️⃣ Spring ?

2000년대 초반에 자바 표준 기술 중 EJB를 많이 사용하였다. 하지만, 쓰기에 어렵고 복잡하고 느렸다. 컴포넌트 간 강한 결합으로 테스트하기 어렵고 코드의 재사용성도 낮았다. 이를 해결하기위해 Spring과 HIbernate가 탄생하였다.

초기 : IoC컨테이너와 AOP기능에 중점을 두었다.

성장과 발전 : Spring MVC모듈이 도입된다. XML 설정파일의 스키마가 개선된다. 개발자들 사이에서 큰 인기를 얻기 시작한다.

확산과 생태계 구축 : 애노테이션 기반 설정 도입, JAVA기능을 활용하기 시작한다. Spring Security, Spring Batch등의 프로젝트 시작→ 편의성, Spring과 통합될 수 있는 기술들의 출시로 확산됨

현대 Spring : Spring WebFlux도입, 이미지 지원 등 기능 추가

→ Spring Framework는 Java 애플리케이션 개발에 있어 표준으로 자리잡은 상태

---

# 2️⃣ 장점 & 단점

## 👉🏻 장점

1. 의존성 주입 : 객체간의 의존성을 관리해주는 기능을 제공한다. (객체 자동 연결)
    
2. 관점 지향 프로그래밍 : 공통적인 기능을 한 곳에서 관리할 수 있게 해준다.
    
3. 다양한 모듈과 통합 : 다양한 모듈을 제공하여 원하는 기능을 쉽게 사용할 수 있다.
    
4. 테스트 용이성 : 쉽게 테스트 할 수 있다.
    

## 👉🏻 단점

1. 학습 곡선 : 배우는데 시간이 걸릴 수 있다.
    
2. 복잡성 : 다양한 기능으로 프로젝트가 복잡해질 수 있다.
    
3. 런타임 오버헤드 : 내부적으로 많은 일을 처리하므로 약간의 속도 저하가 발생할 수 있다.
    

## ⚡ 정리

**Spring**  
EJB의 복잡성과 무거운 프로세스로 인한 어려움을 해결하기 위해 탄생하였다.