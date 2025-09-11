---
title: "[Spring] 필터와 인터셉터"
datePublished: Thu May 29 2025 15:00:00 GMT+0000 (Coordinated Universal Time)
cuid: cmff49ihd000f02ju4bux6qeh
slug: spring-1
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1746863086554/c22dcc04-3450-4c4d-aa14-1ea2d46e0358.png
tags: filter, interceptors

---

# 1️⃣ 필터

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1757574037704/7d162db5-a6e2-4cae-89ec-257c16b1a0f2.png align="center")

웹과 관련된 공통 관심사를 처리할 때 사용하며 서블릿이 제공하는 수문장이고 웹 컨테이너에서 동작한다. 디스패처 서블릿 전/후에 처리한다.

cf)애플리케이션 여러 로직에서 공통으로 관심 있는 것을 공통 관심사라고 한다.

<div data-node-type="callout">
<div data-node-type="callout-emoji">💡</div>
<div data-node-type="callout-text">HTTP요청 → WAS → 필터1→필터2 → (디스패처)서블릿 → 컨트롤러</div>
</div>

* 특정 URL패턴에 적용 가능하다.
    

* 필터는 체인으로 구성돼있어 중간에 필터를 추가할 수 있다.
    

* 서블릿 컨테이너가 싱글톤 객체로 생성하고 관리한다.
    
* HTTP요청 및 응답에 대한 처리를 수행한다.
    
* MVC 전이기 때문에 예외가 발생하면 따로 처리해줘야한다.
    

## ✅ 필터 인터페이스

```java
public interface Filter {
    default void init(FilterConfig filterConfig) throws ServletException {
    }

    void doFilter(ServletRequest var1, ServletResponse var2, FilterChain var3) throws IOException, ServletException;

    default void destroy() {
    }
}
```

* init : 필터 초기화 메서드이다. 서블릿 컨테이너가 생성될 때 호출된다. 웹 컨테이너가 1회 init 메소드를 호출하여 필터 객체를 초기화하면 이후의 요청들은 doFilter를 통해 처리된다.
    
* doFilter : 요청 올 때 마다 호출된다. 필터의 로직을 구현한다[.](https://mangkyu.tistory.com/173) doFilter의 파라미터로는 FilterChain이 있는데, FilterChain의 doFilter 통해 다음 대상으로 요청을 전달하게 된다.
    
* destroy : 필터 종료 메서드이며 서블릿 컨테이너가 종료될때 호출된다. 웹 컨테이너에 의해 1번 호출되며 이후에는 이제 doFilter에 의해 처리되지 않는다.
    

## ✅ 필터 구현체 예시

```java
@Slf4j
public class LoginCheckFilter implements Filter {
    private static final String[] whiteList={"/","/members/add","/login","/logout","/css/*"};
    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        HttpServletRequest httpRequest = (HttpServletRequest) servletRequest; //ServletRequest : Http요청이 아닌 경우까지 고려해서 만든 인터페이스
        String requestURI = httpRequest.getRequestURI();
        HttpServletResponse httpResponse = (HttpServletResponse) servletResponse;
        try {
            log.info("인증 체크 로직 실행 {}", requestURI);
            HttpSession session = httpRequest.getSession(false);
            if (session == null || session.getAttribute(SessionConst.LOGIN_MEMBER) == null) {
                log.info("미인증 사용자 요청 {}", requestURI);
                httpResponse.sendRedirect("/login?redirectURL=" + requestURI);
                return; //중요
            }
            filterChain.doFilter(servletRequest, servletResponse); //다음 필터가 있으면 호출, 없으면 서블릿 호출
        } catch (Exception e) {
            throw e;
        }finally {
            log.info("인증 체크 필터 종료 {}", requestURI);
        }
    }
    //화이트 리스트 일 경우 인증 체크 안함
    private boolean isLoginCheckPath(String requestURI) {
        return !PatternMatchUtils.simpleMatch(whiteList, requestURI);
    }
}
```

## ✅ 필터 등록

```java
@Configuration
public class WebConfig {
    @Bean
    public FilterRegistrationBean logFilter() {
        FilterRegistrationBean<Filter> filterRegistrationBean = new FilterRegistrationBean<>();
        filterRegistrationBean.setFilter(new LoginCheckFilter()); //등록 필터를 지정한다.
        filterRegistrationBean.setOrder(1);//여러 필터 중 숫자가 낮을수록 먼저 동작한다.
        filterRegistrationBean.addUrlPatterns("/*"); //필터를 적용할 URL 패턴을 지정한다.
        return filterRegistrationBean;
    }
}
```

+)@ServletComponentScan @WebFilter로도 필터 등록이 가능하지만 순서 조절이 안된다.

# 2️⃣ 인터셉터

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1757574428873/b9dddac4-805b-429d-bfb6-256e52728abb.png align="center")

스프링 컨텍스트에서 동작하고 스프링 MVC가 제공하는 기술이다. 인터셉터는 스프링 MVC가 제공하는 기술이기 때문에 서블릿 뒤에 온다.

<div data-node-type="callout">
<div data-node-type="callout-emoji">💡</div>
<div data-node-type="callout-text">HTTP요청 → WAS → 필터 → (디스패처)서블릿 → 스프링 인터셉터 → 컨트롤러</div>
</div>

* URL패턴 : 서블릿과 다르고 매우 정밀하게 설정할 수 있다.
    
* 체인으로 구성되어 있어 중간에 추가할 수 있다.
    
* 서블릿 필터보다 편리하고 정교하여 다양한 기능을 지원한다.
    

## ✅ 인터셉터 인터페이스

```java
public interface HandlerInterceptor {
    default boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        return true;
    }

    default void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, @Nullable ModelAndView modelAndView) throws Exception {
    }

    default void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, @Nullable Exception ex) throws Exception {
    }
}
```

* preHandle : 핸들러 어댑터 호출 전에 호출된다. 즉, 컨트롤러 이전에 처리해야하는 전 처리 작업이나 요청 정보를 가공하거나 추가하는 경우 사용할 수 있다. true여야 다음으로 진행한다.
    
* postHandle: 핸들러 어댑터 호출 후 호출된다. 즉, 컨트롤러 호출 후 실행된다. 컨트롤러 이후 처리해야하는 후처리 작업이 있을 때 사용한다.
    
* afterCompletion : 뷰가 렌더링 된 이후에 호출된다. 예외가 발생해도 항상 호출된다. 사용한 리소스를 반환할 때 사용하기에 적합하다.
    

cf)AOP를 적용할 수 있지만 다음과 같으 ㄴ이유들고 컨트롤러 호출 과정에 적용되는 부가기능들은 인터셉터를 사용하는 편이 낫다.

1. 컨트롤러는 타입과 실행 메서드가 모두 제각각이라 포인트컷의 작성이 어렵다
    
2. 컨트롤러는 파라미터나 리턴값이 일정하지 않다.
    
3. AOP는 HttpServletRequest/Response 객체를 얻기 어렵지만 인터셉터에서는 파라미터로 넘어온다.
    

## ✅ 인터셉터 구현체 예시

```java
@Slf4j
public class LoginCheckInterceptor implements HandlerInterceptor {// 인증은 컨트롤러 호출 전에만 호출하면 되니까 이것만 구현
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        String requestURI = request.getRequestURI();
        log.info("인증체크 인터셉터 실행{}", requestURI);
        HttpSession session = request.getSession(false);
        if(session == null || session.getAttribute(SessionConst.LOGIN_MEMBER)==null){
            log.info("미인증 사용자 요청");
            response.sendRedirect("/login?requestURL=" + requestURI);
            return false;
        }
        return true;
    }
}
```

* 인증은 컨트롤러 호출 전에만 호출하면 돼서 preHandle만 구현하면 된다.
    

## ✅ 인터셉터 등록

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {
     @Override
   public void addInterceptors(InterceptorRegistry registry) {
       registry.addInterceptor(new LogInterceptor())//인터셉터 등록
               .order(1)//낮을 수록 먼저 호출
               .addPathPatterns("/**")//적용 패턴 지정
               .excludePathPatterns("/css/**", "/*.ico", "/error");//제외 패턴 지정
       registry.addInterceptor(new LoginCheckInterceptor())
               .order(2)
               .addPathPatterns("/**")
               .excludePathPatterns(
                       "/","/members/add","login","/logout",
                       "/css/**","/*.ico","/error"
               );
   }
}
```

서블릿 필터와 비교하면 매우 편리하다. 경로를 정교하게 지정할 수 있다.

# 3️⃣ 언제 사용하지

선택의 가장 큰 기준은 **어떤 시점에 사용할 것인가**가 중요한 것 같다.

**필터는 서블릿에서 처리하는 과정의 전후를 다룰 수 있으므로 스프링과 무관하게 전역적으로 처리하는 작업들을 수행**할 때 사용할 수 있을 것이다. 예를 들어 모든 요청의 로그를 남기거나, 인증 및 권한 부여, 캐싱을 할 수 있다.

**인터셉터는 스프링 내부에서 컨트롤러가 처리하는 과정의 전후를 다룰 수 있으므로 컨트롤러에 넘겨주는 데이터를 가공하거나 처리**할 때 사용할 수 있다. 예를 들어 토큰 유효성을 검사하거나, 인증 및 권한 부여, 캐싱을 할 수 있다.

### 참고자료

[https://dd-developer.tistory.com/111](https://dd-developer.tistory.com/111)

[https://mangkyu.tistory.com/173](https://mangkyu.tistory.com/173)