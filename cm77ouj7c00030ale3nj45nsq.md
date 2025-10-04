---
title: "[Spring] Bean 등록 방법"
datePublished: Sat Mar 15 2025 15:00:00 GMT+0000 (Coordinated Universal Time)
cuid: cm77ouj7c00030ale3nj45nsq
slug: spring-bean
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1739713261231/d9cce2cc-0ebd-416f-92af-6a396a8104e2.png

---

> 목표 : Spring Boot에서 사용되는 다양한 Bean 등록 방법, 각각의 장단점을 비교하기.
> 
> (출처 : [https://mimah.tistory.com/entry/Spring-%EC%8A%A4%ED%94%84%EB%A7%81-%EB%B9%88%EC%9D%84-%EB%93%B1%EB%A1%9D%ED%95%98%EB%8A%94-%EB%91%90-%EA%B0%80%EC%A7%80-%EB%B0%A9%EB%B2%95#google\_vignette](https://mimah.tistory.com/entry/Spring-%EC%8A%A4%ED%94%84%EB%A7%81-%EB%B9%88%EC%9D%84-%EB%93%B1%EB%A1%9D%ED%95%98%EB%8A%94-%EB%91%90-%EA%B0%80%EC%A7%80-%EB%B0%A9%EB%B2%95#google_vignette),김영한 - Spring강의)

# 1️⃣ 스프링 컨테이너 이용

```java
@Configuration
public AppConfig(){
    @Bean
    public MemberService memberService(){
        return new MEmberServiceImpl(memberRepository());
    }
}
```

```java
ApplicationContext ap= new AnnotationConfigApplicationContext(AppConfig.class);
```

AppConfig.class 정보를 사용해서 스프링 빈을 등록한다.

빈 이름은 메서드 이름이며 빈 이름을 직접 등록할 수도 있다.@ Bean(name=””)

---

# 2️⃣ @ Component, @ Autowired 사용

```java
@Component
public class MemberServiceImpl implements MemberService{
    private final MemberRepository memberRepository;
    @Autowired
    public MemberServiceImpl(MemberRepository memberRepository){
        this.memberRepository=memberRepository;
    }
}
```

(@ ComponentScan은 @ SpringBootApplication안에 들어가있어서 main클래스에서 자동으로 @ Componet를 스캔한다.)

@ Componet가 붙은 모든 클래스들을 스프링 빈으로 등록한다.

빈의 기본이름은 클래스명을 사용하는데 맨 앞 글자만 소문자를 사용한다.

@ Autowired로 의존관계를 자동주입 시켜준다.

---

# 3️⃣ 비교

컴포넌트 스캔 방법이 더 편리하긴 하지만 직접 스프링 빈을 등록하여 관리하는 장점도 있긴 하다.

**외부 라이브러리 사용** 시 @ Bean으로 클래스를 등록해줘야 하는 경우 때문에 사용되기도 하고, SpringConfig 파일에서 한눈에 스프링 빈 객체가 어떤 게 등록되어 있는지 파악하기 쉽다는 장점이 있다.