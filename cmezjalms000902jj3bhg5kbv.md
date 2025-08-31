---
title: "Json과 Jsonb"
datePublished: Sun Jun 22 2025 15:00:00 GMT+0000 (Coordinated Universal Time)
cuid: cmezjalms000902jj3bhg5kbv
slug: json-jsonb
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1756633065737/e888c25b-ecaa-4730-a2ad-d65372d6faf7.png
tags: postgresql, json, jsonb

---

## ✍️ 작성하게 된 이유

프로젝트에서 데이터 값을 Json으로 저장해야하는 경우가 있었다. 해당 프로젝트에서 PostgreSQL을 사용했는데 이는 JSON 데이터를 저장하고 활용할 수 있는 기능을 제공한다. 특히 `JSON`과 `JSONB`라는 두 가지 데이터 타입이 존재하는데, 처음 보면 **왜 두 개나 있는지**, **언제를 선택해야 하는지**, **어떤 차이가 있는지** 혼란스럽다.

이번 글에서는 JSON과 JSONB의 **차이점, 장단점, 선택 기준**까지 하나하나 정리해보려 한다.

---

## 💡 JSON이란?

`{ "key": "value" }` 구조로 된 **가볍고 파싱이 쉬운 데이터 형식**  
→ 널리 사용되고 있으며, PostgreSQL에서도 이를 저장할 수 있다.

PostgreSQL에서는 두 가지 타입을 지원한다.

두 타입의 공통점은 JSON포맷 유효성 체크를 한다.

(JSONB는 9.4버전부터 추가되었다.)

| 타입 | 설명 |
| --- | --- |
| `JSON` | 입력된 문자열을 그대로 저장 |
| `JSONB` | 바이너리 포맷으로 정제해 저장 |

## 🔍 JSON vs JSONB 차이점

| 항목 | JSON | JSONB |  |
| --- | --- | --- | --- |
| 저장 방식 | 입력값에 대한 복사본 저장 → 원본 저장 | 공백 제거, key 정렬 후 decopmose된 바이너리 저장→ 정제된 데이터 저장 |  |
| 파싱 방식 | 요청 시마다 매번 파싱→ 처리속도 느림 | 데이터 파싱 비용이 들지 않아 읽기 비용 적음 → 처리속도 빠름 | 한번 정제 후 저장한 것을 재사용 (빠름) |
| 키 순서 | **보존** | 보존 ❌ |  |
| 공백 | **그대로 유지** | 제거 |  |
| 중복 키 | 모두 저장 | 마지막 키만 저장 |  |
| 인덱스 | ❌ 불가능 | ✅ 가능 (GIN 등) |  |
| 연산자 지원 | 제한적 | 대부분 연산자 지원 |  |
| 쓰기 성능 | ✅ 빠름 | ❌ 느림 |  |
| 읽기 성능 | ❌ 느림 | ✅ 빠름 |  |
| 디스크 사용량 | 상대적으로 적음 | 더 큼 (항상 그런 건 아님) |  |

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1756633839205/1bfc8a92-1d02-436a-a22a-8e85bd9a93ed.png align="center")

**요약:**

> **특별한 이유가 없다면 기본적으로** `JSONB`를 쓰는 것이 좋다!

## ✅ 언제 JSON을 써야 할까?

| 사용 상황 | 추천 타입 |
| --- | --- |
| 원본 보존이 중요할 때 (ex. 로그) | `JSON` |
| INSERT가 많고, 복잡한 쿼리 필요 없음 | `JSON` |
| 기본 객체 키에 대한 순서 보장이 있을 때 | `JSON` |
| 대규모 조회, 고속 필터링 필요 | `JSONB` |

---

## 🔧 JSON / JSONB 연산자 정리

### 1️⃣ 기본 연산자 (JSON/JSONB 공통)

| 연산자 | 설명 | 결과 |
| --- | --- | --- |
| `->` | 키 또는 인덱스에 해당하는 요소 추출 | JSON |
| `->>` | 키 또는 인덱스에 해당하는 요소 추출 | Text |

int형이 주어지면 배열 내 인덱스로 text형이 주어지면 해당 키에 대응하는 요소를 추출한다.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1756634126945/d7215ab5-f73e-4809-afe4-ef5729401e70.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1756634132781/7fa18de5-b39e-4504-b000-464b363d6bd3.png align="center")

| 연산자 | 설명 | 결과 |
| --- | --- | --- |
| `#>` | 중첩된 필드의 특정 조건만을 검색하고 싶을때 | JSON |
| `#>>` | 중첩된 필드의 특정 조건만을 검색하고 싶을때 | Text |

검색 조건으로 활용하는 필드는 콤마로 구분한다.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1756634209329/dbb0d3af-b89c-4eb5-a0e9-177a0a1b41e5.png align="center")

위 연산자들은 `JSON`형으로 저장된 컬럼에 원하는 조건에 해당하는 데이터를 추출하고 싶을 때 사용한다

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1756634254845/c45d0120-950f-44d9-9fa5-8d290e9492fe.png align="center")

### 2️⃣ JSONB 전용 연산자

| 연산자 | 설명 | 결과 |
| --- | --- | --- |
| `@>` | 포함 여부 (data가 값 포함) | Boolean |
| `<@` | 포함 여부 (값이 data 포함) | Boolean |

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1756634308446/b1918c41-8ee8-49a9-bec7-009eea7e3e2c.png align="center")

| 연산자 | 설명 | 결과 |
| --- | --- | --- |
| `?` | 키 존재 여부 | Boolean |
| `?|` | 검색 조건으로 배열이 주어짐. 배열 내 키 하나라도 존재= true | Boolean |
| `?&` | 검색 조건으로 배열이 주어짐. 배열 내 키 모두 존재= true | Boolean |

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1756634408212/4b2d62e1-bd47-493b-be30-d04a8d886b98.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1756634533843/232f4069-8f99-43ac-82d7-a9b6dd251220.png align="center")

| 연산자 | 설명 |
| --- | --- |
| `||` | 주어진 2개의 JSONB들을 하나로 합침 |
| `-` | 주어진 키 제거, int형이 주어지면 특정 인덱스에 해당하는 요소 제거 |
| `#-` | 주어진 경로에 해당하는 값 제거 |

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1756634646273/53d03269-8fbd-4cbf-b130-778f453f241d.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1756634651087/b3933048-9e81-4096-8d96-51dd3fd6e5b8.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1756634671461/2b83b9e4-7171-43b7-a8d3-c15421c67ef9.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1756634780141/67e1a419-626a-4326-bc93-deafaf5d00c5.png align="center")

## ⚠️ 주의할 점

* JSON은 **인덱스 불가능** → 복잡한 조회 시 성능 저하
    
* JSONB는 **쓰기 성능 낮음** → 대량 INSERT 시 부담
    
* JSONB는 **중복 키 제거** → 마지막 키만 유지됨
    
* **데이터 파싱 및 정렬 비용** 고려 필요
    

---

## ✅ 결론

| 조건 | 선택 |
| --- | --- |
| 원본 유지 중요 | JSON |
| 읽기 속도 중요 | JSONB |
| 검색, 인덱스 활용 | JSONB |
| 단순 로그 저장 | JSON |

👉 대부분의 CRUD 기반 웹 서비스에서는 **JSONB가 더 유리**

## 참고자료

[americano\_people**(PostgreSQL) JSON VS JSONB**](https://americanopeople.tistory.com/300)​

[yeongun**\[PostgreSQL\] json, jsonb 타입과 연**](https://yeongunheo.tistory.com/entry/PostgreSQL-json-jsonb-%ED%83%80%EC%9E%85%EA%B3%BC-%EC%97%B0%EC%82%B0%EC%9E%90)[**산자**​](https://yeongunheo.tistory.com/entry/PostgreSQL-json-jsonb-%ED%83%80%EC%9E%85%EA%B3%BC-%EC%97%B0%EC%82%B0%EC%9E%90)

[https://postgresql.kr/blog/postgresq](https://yeongunheo.tistory.com/entry/PostgreSQL-json-jsonb-%ED%83%80%EC%9E%85%EA%B3%BC-%EC%97%B0%EC%82%B0%EC%9E%90)[l\_jsonb.html](https://postgresql.kr/blog/postgresql_jsonb.html)

[https://www.oracle.com/kr/database/what-is-json/](https://www.oracle.com/kr/database/what-is-json/)

[안종혁**PostgreSQL JSON / JSONB 타입을 JPA와 매핑하기**](https://dkswhdgur246.tistory.com/92)​