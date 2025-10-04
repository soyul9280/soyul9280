---
title: "[ Spring ] AOP"
datePublished: Sat Mar 22 2025 15:00:00 GMT+0000 (Coordinated Universal Time)
cuid: cm7hisegc000509l58zk8cwuj
slug: spring-aop
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1740306156436/314e0634-e0fe-482d-8d01-630a24bd4fe9.png
tags: spring-aop

---

> 목표 : Spring에서 AOP(Aspect Oriented Programming)가 필요한 이유, 이를 활용한 실제 애플리케이션 개발 사례

# 1️⃣ 애플리케이션 로직

애플리케이션 로직은 핵심 기능과 부가 기능으로 나뉘어져있다.

1. 핵심 기능 : 고유기능 (ex) 주문 로직
    
2. 부가 기능 : 보조기능, 단독으로 사용되지 않고 핵심기능과 같이 쓰인다. (ex) 로그 추적 기능 (어떤 핵심 기능이 호출됐는지 로그를 남기기 위해 사용)
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740306381929/1f95a313-800a-458d-912a-2e878fadd611.png align="center")

만약 , 같은 부가 기능이 여러 곳에서 동일하게 사용하는 횡단 관심사(cross-cutting concerns)가 발생한다면

1. 부가 기능을 적용할 때마다 코드를 반복해야한다.
    
2. 변경할때 많은 수정이 필요하다.
    
3. 적용 대상을 전체에서 OrderService만 적용한다면? 많은 수정이 필요하다.
    

이를 OOP방식으로 해결하기 어려워서 나타난 개념이 바로 AOP이다.

# 2️⃣ AOP란?

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740306830147/62efa8bd-e3f3-4a04-903c-5f6f4d23ab91.png align="center")

다음과 같이 부가 기능을 핵심 기능에서 분리하였다. 이 부가 기능을 어디에 적용할지 선택하는 기능을 모듈화 한것이 Aspect라고 한다.

Aspect를 사용해서 애플리케이션을 바라보는 관점을 하나의 기능에서 횡단관심사 관점으로 보는 프로그래밍 방식을 관점 지향 프로그래밍 AOP라고 한다. OOP대체가 아닌 횡단관심사를 처리하기 위한 보조 목적으로 개발되었다.

## AspectJ

이를 구현한 구현체로 AspectJ가 있다.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740307055127/8ec6c494-0377-42f6-90f9-d9b7686d515d.png align="center")

AOP는 생성자, static메서드 접근, 필드값 접근, 메서드 실행 지점에 적용할 수 있는데 이렇게 적용할 수있는 지점들을 Join Point라고 한다.

AspectJ는

1. 모든 Join Point에 AOP를 적용할 수 있다.
    
2. CTW,LTW,RTW방식을 사용할 수 있다.
    
3. 디자인패턴이 필요 없다.
    
4. AspectJ컴파일러로 AOP를 구현한다.
    
5. 완전한 AOP를 제공하기 위한 목적을 가지고 있다.
    

## Weaving

AOP를 사용할 때 부가 기능을 실제 로직에 추가한다는 뜻으로 위빙 단어를 사용한다.

1. Compile-Time Weaving 컴파일 시점
    
    컴파일러가 소스코드를 .class로 만드는 시점에 부가 기능을 추가한다. 이때 AspectJ컴파일러를 사용하는데 Aspect로 해당클래스가 적용대상인지 먼저 확인 후 핵심기능이 있는 컴파일된 코드 주변에 부가기능을 작성한다.
    
2. Load-Time Weaving 클래스 로드 시점
    
    .class파일을 JVM내부 클래스로더에 보관하기 전 조작한다. 이때, 클래스 로딩 과정에 참여하기 위해 JavaAgent옵션을 사용해야한다.
    
3. Run-Time Weaving 런타임 시점
    
    컴파일도 완료하고 클래스 로더에 클래스도 다 올라간 후인 메인 메서드가 실행된 다음에 조작한다. 프록시를 통해 부가 기능을 적용하는데 여기서 프록시는 메서드 오버라이딩 개념으로 동작한다. 따라서, Join Point는 메서드 실행에만 제한한다.
    

## Proxy

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740307659185/cbbfeb33-ddc2-4136-bc44-a3e3720cb8ce.png align="center")

프록시는 핵심 기능을 호출하고 부가 기능을 처리해주는 객체이다. 간접적으로 참조하기 때문에 부가기능을 처리할 수 있다. 부가기능을 처리하기 위해 매번 프록시를 생성해야하는 단점이 존재한다.

## Spring AOP

이를 해결하기 위해 Spring AOP가 등장한다.

1. RTW를 사용하기 때문에 프록시 기반 AOP이다.
    
2. Join Point는 메서드 실행으로만 제한된다.
    
3. 프록시 객체를 자동으로 생성해줌으로서 프록시 생성 중복코드를 해결해준다.
    
4. Spring IoC로 간단한 AOP구현이 목적이다.
    

## 용어

1. Advice : 부가기능 자체
    
2. JoinPoint: Advice가 적용될 위치, AOP가 적용될 수 있는 모든 지점
    
3. Pointcut: Joinpoint 중 Advice가 적용될 위치를 선별
    
4. Target: Advice를 받는 객체
    
5. Aspect : Advice + Pointcut
    
6. Advisor : 1개의 Advice + 1개의 Pointcut (Spring Aop에서 사용)
    
7. Weaving : Pointcut으로 결정한 Target의 JoinPoint에 Advice적용
    
8. Aop프록시 : AOP 기능을 구현하기 위해 만든 프록시 객체
    

# 3️⃣ 사례

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740308265019/ab9f00d9-9867-4877-b26a-32e44828b4e5.png align="center")

Repository안 메서드 실행전에 로깅을 남기는 Advice를 정의하였다.

## ⚡ 정리

**AOP : 횡단관심사 관점으로 애플리케이션을 바라본다.**

단점으로 , 만약 오류가 발생한다면 어디서 발생했는지 확인하기 힘들다.

출처

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740308547909/c0ebcdd7-2d46-4a4d-acec-9bfd5ac979c4.png align="center")