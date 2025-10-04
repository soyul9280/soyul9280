---
title: "[Spring] @AllArgsConstructor을 지양하는 이유"
datePublished: Sat Aug 09 2025 15:00:00 GMT+0000 (Coordinated Universal Time)
cuid: cmf0zgl0h000h02ju8lcf7kdc
slug: spring-allargsconstructor
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1756719696393/93403110-517d-4d27-bc2c-4596278850f9.png
tags: allargsconstructor

---

## ✍️ 작성하게 된 이유

Spring을 사용하면서 Lombok의 `@AllArgsConstructor`, `@RequiredArgsConstructor` 같은 어노테이션은 **정말 편리**합니다. 필드를 선언해두기만 하면, 생성자를 자동으로 만들어주기 때문입니다.

하지만 이 편의성이 **설계와 유지보수 측면에서 오히려 위험**이 될 수 있습니다.

이번 글에서는 `@AllArgsConstructor` 사용 시 발생할 수 있는 문제와 이를 대체할 수 있는 더 안전한 방법을 정리해보려 합니다.

---

## `@AllArgsConstructor`란?

```java
@AllArgsConstructor
public class User {
    private String id;
    private String name;
}
```

→ 위 코드처럼 클래스에 선언된 **모든 필드**를 파라미터로 받는 생성자를 자동으로 만들어주는 Lombok 어노테이션입니다.

## 그런데 왜 위험할까?

### 필드 순서 변경 = 치명적 버그 가능성

```java
User user = new User("Kim", "1234"); // id에 name이 들어가고, name에 id가 들어감!
```

→ **같은 타입의 필드가 여러 개일 경우**, 필드 순서를 바꾸는 순간 **컴파일 에러가 발생하지 않고** 추후 인지하지 못한 버그가 발생할 수 있습니다. 자동으로 생성되기 때문에 추후 문제가 발생했을 때 버그를 잡기 어렵습니다.

## 그렇다면 `@RequiredArgsConstructor`는 괜찮을까?

`@AllArgsConstructor`와 동일한 문제를 안고 있지만 편의성을 위해 이 정도는 인지하고 사용해도 괜찮다고 합니다.

```java
@RequiredArgsConstructor
public class MemberService {
    private final MemberRepository memberRepository;
    private final EmailService emailService;
}
```

### ✅ 이유

* `final` 붙이면서 필수 파라미터에 대한 인지를 하고 필드 선언
    
* 필드 순서가 변경되어도 스프링이 타입과 이름에 따른 빈을 찾아 주입하기 때문에 무리 없음
    
* **의존성이 명확**
    

### ⚠️ 주의

* 스프링이 관리하지 않는 사용자가 직접 만들고 사용하는 일반 객체에서는 여전히 **위험 가능성 존재**
    
* 생성자에 들어가는 필드가 많아질 경우, **의존성 설계 재검토 필요**
    

## 대안: `@NoArgsConstructor` 와 `@Builder`를 이용하자

`@NoArgsConstructor` 은 기본 생성자를 자동으로 생성해줍니다. 하지만, 접근제어자를 명확하게 정의하지 않고 그대로 사용하면 의미 없는 객체 생성을 막을 수 없습니다. 따라서 `@NoArgsConstructor(access = AccessLevel.PROTECTED)`같이 접근제어자를 명시하여 코드의 안전성을 높이는 것이 좋습니다.

또한, 메서드 레벨에서 `@Builder` 를 사용하여 의미있는 객체를 생성할 수 있습니다. 필드 순서를 보장해주기 코드의 안전성이 높아집니다.

## 클래스 레벨에 `@Builder`를 붙이면 안되나?

이번 프로젝트에서 클래스에 `@Builder`와 `@NoArgsConstructor`를 사용했었습니다. 하지만 `Lombok @Builder needs a proper constructor for this class`에러가 발생하였습니다. 이를 해결하기 위해서는 `@AllArgsConstructor`도 같이 사용해야합니다.

에러가 발생하는 이유는 다음과 같습니다.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1756723671566/25469bd8-2754-4c78-b32b-8bd927b77cb0.png align="center")

정리해 보면 `@NoArgsConstructor`어노테이션이나 다른 생성자들이 존재하지 않을 경우 전체 생성자를 자동을 생성해 주고, 기본 생성자나 다른 생성자들이 존재하면 `@AllArgsConstructor`을 사용해서 직접 전체 생성자를 만들어줘야 합니다.  
클래스 레벨에서 `@Builder`경우 기본 생성자를 생성해주는 `@NoArgsConstructor`와 같이 사용하게 된다면 `@Builder`에서 제공하는 암묵적 `@AllArgsConstructor`이 사라지게 되어 해당 오류가 발생합니다.

따라서, `@AllArgsConstructor`의 위험성이 다시 나타나기 때문에 메서드 레벨에서 `@Builder`를 사용하는 것이 좋습니다.

## 참고자료

[https://techjisu.tistory.com/155](https://techjisu.tistory.com/155)

[혹시 @AllArgsConstructor 를 지양하시는 이유가 빌더 패턴을 사용하기 위함인가요?](https://www.inflearn.com/community/questions/1179578/%ED%98%B9%EC%8B%9C-allargsconstructor-%EB%A5%BC-%EC%A7%80%EC%96%91%ED%95%98%EC%8B%9C%EB%8A%94-%EC%9D%B4%EC%9C%A0%EA%B0%80-%EB%B9%8C%EB%8D%94-%ED%8C%A8%ED%84%B4%EC%9D%84-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0-%EC%9C%84%ED%95%A8%EC%9D%B8%EA%B0%80%EC%9A%94?srsltid=AfmBOorpSHhT_ymfi0YEtyZEkpoUhCGFcXsbOB7LgI6TUAfqdib3xUxv)

[https://coding789.tistory.com/324](https://coding789.tistory.com/324)

[https://marchcodig.tistory.com/285](https://marchcodig.tistory.com/285)

[https://resilient-923.tistory.com/418](https://resilient-923.tistory.com/418)