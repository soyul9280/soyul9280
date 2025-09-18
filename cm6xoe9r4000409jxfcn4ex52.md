---
title: "[ Spring ] 프레임워크 vs 라이브러리"
datePublished: Sun Feb 09 2025 13:44:24 GMT+0000 (Coordinated Universal Time)
cuid: cm6xoe9r4000409jxfcn4ex52
slug: spring-vs
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1739108925914/7ff9ae8f-3dca-4db2-b78f-523557e3ac18.png
tags: framework, library

---

> 목표 : 프레임워크와 라이브러리의 차이점을 제어 흐름의 주체와 사용 방식을 중심으로 확인하기
> 
> (Spring Framework와 일반 Java 라이브러리 활용)
> 
> 출처 : 위니브 - 이승주 강사님 강의, [https://ctrlcccv.github.io/code/2024-03-04-framework-library/](https://ctrlcccv.github.io/code/2024-03-04-framework-library/)

# 1️⃣ 프레임워크 ?

프레임워크는 애플리케이션의 전체적인 구조를 제공하며, 개발자는 프레임워크가 정의한 규칙과 절차에 따라 코드를 작성한다. 프레임워크는 제어의 역전(IoC)을 구현하여 애플리케이션의 주요 흐름을 제어하고 개발자가 작성한 코드를 호출한다.

즉 Service클래스를 이용하여 repository의 함수를 사용하는 것

단점 : 유연성 제한, 오버헤드(프레임워크 용량이 커서 성능에 영향 미칠 수 있음)

ex ) Java의 서버개발에 사용하는 Spring Framework

---

# 2️⃣ 라이브러리 ?

라이브러리는 특정 기능을 수행하는 코드의 모음으로 개발자가 필요한 기능을 선택하여 사용할 수 있다. 라이브러리를 사용할 때는 개발자가 애플리케이션의 흐름을 제어하고 필요한 시점에 라이브러리의 기능을 호출한다.

Arrays.asSort처럼 메서드 직접 개발자가 호출

단점 : 책임 분담 (라이브러리 작동 문제시 개발자가 해결), 호환성 문제

ex ) JQuery

## 👉🏻 차이점

라이브러리는 개발자가 필요한 기능을 선택적으로 사용하는 도구

프레임워크는 애플리케이션의 전체적인 구조와 흐름을 제어하는 도구

## ⚡ 정리

**프레임워크**  
전체적인 구조를 제공하여 개발자가 그에 따라 코드를 작성할 수 있음.

**라이브러리**  
특정 기능을 수행하는 코드의 모음