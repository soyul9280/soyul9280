---
title: "[Spring] @Cacheable, @CachePut, @CacheEvict"
datePublished: Sat Jul 19 2025 15:00:00 GMT+0000 (Coordinated Universal Time)
cuid: cmbob7xn8000002jx6miy2jol
slug: spring-cacheable-cacheput-cacheevict
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1749265456525/51b97bad-f86e-4f0f-9b33-77eaa733176f.png
tags: cache

---

캐시는 서버의 부담을 줄이고 성능을 높이기 위해 사용되는 기술이다. 반복적으로 동일한 결과를 반환하는 경우 용이하다.

## ✅ 설정

```java
@EnableCaching
@Configuration 
public class CacheConfig {
    ... 
}
```

## ✅ 캐시 매니저 빈 추가

캐시를 관리해 줄 CacheManager를 빈으로 등록한다.

* ConcurrentMapCacheManager : Java의 ConcurrentHashMap을 사용해 구현
    
* SimpleCacheManager : 캐시를 직접 등록하여 사용
    

# 1️⃣ @Cacheable

캐시를 저장/조회하기 위함

캐시를 적용할 메서드에 이 어노테이션을 붙여주면 캐시에 데이터가 없을 경우 기존의 로직을 실행한 후에 캐시에 데이터를 추가하고 캐시에 데이터가 있으면 캐시의 데이터를 반환한다.

```java
@Cacheable("bestSeller")
public Book getBestSeller(String bookNo) {
..
}
```

캐시는 기본적으로 캐시 이름 하위에 key-value형태로 저장하는데 bestSeller가 캐시의 이름이고 그 하위에 bookNo를 key값으로 저장한다.

1. getBestSeller(1) 처음 호출
    
    * bestSeller캐시에 bookNo(1)에 해당하는 값이 있는지 확인한다.(값 없음)
        
    * 캐시에 값이 없으므로 해당 로직을 실행하여 값을 반환한다.
        
    * 반환한 값을 캐시에 bookNo(1)의 value로 저장한다.
        
2. getBestSeller(1) 두번째 호출
    
    * bestSeller캐시에 bookNo(1)에 해당하는 값이 있는지 확인한다.(값 있음)
        
    * 캐시에 값이 있으므로 로직을 실행하지 않고 캐시에서 조회한 값을 반환한다.
        

메서드에 파라미터가 없다면 0을 key로 저장하고 여러개면 hashCode값을 조합하여 키를 생성한다. 여러 파라미터 중에서 1개만 키 값으로 지정하는 경우는 key값을 별도로 지정해주면 된다.

```java
@Cacheable(value = "bestSeller", key = "#bookNo")
public Book getBestSeller(String bookNo, User user, Date dateTime) {

}
```

만약 파라미터가 객체라면 하위속성에 접근해야한다.

```java
@Cacheable(value = "bestSeller", key = "#book.bookNo")
public Book getBestSeller(Book book, User user, Date dateTime) {

}
```

만약 파라미터 값이 특정 조건인 경우에만 적용해야한다면 condition을 이용한다.

```java
@Cacheable(value = "bestSeller", key = "#book.bookNo", condition = "#user.type == 'ADMIN'")
public Book getBestSeller(Book book, User user, Date dateTime) {

}
```

# 2️⃣ @CachePut

캐시에 값을 저장하는 용도로만 사용한다. 항목을 변경할 때마다 선택적으로 업데이트한다. @Cacheable과 유사하게 실행 결과를 캐시에 저장하지만 조회시 저장된 캐시의 내용을 사용하지 않고 항상 메서드의 로직을 실행한다.

# 3️⃣ @CacheEvict

만약 값이 달라진다면 캐시를 제거해야하는 것처럼 캐시는 적절한 시점에 제거가 되어야한다.

* 일정한 주기로 제거
    
* 값이 변할 때 제거
    

이 어노테이션에 캐시 이름을 넣어주면 메서드가 실행될 때 캐시의 내용이 제거된다.

```java
@CacheEvict(value = "bestSeller")
public void clearBestSeller() {
}
-- 메서드의 키에 해당하는 캐시만 제거하는데 밑은 bookNo키 값 캐시만 제거된다.
@CacheEvict(value = "book", key = "#book.bookNo")
public void updateBook(Book book) {

}
--캐시에 저장된 값을 모두 제거할 필요가 있다면 다음 옵션을 true로 설정한다.
@CacheEvict(value = "bestSeller", allEntires = true)
public void clearBestSeller() {

}
```

* 출처
    
    [https://mangkyu.tistory.com/179](https://mangkyu.tistory.com/179)