---
title: "[ Spring ] Bean Scope"
datePublished: Sat May 31 2025 15:00:00 GMT+0000 (Coordinated Universal Time)
cuid: cm89vd7tm000109kzgj342pbn
slug: spring-bean-scope
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1742012197112/f59f2076-baf3-4783-8d3e-1b27ae0067f5.png
tags: scope, bean

---

> 목표 : Bean Scope 개념 및 예제 코드 이해하기

# 1️⃣ 개요

Bean의 이벤트 사이클은 다음과 같다.

컨테이너 생성 → Bean 생성 → 의존관계 주입 → 초기화 콜백 → 사용 → 소멸 전 콜백 → 종료

즉. 의존관게 주입이 완료된 상태가 돼야 우리가 필요한 데이터를 사용할 준비가 된다.

Bean Scope는 우리가 사용하는 Context내 Bean의 생명주기를 정의한다. 최신의 Spring프레임워크에서 이를 6가지로 나누어 설명한다.

1. singleton
    
2. prototype
    
3. request
    
4. session
    
5. application
    
6. websocket
    

3번부터 6번은 Web환경에서만 사용된다.

---

# 2️⃣ Singleton Scope

Bean을 싱글톤 범위로 정한다면 컨테이너는 Bean을 단일 인스턴스로 생성한다. 동일한 이름의 Bean을 요청하면 같은 객체가 반환되며 이는 캐시된다. 해당 객체를 수정하면 해당 Bean의 모든 참조에 반영된다. 다른 범위를 지정하지 않으면 기본적으로 싱글톤 범위가 적용된다.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742015578338/d656fc12-3778-4563-a5ab-aa47500cd545.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742015599493/ec5a35b3-376c-4077-bf4d-a9a1bcbd9e2e.png align="center")

@Scope를 사용하여 해당 Bean의 범위를 지정하였다.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742015680440/502d6095-47c1-4b06-afaf-4950b25fa79e.png align="center")

테스트 코드를 보면 getBean을 통해 personSingletonA와 personSingletonB가 생성된다. 과연 이 2개의 객체가 같은 Person일까? 우선 personSingletonA에 setName을 통해 이름을 지정해주었다. 그 후 personSingletonB의 name이 John Smith인지 비교해보았다. 우리는 personSingletonA에만 이름을 지정해주었는데 테스트는 통과한다. 그 이유는 personSingletonA 와 personSingletonB가 같은 Person을 사용하고 있기 때문이다.

---

# 3️⃣ Prototype Scope

컨테이너로 부터 요청받을 때마다 다른 인스턴스를 반환한다.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742016623520/a6b7f89d-f792-49dc-ba78-465487063375.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742016662069/cc0f471a-dc24-4bb0-86e8-1fe789efa109.png align="center")

앞 테스트와 같은 내용이다. personSingletonA와 personSingletonB에 다른 이름을 설정했다. 만약 singleton일 경우 setName한 순간 personSingletonA와 personSingletonB에 같은 이름으로 설정되지만 지금은 prototype 범위이다.

getBean할 때마다 새로운 인스턴스가 생성되기 때문에 personSingletonA와 personSingletonB는 서로 다른 인스턴스이다. 따라서, name이 독립적으로 관리된다. setName을 해도 이전에 설정한 이름은 해당 객체에서 변하지 않는다.

---

# 4️⃣ Web환경에서의 Scope

## ❓Request Scope

하나의 HTTP 요청 당 Bean이 생성된다.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742017135941/7308a668-0944-46f4-9761-01cab66670c1.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742017268523/a9dd11a2-0066-481c-a6f0-5cfc021ab918.png align="center")

@Scope를 통해 Bean의 범위를 지정했는데 String 뿐 만 아니라 다음과 같이 WebApplicationContext를 이용해서 지정할 수도 있다.

ApplicationContext는 애플리케이션이 시작될 때 Bean을 미리 생성해두는데 request scope는 고객의 요청이 와야 Bean을 생성할 수 있어 Bean을 만들지 못한다. 이 문제를 해결하기 위해 Proxy를 생성하여 의존성 주입을 진행하고 실제 요청이 발생할 때 해당 Bean을 생성하여 사용한다.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742019788685/d6970c7e-6e79-4200-a995-edd324b18143.png align="center")

RequestScopeBean을 주입받는 컨트롤러이다. 웹 전용 스코프를 테스트하려면 같은 요청을 두 번 이상 해야한다.

먼저 previousMessage에는 최초 요청 값이므로 null이 들어간다. 그 후 currentMessage에는 Good morning이 들어간다.

또 다시 request를 요청하면 previousMessage 에는 null이 들어간다. 이전 요청에서의 setMessage가 유지되지 않는다.

이를 통해, 요청마다 새로운 Bean 인스턴스를 생성하기 때문에 이전 메세지가 유지되지 않는다는 것을 확인할 수 있다.

## ❓Session Scope

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742020794580/563d92f0-1939-49ec-ba66-253ca06a1b5f.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742020828027/c85b5d0b-95de-4516-9b00-fa3efc94136a.png align="center")

request scope와 같은 방식이다. session범위에서 메세지 필드값이 같은지 확인하기 위해 두번 요청한다. 처음 요청했을 때, previousMessage는 null이며 currentMessage는 Good afternoon이다. 두 번째 요청했을때 previousMessage는 Good afternoon이다. 같은 세션내 실행된 두 번째 요청에서 같은 Bean인스턴스를 사용하기 때문이다.

## ❓Application Scope

ServletContext 의 생명주기 당 Bean인스턴스를 생성한다. 싱글톤 범위와 비슷하지만, 싱글톤 범위는 단일 ApplicationContext에서만 사용되는 반면, application 범위는 여러 개의 ApplicationContext에서 공유가 가능하다.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742021223593/d35887fb-39a7-4c8a-9714-0dd7f9371b1a.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742021252846/c2148c8d-52ad-4c62-a3d4-5b52d5e1b97b.png align="center")

application들이 같은 ServletContext에서 실행될 경우에, Bean에 값이 설정되면 해당 message}의 값은 다음 모든 요청에서도 유지된다. session 뿐만아니라 다른 servlet application에서도 유지된다. 결과는 request scope와 같다.

## ❓WebSocket Scope

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1742022509248/8f02b5ca-a877-4c34-92d2-20db8121d306.png align="center")

처음 접근할때, WebSocket 범위를 가진 Bean은 WebSocket 세션 속성에 저장된다. 이후 WebSocket 세션 전체에서 해당 Bean에 접근할 때마다, 동일한 Bean 인스턴스가 반환된다. WebSocket 세션 내에서만 제한적으로 적용된다.

출처 : [https://www.baeldung.com/spring-bean-scopes](https://www.baeldung.com/spring-bean-scopes)