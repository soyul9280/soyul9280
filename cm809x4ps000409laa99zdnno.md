---
title: "[ Spring ] IoC 와 DI"
datePublished: Sat May 10 2025 15:00:00 GMT+0000 (Coordinated Universal Time)
cuid: cm809x4ps000409laa99zdnno
slug: spring-ioc-di
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1741050873238/78237356-b6f9-4258-8e26-fa9f68aa10d5.png
tags: spring

---

> 목표 : IoC와 DI 개념 확인 및 간단한 예시 코드 확인
> 
> IoC와 DI를 이용한 게시판 예시 코드 : [https://github.com/soyul9280/crud\_board/tree/soyul](https://github.com/soyul9280/crud_board/tree/soyul) (2주차)

# 1️⃣ 개요

먼저 IoC와 DI를 알아보기 전에 ApplicationContext와 Bean에 대해 이해해야한다. 다음 글을 참고하자.

%[https://soyulia.hashnode.dev/spring-applicationcontext] 

DI는 IoC의 한 형태이다. Bean을 만들 때 IoC컨테이너는 의존성을 주입한다. IoC컨테이너는 org.springframework.beans, org.springframework.context 패키지를 기반으로 한다.

ApplicationContext는 다음과 같은 기능을 제공한다.

* AOP와 쉬운 통합
    
* 메시지 리소스 처리
    
* 이벤트 발행
    
* WebApplicationContext와 같이 계층별 컨테이너 제공
    

즉, BeanFactory는 구성 프레임워크와 기능을 제공하고 ApplicationContext는 엔터프라이즈 환경(대규모 데이터 처리와 트랜잭션이 동시에 여러 사용자로 부터 행해지는 큰 규모의 환경)에서 필요한 기능을 추가한다.

Bean과 여러 객체 사이 의존성은 설정 메타 데이터에 반영되어 컨테이너에 등록된다.

---

# 2️⃣ 컨테이너

컨테이너는 설정 메타데이터를 읽어 구성요소를 생성,설정,조립하는 방법을 가져온다. 이때, 설정 메타데이터는 애노테이션으로된 component클래스, 팩토리 메서드 설정클래스, 외부 XML파일, Groovy scripts로 제작할 수 있다. 이러한 방식으로 애플리케이션을 구성하고 구성 요소간 복잡한 의존성을 설정할 수 있다.

ApplicationContext인터페이스는 스프링IoC컨테이너를 대표한다. Bean의 생성, 설정, 조립을 담당한다. 이 인터페이스의 구현체는 여러가지가 존재하며 독립 실행형 애플리케이션에서 보통 AnnotationConfigApplicationContext혹은 ClassPathXmlApplicationContext인스턴스를 생성한다.

대부분 애플리케이션에서는 사용자가 스프링 IoC컨테이너의 인스턴스 생성 코드를 명시하지 않아도 된다. 예를 들어, 일반적인 웹 애플리케이션은 web.xml파일의 웹 디스크립터로 Ioc컨테이너를 설정한다. SpringBoot환경에서는 기본 설정 규칙에 따라 자동으로 설정된다.

다이어그램은 어떻게 스프링이 작동하는지 보여준다. 에플리케이션 클래스들은 먼저 설정 메타데이터와 결합된다. ApplicationContext가 만들어지고 초기화된 후, 완전히 설정되고 실행가능한 시스템이 된다.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1741139079358/50852cb4-f0c8-43d8-954a-cdfae7ada826.png align="center")

## Configuration Metadata

다이어그램에서 보듯이, 스프링 IoC컨테이너는 설정 메타데이터를 사용한다. 이는 어떻게 구성요소를 생성, 설정, 조립할지 알려준다. Bean을 어떻게 설정하는지 이전 글에서 살펴보았다.

스프링 설정에는 적어도 하나의 Bean이 정의되어있어야하고, 일반적으로 컨테이너를 관리하기 위해 여러개의 Bean이 정의되어있다. 자바 설정에서 일반적으로 @Configuration클래스 내 애노태이션이 적용된 @Bean은 하나의 Bean정의에 해당한다.

---

# 3️⃣ IoC란 ?

Inversion of Control은 프로그램의 일부 또는 객체의 제어를 컨테이너 혹은 프레임워크로 전환하는 소프트웨어 엔지니어링 원칙이다. 우리는 객체 지향 프로그래밍 맥락에서 자주 사용한다.

사용자 정의 코드는 라이브러리를 호출하는 전통적인 프로그래밍 방식과 달리, IoC는 프레임워크가 프로그램의 흐름을 제어하고 사용자 정의 코드를 호출할 수 있다. 이를 위해, 프레임워크는 추가 동작과 함께 추상화를 사용한다. 만약 행동을 추가하고 싶다면, 우리는 클래스를 확장하거나 자체클래스를 플러그인해야한다.

* 🙋🏻‍♀️ 플러그인 : 애플리케이션이 실행되는 도중에 새로운 기능을 추가하거나 제거할 수 있도록 하는 독립적인 코드 조각
    
* 🙋🏻‍♀️ 모듈 : 하나의 독립적인 기능을 수행하는 코드 단위
    

이 구조의 장점은

1. 구현에서 작업 실행을 분리한다.
    
2. 다른 구현체로 쉽게 전환할 수 있다.
    
3. 프로그램의 더 큰 모듈화 구현
    
4. 구성요소를 분리하거나 의존성을 mocking하여 프로그램 테스트가 쉽다.
    
5. 구성요소간 약속을 통해 상호작용 할 수 있다.
    

우리는 IoC를 전략패턴, Service Locator패턴, 팩토리 패턴, Dependency Injection와 같이 다양한 방식으로 이룰 수 있다.

---

# 4️⃣ DI란 ?

Dependency Injection은 IoC의 구현을 위해 사용하는 패턴이다. 여기서 제어가 역전되는 부분은 객체의 의존성을 설정하는 과정이다. 객체를 다른 객체와 연결하거나 다른 객체 내부에 주입하는 작업은 객체가 직접 하지않고, 객체 간의 관계를 외부에서 설정(주입)하는 방식을 사용한다.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1741167173021/65964ec0-7816-402e-8aeb-6034e35d58b5.png align="center")

위 예시는 전통적인 프로그래밍 방식으로 , Store클래스 내에서 Item인터페이스의 구현을 정의한다.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1741167278318/09e6bf38-150e-4f1e-8655-8c92af579192.png align="center")

DI를 사용함으로서, 우리가 원하는 Item의 구현을 지정하지 않을 수 있다.

IoC와 DI모두 간단한 개념이지만 우리가 시스템을 구성하는 방식에 큰 영향을 미치므로 완전히 이해해야한다.

---

# 5️⃣ DI 하는 방법

## `Constructor기반 DI`

객체를 생성할 때 필요한 구성요소를 생성자의 인수로 전달하는 방식이다. build.gradle에 spring-boot-starter-web을 추가해야한다.

컨테이너는 우리가 설정한 의존성을 나타내는 인수가 있는 생성자를 호출한다. 각 인수를 유형별, 속성이름, 인덱스(순서)기반으로 해결할 수 있다.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1741168636543/3c46d230-1df6-45c4-8fd0-6d1800880796.png align="center")

@Configuration은 이 클래스가 Bean정의하는 클래스임을 보여준다. 우리는 또한 다양한 설정 클래스를 추가할 수 있다. @Bean은 Bean을 정의하는 방법이다. 만약 이름을 정하지 않는다면 Bean의 이름은 메서드 이름으로 정해진다. 기본적인 싱글톤 범위에서 Spring은 Bean의 인스턴스가 이미 존재하는 경우인지 확인한다. 그리고 만약 존재하지 않는다면 새롭게 만든다.( 만약 프로토타입 범위를 사용한다면 그 컨테이너는 각 메서드 호출에 대한 새로운 Bean 인스턴스를 반환한다. -빈스코프)

XML으로 빈을 설정하는 방법은 다음과 같다.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1741176461024/5006ec70-e93d-4ec1-a8ed-7fccb3c2b854.png align="center")

좀 더 이해하기 쉽게 다른 예시도 함께 가져와봤다.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1741225186384/c0e99837-8290-4067-9f51-3c839f62252b.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1741225248459/03e63f15-8ea1-4d2f-afc1-214466c67c90.png align="center")

constructordi폴더를 스캔하여 추가적인 Bean을 찾는다. 이때 Component로 Car클래스를 찾는다. @Autowired로 생성자를 호출하여 인스턴스를 초기화한다. @Bean메서드를 호출하여 Engine과 Transmission인스턴스를 생성하게 된다.

생성자가 하나만 있는 클래스는 @Autowired를 생략할 수 있다. Spring 4.3부터 @Configuration클래스에도 생성자 기반 주입을 활용할 수 있다.

## 장점

필드주입과 비교하여 몇가지 장점이 있다. 먼저 테스트가 가능하다.

필드 주입으로 단위테스트한다고 가정하자.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1741225622523/fca5543c-1823-4b4d-907b-20b3899f489c.png align="center")

1. UserService객체를 생성하는 동안 우리는 UserRepository상태를 초기화 할 수 없다. 이를 해결하기 위해서 Reflection을 사용해야하지만 이는 캡슐화를 완전히 깬다.
    
2. 이 방식은 간단한 생성자 호출보다 안전성이 낮다.
    
3. 클래스 수준에서 불변성을 강제 할 수 없다. 따라서, 올바르게 초기화되지 않아서 userRepostiory 없이 userService가 생성되어 NullPointerException이 발생할 수 있다. 반면, 생성자를 이용하면 final을 사용할 수 있어 불변성을 보장할 수 있다.
    
4. 객체지향 프로그래밍 관점에서 생성자를 사용해서 객체 인스턴스를 만드는 것이 자연스럽다.
    

## 단점

단점은 Bean이 많은 의존성을 가질때 코드가 장황해질 수 있다.

## `Setter 기반 DI`

이 방법은 컨테이너가 매개변수가 없는 생성자 혹은 매개변수가 없는 정적 팩토리 메서드를 호출하여 Bean을 인스턴스 한 후에 setter메서드를 호출하여 의존성을 주입한다.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1741176874354/fe7f63b6-4198-407d-8874-49506c1b41d3.png align="center")

애노테이션 기반 설정 방법이다.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1741176894706/9d5111fc-31cd-4a1c-af4d-e2708c6093a9.png align="center")

XML기반 설정 방법이다.

같은 Bean에 대해 생성자 기반 그리고 setter기반 타입의 주입을 함께 사용할 수 있다. Spring공식문서에서는 필수적인 의존성에 생성자 기반 주입, 선택적인 의존성에는 setter기반 주입을 사용할 것을 권장한다.

## `Field 기반 DI`

@Autowired를 사용하여 의존성을 주입할 수 있다.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1741177101077/b2e3dcc2-69ac-4435-8b6a-78c6c4a32a06.png align="center")

Store객체가 생성하는 동안, Item Bean을 주입하기 위한 생성자 혹은 setter메서드가 없다면 컨테이너는 Store에 Item을 주입하기 위해 리플렉션을 사용할 것이다. 이 방법은 보기에 간단하고 깔끔해보이지만 몇가지 단점이 있어 추천하지 않는다.

1. 의존성을 주입할 때 리플렉션을 사용하는데 생성자 기반 혹은 setter기반 주입보다 비용이 많이 든다.
    
2. 여러 의존성들을 쉽게 추가할 수 있다. 결국 클래스가 복잡해진다.
    
    예를 들어 생성자 주입 방법으로 생각해보면, 생성자 매개변수가 많아져 해당 클래스가 여러가지 일을 수행한다는 생각을 들게 한다. 이는 단일 책임 원칙을 위반할 가능성이 있다.
    

---

# 6️⃣ **Autowiring Dependencies**

Wiring방식을 사용하면 컨테이너가 정의된 Bean을 검사하여 협력하는 Bean 간 의존성을 자동으로 해결해준다. XML방식으로 Bean을 자동으로 주입하는 4가지 모드가 잇다.

1. no : 기본 값, 우리가 명시적으로 의존성을 지정해야한다.
    
2. byName : 이름 기반 자동 주입, 속성이름으로 자동 주입한다. Spring은 설정해야하는 해당 속성과 동일한 이름을 가진 Bean을 찾는다.
    
3. byType : 타입기반 자동 주입, byname과 유사하지만 속성의 타입을 기준으로 자동 주입, 해당 속성과 같은 타입의 빈을 찾아 자동 주입, 같은 타입의 Bean이 2개 이상이면 예외 발생
    
4. 생성자 기반 자동 주입 : 생성자 매개변수 기준으로 자동 주입, 스프링은 생성자 매개 변수와 같은 타입의 Bean을 찾아 주입
    

예를 들어, item Bean을 store Bean에 by Type방식으로 자동 주입해보겠다.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1741178658881/2f304f98-5d7c-410b-963d-c90dca9f3fe9.png align="center")

현재 방법 Autowire속성은 Spring 5.1.기준으로 사용하지 않는다.

같은 타입의 빈이 2개 이상일 경우, 우리는 @Qualifier을 이용하여 이름으로 Bean을 참조할 수 있다.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1741178845185/9fa6998c-9668-452b-808e-f21b1c35f7bb.png align="center")

이제 XML기반으로 빈을 자동 주입해보자.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1741178901225/4fa114c4-7141-45d3-9c8f-bbee336e8f48.png align="center")

다음으로 이름으로 Store Bean의 item속성에 item이름으로 된 Bean을 주입하자.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1741178977081/0ea508b7-5bd9-4468-a05a-65e465701f47.png align="center")

---

## 👉🏻 IoC 장점

1. 의존성 주입 : 객체간의 의존성을 관리해주는 기능을 제공한다. (객체 자동 연결)
    
2. 관점 지향 프로그래밍 : 공통적인 기능을 한 곳에서 관리할 수 있게 해준다.
    
3. 다양한 모듈과 통합 : 다양한 모듈을 제공하여 원하는 기능을 쉽게 사용할 수 있다.
    
4. 테스트 용이성 : 쉽게 테스트 할 수 있다.
    

## 👉🏻 IoC 단점

1. 학습 곡선 : 배우는데 시간이 걸릴 수 있다.
    
2. 복잡성 : 다양한 기능으로 프로젝트가 복잡해질 수 있다.
    
3. 런타임 오버헤드 : 내부적으로 많은 일을 처리하므로 약간의 속도 저하가 발생할 수 있다.
    

### 출처

[https://www.baeldung.com/inversion-control-and-dependency-injection-in-spring](https://www.baeldung.com/inversion-control-and-dependency-injection-in-spring)

[https://www.baeldung.com/spring-application-context](https://www.baeldung.com/spring-application-context)

[https://www.baeldung.com/constructor-injection-in-spring](https://www.baeldung.com/constructor-injection-in-spring)

[https://docs.spring.io/spring-framework/reference/core/beans/basics.html](https://docs.spring.io/spring-framework/reference/core/beans/basics.html)

[https://docs.spring.io/spring-framework/reference/core/beans/introduction.html](https://docs.spring.io/spring-framework/reference/core/beans/introduction.html)