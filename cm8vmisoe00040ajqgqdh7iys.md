---
title: "[Spring] N+1문제 원인과 해결방법"
datePublished: Sat Jun 07 2025 15:00:00 GMT+0000 (Coordinated Universal Time)
cuid: cm8vmisoe00040ajqgqdh7iys
slug: spring-jpa-problem
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1743314857441/fd0e89cf-a2e9-48b6-92e4-1115a991e3fd.png
tags: springboot, jpa

---

> 목표 : JPA에서 발생하는 N+1 문제의 발생 원인과 해결 방안 확인하기

시각화를 위해 예시를 사용한다. 다이어그램에서는 Iterable을 사용했지만 , 실제 구현에서는 List를 사용한다.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1743318148191/48925382-aaa4-4f21-a42e-fae5aadeca07.png align="center")

# 1️⃣ N+1 ?

N+1 문제는, 예를 들어 `User` 목록을 조회하는 단일 요청 하나에 대해, 각 `User`의 정보를 가져오기 위해 **추가적인 쿼리**가 발생하는 상황을 말한다. 다양한 연관관계들의 매핑에 의해서 관계가 맺어졌을때 다른 객체가 함께 조회되는 경우에 발생한다. 이 문제는 **어떤 종류의 관계**에서도 발생할 수 있다. 하지만 보통 **다대다(many-to-many)** 또는 **일대다(one-to-many)** 관계에서 주로 나타난다.

1. Lazy-Loading 지연 로딩
    
    ```java
    @Entity
    @Getter
    @NoArgsConstructor(access = AccessLevel.PROTECTED)
    public class User {
        @Id
        private Long id;
        private String username;
        private String email;
        @OneToMany(cascade = CascadeType.ALL, mappedBy = "author",fetch = FetchType.LAZY)
        private List<Post> posts;
    }
    
    @Entity
    @Getter
    @NoArgsConstructor(access = AccessLevel.PROTECTED)
    public class Post {
        @Id@GeneratedValue
        private Long id;
        private String content;
        @ManyToOne(fetch = FetchType.LAZY)
        private User author;
    }
    ```
    
    `User`는 `Post`와 **일대다 관계**를 가지고 있으며, 각 사용자(User)는 여러 개의 게시글(Post)을 갖는다. 모든 `User`를 가져오려 해도, **Lazy Fetch는** 우리가 접근한 정보만 조회한다. 즉, 모든 `User`를 가져올 때는 **단 하나의 쿼리**만 실행된다. `posts` 정보는 이전에 가져오지 않았기 때문에 `posts`에 접근하려고 하면, Hibernate는 **추가 쿼리**를 실행한다.
    
    ```java
    @Test
    @DisplayName("Lazy type은 User 검색 후 필드 검색을 할 때 N+1문제가 발생한다.")
    void userFindTest() {
            System.out.println("== start ==");
            List<User> users = userRepository.findAll();
            System.out.println("== find all ==");
            for (User user : users) {
                System.out.println(user.getPosts().size());
            }
        }
    ```
    
    User가 2명일때, findAll에서 쿼리 1개 + user.getPosts().size()에서 추가 쿼리가 2개(User가2명이니까)로 N+1문제가 발생한다.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1743328853771/301e6176-b847-4541-a7ae-8e89da3d1b96.png align="center")
    
2. Eager fetch 즉시 로딩
    
    즉시 로딩으로 변환했을 때,
    
    ```java
    @OneToMany(cascade = CascadeType.ALL, mappedBy = "author",fetch = FetchType.EAGER)
    private List<Post> posts;
    
    @ManyToOne(fetch = FetchType.EAGER)
    private User author;
    ```
    
    테스트 코드는 다음과 같다.
    
    ```java
    @Test
    @DisplayName("Eager type은 User를 단일 조회할 때 join문이 날아간다.")
    void userSingleFindTest() {
            System.out.println("== start ==");
            User user = userRepository.findById(1L)
                    .orElseThrow(RuntimeException::new);
            System.out.println("== end ==");
            System.out.println(user.getUsername());
        }
    ```
    
    단일 조회에서는 조인으로 쿼리가 한번만 나가는 것을 확인 할 수 있다.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1743330177184/9c14bb43-67dc-4ff0-aa4a-3546c842bbf9.png align="center")
    
    하지만 모든 User를 조회하는 경우 Posts 필드를 실제로 사용하든 말든 무조건 N+1 문제가 발생한다.
    
    ```java
    @Test
    @DisplayName("Eager type은 User를 전체 검색할 때 N+1문제가 발생한다.")
    void userFindTestEager() {
         System.out.println("== start ==");
         List<User> users = userRepository.findAll();
         System.out.println("== find all ==");
    }
    ```
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1743330368678/12ee658d-5fb7-41ee-a9bb-c7fcd35be4eb.png align="center")
    

---

# 2️⃣ 해결 방안

1. 일반적인 Fetch Join
    
    쿼리를 날릴 때 post을 한번에 가져옴을 알 수 있다.
    
    ```java
    @Query("select distinct u from User u left join fetch u.posts")
    List<User> findAllJPQLFetch();
    
    //테스트
    @Test
        @DisplayName("fetch join을 하면 N+1문제가 발생하지 않는다.")
        void fetchJoinTest() {
            System.out.println("== start ==");
            List<User> users = userRepository.findAllJPQLFetch();
            System.out.println("== find all ==");
            for (User user : users) {
                System.out.println(user.getPosts().size());
            }
        }
    ```
    
    ```java
    //결과
    == start ==
    select distinct u1_0.id,u1_0.email,p1_0.user_id,p1_0.id,p1_0.content,u1_0.username from users u1_0 left join posts p1_0 on u1_0.id=p1_0.user_id;
    == find all ==
    2
    1
    ```
    
2. @EntityGraph  
    위 테스트 코드에서 findAllEntityGraph를 사용했을때, 결과이다.
    
    ```java
    @EntityGraph(attributePaths = {"posts"}, type = EntityGraph.EntityGraphType.FETCH)
    @Query("select distinct u from User u left join u.posts")
    List<User> findAllEntityGraph();
    ```
    
    ```java
    //결과
    == start ==
    select distinct u1_0.id,u1_0.email,p1_0.user_id,p1_0.id,p1_0.content,u1_0.username from users u1_0 left join posts p1_0 on u1_0.id=p1_0.user_id;
    == find all ==
    2
    1
    ```
    

장점 : 단 한번의 쿼리만 발생하도록 설계할 수 있다.

단점 :

1. 번거롭게 쿼리문을 작성해야 함
    
2. JPA가 제공하는 Pageable 기능 사용 불가→ 페이징 단위로 데이터 가져오기 불가능
    

* batch size로 해결 : 즉시로딩이나 지연로딩 시에 연관된 엔티티를 조회할 때 지정한 size 만큼 sql의 IN절을 사용해서 조회하는 방식
    

3. 1 : N 관계가 2개인 엔티티를 패치 조인 사용 불가→ MultipleBagFetchException 발생
    

* batch size로 해결
    

출처

[https://velog.io/@jinyoungchoi95/JPA-%EB%AA%A8%EB%93%A0-N1-%EB%B0%9C%EC%83%9D-%EC%BC%80%EC%9D%B4%EC%8A%A4%EA%B3%BC-%ED%95%B4%EA%B2%B0%EC%B1%85](https://velog.io/@jinyoungchoi95/JPA-%EB%AA%A8%EB%93%A0-N1-%EB%B0%9C%EC%83%9D-%EC%BC%80%EC%9D%B4%EC%8A%A4%EA%B3%BC-%ED%95%B4%EA%B2%B0%EC%B1%85)

[https://velog.io/@sweet\_sumin/JPA-N1-%EC%9D%B4%EC%8A%88%EB%8A%94-%EB%AC%B4%EC%97%87%EC%9D%B4%EA%B3%A0-%ED%95%B4%EA%B2%B0%EC%B1%85%EC%9D%80-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80%EC%9A%94](https://velog.io/@sweet_sumin/JPA-N1-%EC%9D%B4%EC%8A%88%EB%8A%94-%EB%AC%B4%EC%97%87%EC%9D%B4%EA%B3%A0-%ED%95%B4%EA%B2%B0%EC%B1%85%EC%9D%80-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80%EC%9A%94)

[https://www.baeldung.com/spring-hibernate-n1-problem](https://www.baeldung.com/spring-hibernate-n1-problem)