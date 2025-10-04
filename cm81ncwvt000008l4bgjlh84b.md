---
title: "[ 데이터베이스 ] 정규화와 역정규화"
datePublished: Sat May 24 2025 15:00:00 GMT+0000 (Coordinated Universal Time)
cuid: cm81ncwvt000008l4bgjlh84b
slug: wydrjbdsnbtthldrsqdsnbtsiqqgxsdsojxqt5ztmztsmyag7jet7kcv6rec7zmu
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1741520524611/39a1f0e2-a590-471b-8818-385d2506128e.png
tags: nomarlization

---

> 목표 : 역정규화가 필요한 상황, 적용 시 고려해야 할 점, 장단점 확인하기.

# ❓정규화 (Normalization)

<div data-node-type="callout">
<div data-node-type="callout-emoji">💡</div>
<div data-node-type="callout-text">중복을 최소화하고 데이터 구조를 조직화하여 데이터 일관성 유지시키는 과정</div>
</div>

이를 위한 각각의 규칙을 Normal Form (NF)라고 한다.

## 1️⃣ 제 1정규형

원자값을 갖도록 테이블을 분해한다.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1741520935048/bcb5ba2f-be3e-43f2-9c93-f5d992fa1781.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1741520947468/69db27fa-241f-4c85-a9de-d50db3937d59.png align="center")

장점 : 데이터 중복 최소화, 데이터 일관성 유지

단점 : 데이터 구조 복잡할 가능성 존재

## 2️⃣ 제 2정규형

현재 테이블 주제와 관련없는 컬럼들을 다른 테이블로 만들기

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1741521059461/2623ab20-af65-4650-ad85-3b3fb9bfa7a2.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1741521097352/810a9243-a67e-4ae2-9b98-d64bb5e2f010.png align="center")

장점 : 데이터 수정 편리

단점 : 테이블 분리로 일부 쿼리 복잡 가능성 존재

## 3️⃣ 제 3정규형

일반 컬럼에만 종속된 컬럼을 다른 테이블로 만들기

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1741521195464/ed343ce0-fe4f-400e-a614-6a1808419966.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1741521231276/56eb9a98-d680-4ad3-a47a-34d66df87244.png align="center")

장점 : 이상현상 방지

단점 : 많은 조인으로 성능 문제가 발생할 가능성 존재

* ### 🙋🏻‍♀️ 이상현상
    
    테이블에서 일부 데이터들이 불필요하게 중복되어 발생하는 문제
    
    1 ) 삽입 이상 : 의도와 상관없는 값들이 같이 삽입, 데이터가 부족해서 null값이나 불필요한 값 삽입
    
    2) 삭제 이상 : 삭제할때 함께 연쇄적으로 삭제됨
    
    3) 갱신 이상 : 갱신할 때 일부 데이터만 갱신되어 모순 발생
    

---

## ❓역정규화

<div data-node-type="callout">
<div data-node-type="callout-emoji">💡</div>
<div data-node-type="callout-text">성능을 향상시키기 위해 정규화된 구조를 일부 중복/통합/분리하는 과정</div>
</div>

**When** : 정규화된 테이블 중 읽기가 자주 일어날 때, 조회 성능이 중요할 때

* 정규화로 테이블이 나뉘어지게 되면 join이 필수 → 비용이 큼
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1741524053456/2d529f9a-fd59-4718-b07f-c3d8427e25a4.png align="center")

저자별로 몇개의 토픽을 생성했는지 알고싶을 때 group by를 사용한다. 데이터 개수가 많아질 수록 연산이 느려진다.

```sql
select author_id, count(author_id)
from topic
group by author_id;
```

이를 해결하기 위해 토픽 테이블에 행을 추가할 때마다 저자 아이디를 카운트해서 저자 테이블에 최신화한다.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1741524297616/fbd575e0-9aa3-4448-9333-29f74ef1e823.png align="center")

**Pros** : 읽기에 들어가는 부하, 비용 줄여 성능 향상, 복잡한 쿼리 간소화

**Cons** : 시스템 복잡도 증가, 프로그램 고장날 가능성 존재, 정규화하기 전 문제를 가짐

**고려해야할 점**

1. 무결성(데이터 정확성) : 데이터 중복으로 나중에 수정할때 일부만 변경되어 정확하지 않을 수 있다. 데이터베이스에서 무결성은 가장 중요한 요소라서 민감한 부분이다.
    
    (ex) topic\_count 값을 수동으로 업데이트 해야하므로 무결성 해칠 위험 있음.
    
2. 쓰기 속도 느려짐 : 따라서 읽기 작업이 중요할 때 고려한다.
    
3. 저장공간 효율 떨어짐 : 중복된 데이터의 공간 차지로 데이터 용량이 늘어난다.
    
4. 유지보수 어려움 : 테이블이 복잡하여 쉽게 수정할 수 없고 확장성이 떨어진다.
    

* ### 🙋🏻‍♀️ 중복 데이터 = total\_count
    
    정규화된 토픽 테이블에서 COUNT연산을 수행하면 작성자의 게시글 수를 계산할 수 있는데 역정규화로 직접 값을 저장한다. 같은 정보를 다른 방식으로 중복 저장하므로 중복 데이터로 간주한다.
    

---

출처: [https://velog.io/@calis\_ws/DB-%EC%A0%95%EA%B7%9C%ED%99%94-%EC%97%AD%EC%A0%95%EA%B7%9C%ED%99%94](https://velog.io/@calis_ws/DB-%EC%A0%95%EA%B7%9C%ED%99%94-%EC%97%AD%EC%A0%95%EA%B7%9C%ED%99%94), [https://velog.io/@clock509/%EC%97%AD%EC%A0%95%EA%B7%9C%ED%99%94](https://velog.io/@clock509/%EC%97%AD%EC%A0%95%EA%B7%9C%ED%99%94), [https://whdgus928.tistory.com/156](https://whdgus928.tistory.com/156),[https://young0105.tistory.com/282](https://young0105.tistory.com/282), [https://aeroej.tistory.com/149](https://aeroej.tistory.com/149)