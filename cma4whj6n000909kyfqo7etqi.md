---
title: "[Spring] 로그인 - 쿠키?세션?"
datePublished: Sat Jun 28 2025 15:00:00 GMT+0000 (Coordinated Universal Time)
cuid: cma4whj6n000909kyfqo7etqi
slug: spring
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1746001021360/46fffbe8-7089-432b-9da3-060357f3f126.png
tags: cookies, session

---

# 1️⃣ 도메인

시스템이 구현해야하는 핵심 비즈니스 업무 영역

web은 도메인을 알고있지만 도메인은 web을 모르도록 설계해야한다.

# 2️⃣ 쿠키

쿼리 파라미터를 계속 유지하면서 요청하면 매우 어렵고 번거롭다. 만약 서버에서 로그인에 성공하면 HTTP응답에 쿠키를 담아 브라우저에게 전달한다. 그러면 브라우저는 해당 쿠키를 지속해서 이용할 수 있다.

* 영속 쿠키 : 만료 날짜를 입력하면 해당 날짜까지 유지
    
* 세션 쿠키 : 만료 날짜를 생략하면 브라우저 종료까지만 유지
    

## **👨🏻‍🏫** 세션 쿠키

컨트롤러에서 쿠키를 설정할 수 있다.

1. 로그인
    

```java
    @PostMapping("/login")//로그인 성공시 세션쿠키 생성
    public String login(@Valid @ModelAttribute("loginForm") LoginForm loginForm,
                        BindingResult bindingResult, HttpServletResponse response) {
        Member login = loginService.login(loginForm.getLoginId(), loginForm.getPassword());
        //쿠키생성로직 -> 로그인 성공시 쿠키생성하고 response에 담아. 쿠키이름은 memberId
        //웹브라우저 종료전까지 회원 id를 서버에 계속 보내줌
        Cookie idCookie = new Cookie("memberId", String.valueOf(login.getId()));
        response.addCookie(idCookie);

        return "redirect:/";
    }

      @GetMapping("/")//cookievalue로 쿠키 조회, 로그인하지않은 사용자도 홈에 접근하니 required=false
      public String homeLogin(@CookieValue(name = "memberId",required = false) Long memberId,
                          Model model) {
      model.addAttribute("member", loginMember);//홈화면에 회원관련정보도 출력해야해서 member데이터도 담기
      return "loginHome";
    }
```

* 결과
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1745999398891/6dc08592-db76-4f8a-861b-2cb385cd4d0c.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1745999457232/040b67b9-5670-48d7-8d70-58c7f122c30f.png align="center")

2. 로그아웃
    

```java
@PostMapping("/logout")
    public String logout(HttpServletResponse response) {
        expireCookie(response,"memberId");
        return "redirect:/";
    }

    private void expireCookie(HttpServletResponse response, String cookieName) {
        Cookie cookie = new Cookie(cookieName, null);
        cookie.setMaxAge(0);//서버 해당 쿠키 종료 날짜 0지정
        response.addCookie(cookie);
    }
```

* 결과
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746000668341/72d61e9f-264c-43ff-8fb1-bb31c3a51db2.png align="center")

## ✅ 쿠키와 보안문제

* 클라이언트가 쿠키를 강제로 변경할 수 있다.
    
* 쿠키에 보관된 정보는 훔쳐갈 수 있다.  
    ( 정보들이 웹브라우저에 보관, 네트워크 요청마다 클라이언트에서 서버로 전달 → 내 PC 혹은 네트워크 전송구간에서 털림)
    
* 해커카 쿠키 훔쳐서 악의적인 요청이 가능하다.
    

## ✅ 쿠키와 보안문제 대안

* 예측 불가능한 토큰 값 노출하기  
    (서버에서 토큰과 사용자 id 매핑하고 서버에서 토큰관리)
    
* 시간이 지나면 사용할 수 없도록 토큰 만료시간을 짧게 유지
    

# 3️⃣ 세션

서버에 중요한 정보를 보관하고 연결을 유지하는 방법

세션ID에 값을 저장해두고 쿠키로 클라이언트와 연결된다.

* 서버 → 클라이언트 : 세션ID만 쿠키에 담아 전달
    
* 클라이언트 → 쿠키저장소 : 세션ID쿠키 보관
    
* 클라이언트 → 서버 : 세션ID쿠키 정보로 세션 저장소에서 조회 후 보관한 세션 정보를 사용
    

회원과 관련된 정보는 클라이언트에게 전달되지 않는다.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746002592193/9a5d175d-7cde-402e-aedf-b07d74ea7ee1.png align="center")

## **👨🏻‍🏫** HttpSession

서블릿을 통해 HttpSession을 생성하고 JSESSIONID이름의 쿠키를 사용한다.

```java
    @PostMapping("/login")
    public String loginV3(@Valid @ModelAttribute LoginForm form, BindingResult bindingResult, HttpServletRequest request) {
        HttpSession session = request.getSession(true); //없으면 신규 생성 false=없으면 null
        session.setAttribute(SessionConst.LOGIN_MEMBER, login);//세션에 데이터 보관, 하나의 세션에 여러 값 보관 가능
        return "redirect:/";
    }

    @PostMapping("/logout")
    public String logoutV3(HttpServletRequest request) {
        HttpSession session = request.getSession(false);
        if (session != null) {
            session.invalidate(); //세션 제거
        }
        return "redirect:/";
    }

    @GetMapping("/")
    public String homeLoginV3(HttpServletRequest request,Model model) {
        HttpSession session = request.getSession(false);
        if (session == null) {
            return "home";
        }
        //세션값찾기
        Member loginMember = (Member) session.getAttribute(SessionConst.LOGIN_MEMBER);
        if(loginMember == null) {
            return "home";
        }
        model.addAttribute("member", loginMember);
        return "loginHome";
    }
```

* session.setAttribute: 세션에 데이터 저장
    
* request.getSession : 세션 조회
    
* session.getAttribute : 세션에 보관한 데이터 조회
    
* 결과 (JSESSIONID 확인)
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746017888283/82777781-366f-44a1-b5e3-adf5fff1e481.png align="center")

## ✅ @SessionAttribute

세션 찾고, 세션의 데이터 찾는 복잡한 과정을 스프링에서 처리

```java
    @GetMapping("/")
    public String homeLoginV3Spring(
@SessionAttribute(name = SessionConst.LOGIN_MEMBER,required = false) Member loginMember,
  Model model) {
        if(loginMember == null) {
            return "home";
        }
        model.addAttribute("member", loginMember);
        return "loginHome";
    }
```

## ✅ 세션 유지 시간

사용자가 서버에 요청한 시간(LastAccessedTime) 기준 30분 설정

application.properties

```yaml
server.servlet.session.timeout=60 # 글로벌 설정 기본 1800 (30분)
session.setMaxInactiveInterval(1800) #1800초
```