---
title: "[ Spring ] ApplicationContext"
datePublished: Sat May 03 2025 15:00:00 GMT+0000 (Coordinated Universal Time)
cuid: cm7wny9qq000009ju6wwsce52
slug: spring-applicationcontext
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1741179190434/c47883f3-6c79-48f1-ac9e-563a9e00dc82.png
tags: spring, applicaitoncontext

---

> 목표 : IoC를 이해하기 위해 ApplicationContext에 대한 개념 확인하기
> 
> 해당 개념 적용한 예시 : [https://github.com/soyul9280/crud\_board/tree/soyul](https://github.com/soyul9280/crud_board/tree/soyul) (2주차)

BeanFactory와 ApplicationContext는 Spring IoC 컨테이너를 나타낸다. BeanFactory는 최상위 인터페이스이며 Bean을 조회하고 관리하기 위한 기본적인 기능을 제공한다. 반면에 ApplicationContext는 BeanFactory의 하위 인터페이스이다. 따라서, BeanFactory의 모든 기능을 사용할 수 있다. 또한, 엔터프라이즈 환경에 특화된 기능을 제공한다. 설정 메타데이터 클래스에 정의된 Bean들을 초기화하고 의존성을 설정하고 lifecycle을 관리한다.

ApplicationContext은 다음과 같은 <mark>중요한 기능</mark>을 제공한다.

1. 메세지 해석
    
2. 국제화 지원
    
3. 이벤트 발행
    
4. 애플리케이션 계층에 특화된 컨텍스트 제공
    

이러한 이유로 기본적인 Spring컨테이너로 불리고 있다.

---

## ❓Bean

Spring컨테이너에 등록된 객체이다. Bean은 스프링 컨테이너가 생성하고 조립하고 관리한다. 하지만 모든 객체를 Bean으로 설정하는것은 좋지 않다. Spring공식문서에 따르면, 우리는 다음과 같은 객체들을 Bean으로 정의한다.

1. 서비스 계층 객체
    
2. 데이터 접근 객체
    
3. 프레젠테이션 객체 : 클라이언트의 요청을 받아 처리하고 응답을 반환하는 객체
    
4. SessionFactory,JMS 등 같은 인프라 객체 : 애플리케이션의 핵심 로직을 지원하는 객체
    

또한, 일반적으로 컨테이너에서 단순히 데이터만 가지는 객체를 설정해서는 안된다. 컨테이너는 공유되는 객체를 관리하기 위한 것이며 매번 새롭게 생성되어야 하는 데이터 객체를 관리하기에는 부적절하다. 데이터 객체의 생성과 로드는 DAO(데이터베이스와 상호작용하는 객체)와 비즈니스 로직을 담당하는 도메인 객체의 책임이다.

---

## ❓Bean 설정 방법

ApplicationContext의 주요 업무는 Bean을 관리하는 것이다. Spring에서 Bean 설정 파일은 하나 이상의 Bean정의로 구성되어있다. 그리고 컨테이너는 이 메타 데이터를 읽어서 런타임 시점에 Bean을 조립하는데 사용한다.

1. 애노테이션 기반 Java 설정 방식
    

가장 선호되는 방법이다. Spring 3.0 부터 사용가능하다. @Configuration클래스는 설정 파일임을 나타낸다. 이 클래스 안에 메서드 위 @Bean을 적으면 해당 메서드를 호출하면서 반환된 객체를 스프링 컨테이너에 등록한다.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1741181854660/9ad6e9d8-bc95-4c66-ac15-c5fd543fca51.png align="center")

2. XML 설정 방식
    
    해당 글에서는 생략하겠다.
    

각각의 메서드에서 메타 정보가 생성된다. 이 메타 정보를 기반으로 Bean을 생성하는데 이때 @Bean, &lt;bean&gt;처럼 다양한 방식이 있기 때문에 BeanDefinition을 인터페이스로 추상화 하였다. 이 인터페이스의 구현체가 xxxCofig.class, xxxXmlConfig.class이다.

### BeanDefinition

1. BeanClassName : 빈 클래스 명
    
2. factoryBeanName : 팩토리 역할 빈 사용할 경우 이름 ex)Appconfig
    
3. factoryMethodName : 빈을 생성할 팩토리 메서드 지정
    
4. Scope
    
5. lazyInit
    
6. InitMethodName
    
7. DestroyMethodName
    
8. Constructor arguments, Properties : 의존관계 주입에서 사용
    

---

## ❓ApplicationContext 의 다양한 구현체들

스프링은 다양한 요구사항에 적합한 ApplicationContext컨테이너를 구현체로서 제공한다.

1. AnnotationConfigApplicationContext
    
    Spring 3.0에서 소개되었다. @Component,@Configuration이 적용된 클래스 및 JSR-330 메타데이터를 입력으로 사용할수있다.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1741220905902/d3d2a384-537b-4f7a-98cb-deffcd3be46e.png align="center")

2. AnnotationConfigWebApplicationContext
    
    Web 기반 AnnotationConfigApplicationContext이다. ContextLoaderListener서블릿리스너 혹은 DispatcherServlet을 설정할 때 사용한다. Spring 3.0이후부터 WebApplicationInitializer인터페이스를 구현해서 설정할 수 있다.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1741221213734/97faba39-2fb5-42e3-a6ff-852cf0e2dcc0.png align="center")
    
3. XmlWebApplicationContext
    
    만약 XML기반 설정방식을 사용한다면 XmlWebApplicationContext클래스를 사용한다. 설정 방식은 AnnotationConfigWebApplicationContext와 동일하다. WebApplicationInitializer인터페이스를 구현하여 web.xml 설정 정보를 사용할 수 있다.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1741221433837/aafd3fba-522e-4e32-a294-c71bfd2612b0.png align="center")
    
4. FileSystemXMLApplicationContext
    
    URL혹은 파일시스템으로 부터 XML기반 설정파일을 로드할때 이 클래스를 사용한다. 예를 들어,Test Harness(특정 기능 테스트를 위한 환경) 및 독립실행형 애플리케이션(단독 실행 가능한 프로그램)에서 사용한다.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1741221646595/54bff822-d48d-4300-8e6a-c4ee3f6b00b7.png align="center")
    
5. ClassPathXmlApplicationContext
    
    XML설정 파일을 Classpath로 불러오고 싶을 때 사용한다. 4번 방식과 비슷하게 Test Harness, JAR파일에 포함된 ApplicationContext에서도 유용하다.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1741221862653/aeb5942e-a58b-4099-bd45-35686cef338d.png align="center")
    

---

## ❓1개의 애플리케이션 내 여러 컨테이너 사용

하나의 애플리케이션 내 여러 ApplicationContext의 인스턴스가 필요한 경우도 있다.

1. Modular Applications
    
    대규모 modular application에서 각 모듈마다 자신의 ApplicaitonContext를 가진다. 이는 각 모듈의 독립적인 설정, Bean이름 충돌을 막으며 유지보수가 쉽도록 해준다. 모듈형 애플리케이션은 각 모듈이 자신의 ApplicaitonContext를 로드하여 Bean을 독립적으로 관리하는데 다른 모듈의 컨테이너에 간섭하지 않는다.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1741222805668/af9c0e31-5b2a-403f-907f-fd45908ade19.png align="center")
    
2. Hierarchical Application Contexts
    
    Spring은 계층적인 컨테이너를 지원하며 부모 컨테이너에서 정의한 Bean은 자식 컨테이너에서도 접근 가능하다. 자식 컨테이너는 특정 모듈에만 사용하는 Bean을 추가로 설정할 수 있다. 이는 부모 컨테이너에서 공통 설정을 정의하고 자식 컨테이너에서 공유하는 경우 유용하다.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1741223063144/d2e572aa-bf3a-4170-88dc-e19719f68aae.png align="center")
    
    예를 들어 부모 컨테이너 먼저 로드한다. 자식 컨테이너는 setParent()메서드를 사용해서 부모 컨테이너와 연결된다. 부모 컨테이너에서 정의된 Bean은 childContext에서도 사용가능하다.
    
3. Isolation for Testing
    
    애플리케이션의 서로 다른 부분을 테스트하기 위해 다양한 ApplicationContext인스턴스를 사용할 수있다. 한 부분을 테스트할 때 다른 부분에 영향주지 않도록 한다. 단위 혹은 통합 테스트에서 각 테스트 시나리오에 맞는 특정 컨테이너를 만들 수 있다.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1741223336730/b9d99112-7266-4928-882a-76011474b61e.png align="center")
    

---

## ❓ApplicationContext 의 추가 기능

1. Message Resolution
    
    ApplicationContext인터페이스는 MessageSource인터페이스를 이용해서 메시지 해석, 국제화를 지원한다. Spring은 MessageSource구현체로 ResourceBundleMessageSource와 StaticMessageSource를 제공한다.
    
    * StaticMessageSource : 메시지를 프로그래밍방식으로 추가할 수 있지만 기본적인 국제화 기능만 지원하고 production환경보다 테스트 환경에 적합하다.
        
    * ResourceBundleMessageSource : 일반적으로 사용한다. JDK의 ResourceBundle 구현을 기반으로 하고 JDK의 MessageFormat이 제공하는 표준 메세지 파싱기능을 사용한다.
        
        ### 🙋🏻‍♀️ JDK ?
        
    * JDK(Java Development Kit) : **Java 애플리케이션을 개발하고 실행하는 데 필요한 도구 모음**
        
    * JRE(Java Runtime Environment) + 개발 도구(컴파일러, 디버거, 실행기 등)
        
    
    MessageSource로 properties파일에서 메시지를 읽는 방법은 다음과 같다. 먼저 messages.properties파일을 classpath에 만든다. 그리고 AccountConfig클래스에 Bean을 정의한다
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1741223840540/557d4f17-14da-4f3d-91da-0273ae867766.png align="center")
    
    그리고 AccountService에 MessageSource를 주입한다.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1741223874272/883eb785-68ad-4c2d-9957-5c8f002085be.png align="center")
    
    마지막으로 getMessage메서드를 AccountService 어디에서든 사용해서 메시지를 읽는다.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1741223907079/290ef2ea-8ef0-4321-ba36-bf794ca43ae4.png align="center")
    
    스프링은 또한 ReloadableResourceBundleMessageSource클래스를 제공하는데 이는 리소스 위치에서 파일을 읽을 수 있고 bundle 속성파일의 변경사항을 감지하여 자동으로 반영(hot reloading)할 수 있다.
    
2. Event Handling
    
    ApplicationEvent클래스 그리고 ApplicationListener인터페이스의 도움으로 이벤트를 다룬다. ContextStartedEvent,ContextStoppedEvent,ContextClosedEvent,RequestHandledEvnet 처럼 built-in events를 지원한다. 비즈니스 사용을 위해 사용자 제작 이벤트도 지원한다.
    

출처:[https://www.baeldung.com/spring-application-context](https://www.baeldung.com/spring-application-context)