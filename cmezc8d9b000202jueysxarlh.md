---
title: "[Spring]@ElementController"
datePublished: Sun Jun 22 2025 15:00:00 GMT+0000 (Coordinated Universal Time)
cuid: cmezc8d9b000202jueysxarlh
slug: springelementcontroller
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1756554158814/a07c791a-6373-47f7-9ea2-45ffecf97bc5.png
tags: spring, elementcollection

---

## ✍️ 작성하게 된 이유

프로젝트를 진행하다가 RDB안에 List를 저장해야하는 상황이 발생했다. ElementController와 Jsonb를 고민하는 과정에서 ElementController에 대해 정리해보고자 한다.

값 타입은 몇 가지 **주의할 점**이 있으며, 잘못 사용하면 **사이드 이펙트**가 발생할 수 있다.

이번 글에서는 JPA에서의 값 타입 개념, 분류, 사용 예시, 주의사항, 그리고 실전 설계 팁까지 담아보았다.

---

## 💡 값 타입이란?

> 값 자체에 의미가 있으며, 식별자 없이 사용하는 타입

* 자바의 `int`, `String`, `Integer`와 같은 **기본형**
    
* 여러 값을 묶은 사용자 정의 타입인 **임베디드 타입**
    
* 리스트, 셋 등 컬렉션 형태로 사용하는 **값 타입 컬렉션**
    

---

## 📌 값 타입 분류

### 1️⃣ 기본 값 타입

* Java의 기본형 (`int`, `double`, `boolean`)
    
* 래퍼 클래스 (`Integer`, `Long`, `Double` 등)
    
* `String`
    

### ✅ 특징

* 엔티티 내부에서 사용되며 생명주기를 공유
    
    * ex) 회원 삭제하면 이름, 나이 필드도 삭제
        
* 값 타입은 절대 공유하면 안된다.
    
    * Side Effect → 참조를 공유하게 되면 예기치 않은 동시 변경이 발생할 수 있음
        

### ⚠️ 참고

* 자바의 기본타입은 항상 값을 복사하여 사용하기 때문에 절대 공유 되지 않는다.
    
* 래퍼클래스나 String은 주소값을 복사하여 사용하기 때문에 같은 장소를 가리키게 된다. 따라서, 하나를 수정하면 주소값이 공유된 객체도 수정된다.
    
    * 래퍼클래스나 String에는 setValue메서드가 존재하지 않는다. 따라서 공유가능한 객체지만 변경할 수 없다.
        

### 2️⃣ 임베디드 타입 (Embedded Type)

> 여러 값을 묶어 새로운 값 타입을 직접 정의
> 
> 기본 값 타입을 모아 만들어 복합 값 타입  
> `@Embeddable` : 값 타입을 정의하는 곳에 표시
> 
> `@Embedded`: 값 타입을 사용하는 곳에 선언

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1756556128113/c706c237-3110-467e-b375-8cf000a144f3.png align="center")

### ✅ 장점

| 항목 | 설명 |
| --- | --- |
| 재사용성 | 여러 엔티티에서 공통으로 사용 가능 |
| 높은 응집도 | 관련 필드를 하나의 타입으로 묶어 표현 |
| 객체지향적 설계 | 해당 값 타입만 사용하는 의미 있는 메서드 추가 가능 (예: `isWithinRange()`) |

### ⚠️ 주의 : 임베디드 타입 공유에 따른 사이드 이펙트

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1756556183211/4aff0aea-e9f9-4e35-8963-b1909a32eca5.png align="center")

> 👉 이렇게 공유하면 JPA는 **업데이트 쿼리를 두 번 날리고**, 두 객체의 상태가 **의도치 않게 함께 변경**됨

---

## 🧩 값 타입의 설계 팁

* Embedded 타입은 **기본 생성자 필수** (보통 `protected`)
    
* 같은 임베디드 타입 2번 이상 사용할 땐 컬럼명이 겹치기 때문에 `@AttributeOverrides` 로 재정의
    
* 테이블 수 &lt; 클래스 수  
    → 객체 모델은 더 세밀하게 나누는 것이 잘 설계된 ORM구조
    
    → 테이블 생성 쿼리는 임베디드를 사용하지 않을 때와 같다.(=매핑하는 테이블은 같다)
    

---

## ✅ 불변 객체(Immutable Object) 설계

값 타입의 **공유 참조 문제**를 해결하기 위해 내부 값을 변경할 수 없도록 설계

* `setXxx()` 제거
    
* 모든 필드는 `final` (선택)
    
* 생성자만으로 초기화
    

## 🧠 equals & hashCode 꼭 재정의

> 값 타입은 **동등성(equality)** 기준으로 비교해야 함  
> `==` 대신 `.equals()` 활용

---

### 3️⃣ **컬렉션 값 타입(collection value type)**

* Java collection(Array, Map, Set)에 값을 넣을수 있는 것을 컬렉션 값 타입이라 한다.
    

값 타입 컬렉션 Elemont Collection

> 값 타입(Value Object) 을 List, Set, Map 등의 컬렉션으로 관리하고, 별도의 테이블에 저장하는 JPA 기능

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1756556595920/7f04bb73-24d7-4b86-8206-07b8a0093606.png align="center")

* 한 개의 값 타입 컬렉션당 **1개의 테이블 생성**
    

* **컬렉션마다 별도의 테이블 필요** (하나의 테이블에 여러 컬렉션은 불가)
    

### 📌 특징

| 항목 | 설명 |
| --- | --- |
| 생명주기 | 엔티티에 종속 (영속성 전이 + 고아 객체 처리됨) |
| 저장방식 | 별도 테이블에 저장(엔티티는 아님) |
| 추적 방식 | 변경 시 전체 삭제 후 다시 INSERT( 즉, 쿼리 2~N개 발생) |
| 로딩 방식 | 컬렉션은 지연 로딩 (Lazy) |

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1756623002018/16e8cce9-d659-485f-801c-009871495fda.png align="center")

### ⚠️ 한계

* 컬렉션 내부 값이 바뀌면 전체 삭제 후 다시 저장
    
* `equals` / `hashCode`가 정확히 구현되어야 `remove` 작동
    
* 값타입은 식별자 개념이 없기 때문에 값의 변경을 JPA가 추적할 수 없음 → 객체 갈아끼우기 방식 사용
    

## 💡 값 타입 컬렉션의 한계 → 엔티티로 승격

<mark>위 한계 때문에 실무에서는 사용하지 않는 것을 추천한다.</mark>

실무에서는 **추적, 수정, 식별이 필요**하면 일대다 관계를 고려한다.

일대다 관계를 위한 엔티티르 만들고 여기에서 값 타입을 사용한다.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1756623264400/ac3d33be-646a-407e-816a-25a742300ba3.png align="center")

## ✅ 결론

| 값 타입은 언제 사용? | 언제 쓰면 안 됨? |
| --- | --- |
| 단순하고 불변일 때 | 자주 변경, 식별 필요, 참조 공유할 가능성 있을 때 |

**1\. 엔티티 타입의 특징**

* 식별자가 있다
    
* 생명 주기 관리(값 타입은 생명주기 관리를 주도적으로 할 수 없다.)
    
* 공유
    

**2\. 값 타입의특징**

* 식별자가 없다
    
* 생명 주기를 엔티티에 의존
    
* 공유하지 않는 것이 안전(복사해서 사용)
    
* 불가피하게 공유가 필요할 때 불변 객체로 만드는 것이 안전
    

## 참고자료

[https://ttl-blog.tistory.com/121](https://ttl-blog.tistory.com/121)

[https://www.inflearn.com/course/ORM-JPA-Basic/dashboard](https://www.inflearn.com/course/ORM-JPA-Basic/dashboard)

[https://s-y-130.tistory.com/284?category=1065584](https://s-y-130.tistory.com/284?category=1065584)

[https://s-y-130.tistory.com/283](https://s-y-130.tistory.com/283)