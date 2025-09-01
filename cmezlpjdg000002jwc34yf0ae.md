---
title: "[Spring] @Valid와 @Validated"
datePublished: Tue Jun 24 2025 15:00:00 GMT+0000 (Coordinated Universal Time)
cuid: cmezlpjdg000002jwc34yf0ae
slug: spring-valid-validated
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1756637563515/85df09f4-09d3-410f-b57c-eac8eff55101.png
tags: spring, validation, valid

---

## ✍️ 작성하게 된 이유

Spring 프로젝트를 개발하면서 사용자 입력을 **검증(Validation)** 하는 건 너무나도 중요한 작업입니다. 특히 DTO 객체의 필드에 `@NotBlank`, `@Email` 같은 어노테이션을 붙이면 아주 간단하게 유효성 검증이 가능하다는 점에서 `@Valid`, `@Validated`를 많이 사용하게 됩니다.

하지만 두 어노테이션의 차이를 잘 모르고 쓰면, **검증이 되지 않거나 예외가 예상과 다르게 발생**할 수 있습니다.

이번 글에서는 `@Valid`와 `@Validated`의 개념, 차이점, 작동 방식까지 정리해보겠습니다.

---

## 📚 `@Valid`란?

> JSR-303(자바 진영 표준) 기반의 객체의 제약 조건을 검증하도록 지시하는 **Bean Validation** 어노테이션
> 
> Bean Validation
> 
> * 도메인 모델의 필드에 유효성 제약을 위한 어노테이션을 제공하는 API → 편리
>     
> * 표준으로는 JSR 303, JSR 380 등이 존재하며, 이를 구현한 대표적인 구현체가 Hibernate Validator
>     

Spring에서는 일종의 어댑터인 LocalValidatorFactoryBean가 제약 조건 검증을 처리한다. 이를 이용하려면 LocalValidatorFactoryBean을 빈으로 등록해야 하는데, SpringBoot에서는 validation 의존성만 추가해주면 해당 기능들이 자동 설정된다.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1756637692760/fea2500e-e034-49de-91a8-a9312939faa0.png align="center")

### ✅ 주요 특징

* 데이터 무결성을 유지하는 데 필요한 유효성 검사 수행
    
* `javax.validation` 패키지에 포함
    
* `@NotBlank`, `@Min`, `@Email` 등과 함께 사용
    
* **컨트롤러에서만 유효성 검사가 작동**
    
* 유효성 실패 시 `MethodArgumentNotValidException` 발생
    
* `DispatcherServlet`에 기본으로 등록된 Exception Resolver인 `DefaultHandlerExceptionResolver`에 의해 400 에러가 발생
    

### 💡 동작 방식

1. 요청이 들어오면 `DispatcherServlet`에서 요청에 맞는 컨트롤러에 요청을 전달
    
2. 전달 과정에서 컨트롤러 메소드의 객체를 전달해주는 `HandlerMethodArgumentResolver` 작동
    
3. `HandlerMethodArgumentResolver`의 `resolveArgument` 메소드에서`@Valid`로 시작하는 어노테이션이 붙어있으면 `DispatcherServlet` 단에서 검사를 진행하는 식으로 동작하며, 검증을 할 파라미터에 `@Valid`를 붙여줘야 유효성 검증이 진행
    
    * `@RequestBody`는 Json 메세지를 객체로 변환해주는 작업, ArgumentResolver의 구현체인`RequestResponseBodyMethodProcessor`가 처리
        
    * `@ModelAttribute`를 사용중이라면 `ModelAttributeMethodProcessor`가 처리
        

cf) ArgumentResolver : `@RequestBody`가 붙은 데이터를 Json 포멧으로 변경

## 🧩 `@Validated`란?

입력 파라미터의 유효성 검증은 컨트롤러에서 최대한 처리하고 넘겨주는 것이 좋다. 하지만 개발을 하다보면 불가피하게 다른 곳에서 파라미터를 검증해야 할 수 있다. Spring에서는 이를 위해 AOP 기반으로 메소드의 요청을 가로채서 유효성 검증을 진행한다.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1756637736262/1e02d3b6-658a-48cf-befe-1bba76c86e77.png align="center")

### ✅ 주요 특징

* 메소드 요청을 가로채서 유효성 검증을 해주는 **Spring AOP 기반 기능**
    
    → 스프링 빈이면 어디든 작동 가능 (Controller, Service, Repository 등)
    
* 클래스 레벨에 `@Validated`, 유효성 검증을 할 파라미터에 `@Valid` 사용
    
* 유효성 실패 시 `ConstraintViolationException` 발생
    
* **그룹 검증(Validation Group)** 지원
    
* 만약, 특정 클래스를 지정하지 않는 경우 groups가 없는 속성들만 처리
    

### 💡 동작 방식

1. Spring AOP의 유효성 검증을 위한 `MethodValidationInterceptor`가 클래스에 등록됨
    
    * `MethodValidationInterceptor`에 Validator이 의존성 주입으로 들어가므로 `@Valid`와 똑같이 Hibernate Validator를 사용 → Bean Validation을 그대로 이용할 수 있음
        
2. 메서드 호출 시 AOP가 포인트컷으로 확인을 하고 요청을 중간에 가로채어 유효성 검사 진행
    
3. 실패 시 `ConstraintViolationException` 예외 발생
    

## 🧠 Validation Group, Group Sequence란?

### Group Validation

* 필드마다 조건을 다르게 걸고 싶을 때 사용
    
* 검증 규칙을 논리적인 그룹에 따라서 특정 상황에서만 적용
    
* 예: 회원 가입 시 필수, 수정 시는 선택
    

### Group Sequence

여러 검증 그룹을 순차적으로 검증하도록 하는 어노테이션

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1756637817174/561ad301-437b-466a-9576-4ff7c5acc9ae.png align="center")

→ 순서대로 그룹 검증을 적용

위에서 살펴보았듯 `@Validated`의 기능으로 유효성 검증 그룹의 지정도 있지만 거의 사용되지 않으므로 유효성 검증 진행을 기준으로 차이를 살펴보도록 하자.

## 🆚 `@Valid` vs `@Validated` 차이 정리

| 항목 | `@Valid` | `@Validated` |
| --- | --- | --- |
| 제공 | JSR-303(Java 표준) | Spring 프레임워크 |
| 적용 대상 | Controller 계층 | Spring Bean 전역 (Controller, Service 등) |
| 동작 방식 | ArgumentResolver 기반 | AOP 기반 |
| 검증 시점 | 요청 바인딩 시 | 메서드 호출 시 |
| 예외 | `MethodArgumentNotValidException` | `ConstraintViolationException` |
| 그룹 지원 | ❌ 미지원 | ✅ 지원 (`@GroupSequence` 등) |

## ✅ 결론: 언제 어떻게 써야 할까?

| 상황 | 사용 방식 |
| --- | --- |
| Controller에서 요청 DTO 검증 | `@Valid` |
| Service 계층 등 Bean에서 검증 | `@Validated` + `@Valid` |
| 그룹별 유효성 검사 필요 | `@Validated(groups = ...)` 사용 |

## 🔍 예외 정리

| 어노테이션 | 예외 | 설명 |
| --- | --- | --- |
| `@Valid` | `MethodArgumentNotValidException` | 요청 파라미터 바인딩 시 검증 실패 |
| `@Validated` | `ConstraintViolationException` | 메서드 실행 전 AOP로 검증 실패 |

## 참고 자료

[https://mangkyu.tistory.com/174](https://mangkyu.tistory.com/174)

[https://velog.io/@hellojihyoung/Spring-Valid와-Validation](https://velog.io/@hellojihyoung/Spring-Valid%EC%99%80-Validation)

[eckrin\*\*\[Spring\] @Valid와 @Validated를 이용한 순차 검증\*\*](https://eckrin.tistory.com/201)​