---
title: "[ Spring ] RestController vs Controller"
datePublished: Sat Mar 29 2025 15:00:00 GMT+0000 (Coordinated Universal Time)
cuid: cm7hl5omy000009l8dz4qfkd8
slug: spring-mvc
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1740308627094/7732e310-d1c0-4b71-8d44-427982db500e.png
tags: mvc

---

> 목표 : MVC 구조 살펴보기  
> Spring MVC에서 클라이언트의 요청 처리 흐름을 @Controller와 @RestController의 차이점을 중심으로 각각의 처리 과정과 특징에 대해 알아보자. 또한, RestController에서 HTTP 요청이 처리되어 응답으로 변환되는 전체 과정을 확인하자. (HTTP 메시지 컨버터가 동작하는 시점과 역할을 포함)

# 1️⃣ Servlet

Http의 요청과 응답을 위한 필요한 정보 파싱하는 것을 도와주는 도구

### `HttpRequestServlet`

* Http 요청 메시지를 파싱한 결과가 담긴 객체
    
* StartLine : URI, URL, 프로토콜, scheme, 보안유무..등
    
* Header : host, connection상태, 캐시, 접속창 정보….등
    
* 기타 : remote, Local정보
    

### `HttpResponseServlet`

* Http 응답 메시지를 파싱한 결과가 담긴 객체
    
* setHeader : 어떤 타입의 응답인지 설정
    
* setCharacterEncoding : 응답 본문 인코딩 설정
    

### `클라이언트 → 서버 요청메시지 전달방법`

1. GET : url파라미터로 전달, HTTP 메시지바디 사용x→content-type존재x
    
2. POST : html을 HTTP형식으로 변환 후 메시지 바디에 파라미터 형식으로 전달
    
    * content-type : www-form-urlencoded(쿼리 파라미터 조회 메서드 적용o)
        
3. HTTP메시지 바디 : 직접 전달 (ex)JSON
    

```java
@WebServlet(name = "helloServlet",urlPatterns = "/hello")
public class HelloServlet extends HttpServlet {
    @Override //url호출하면 service 실행
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //요청 url파라미터에서 username이름 갖고오기,값 하나일때만 사용, 중복이면 첫번째 값 반환
        String username = req.getParameter("username");
        String[] usernames = req.getParameterValues("username");//값 중복일때
        System.out.println("username = " + username);
        resp.setContentType("text/plain"); 
        //resp.setContentType("text/html"); //html응답
        resp.setCharacterEncoding("UTF-8"); //형식 지정
        resp.getWriter().write("hello " + username); //응답
    }
}
------------------------------------------------------------------------------------
@WebServlet(name = "requestBodyJsonServlet",urlPatterns = "/request-body-json")
public class RequestBodyJsonServlet extends HttpServlet {
    private ObjectMapper objectMapper = new ObjectMapper();//json을 객체로 매핑
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        ServletInputStream inputStream = req.getInputStream(); //byte로 데이터 읽어옴
        String messageBody = StreamUtils.copyToString(inputStream, StandardCharsets.UTF_8);//byte데이터를 어떤형식으로 읽을지 설정
        System.out.println("messageBody = " + messageBody);
        HelloData helloData = objectMapper.readValue(messageBody, HelloData.class);//json->객체
        System.out.println(helloData.getUsername());
        System.out.println(helloData.getAge());
        //응답
        resp.setHeader("content-type","application/json");//자동 utf8설정으로 application/json;utf-8설정x
        resp.setCharacterEncoding("UTF-8");//응답 본문 인코딩 방식 설정
        HelloData data = new HelloData();
        data.setAge(18);
        data.setUsername("hello");
        String result = objectMapper.writeValueAsString(data);//객체->json
        resp.getWriter().write(result);
    }
}
```

# 2️⃣ Model View Controller

HttpResponseServlet을 이용하여 w.write(“&lt;html&gt;”)처럼 html문서를 작성할 수 있지만 너무 복잡하다. 이를 위해 템플릿 엔진이 사용된다. 하지만 템플릿엔진에 비즈니스 로직과 html 코드가 섞여있으면? 수정 라이프사이클이 달라서 유지보수하기 어렵다. 이를 해결하기 위해 MVC패턴이 탄생하였다.

M : 뷰에 전달하기 위한 데이터 담음 (HttpRequestServlet 안에 데이터 저장소 존재)

V : 화면 렌더링

C : 비즈니스 로직 호출 , Http 파라미터 검증, 모델을 뷰에 전달

(S) : 비즈니스 로직 수행

```java
public class MvcMemberListServlet extends HttpServlet {
    private MemberRepository memberRepository = MemberRepository.getInstance();
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("Listservlet");
        List<Member> members = memberRepository.findAll();
        req.setAttribute("members", members);//members이름으로 데이터들을 모델에 저장
        String viewPath = "/WEB-INF/views/members.jsp";
        //모델을 뷰에 전달하기 위한 작업
        RequestDispatcher dispatcher = req.getRequestDispatcher(viewPath);//이동경로 설정
        dispatcher.forward(req, resp);//다른 서블릿으로 이동, 서버 내부에서 호출로 redirect와 달리 클라이언트가 인지 못함
    }
}
```

```java
public abstract class FrameworkServlet extends HttpServletBean implements ApplicationContextAware {
//HttpServlet의 service함수 구현
protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        if (HTTP_SERVLET_METHODS.contains(request.getMethod())) {
            super.service(request, response);
        } else {
            this.processRequest(request, response);
        }

    }
}

//DispatcherServlet이 service함수사용
public class DispatcherServlet extends FrameworkServlet {
    protected void doDispatch(HttpServletRequest request, HttpServletResponse response) throws Exception {
            HttpServletRequest processedRequest = request;
            HandlerExecutionChain mappedHandler = null;
            ModelAndView mv = null;//뷰이름으로 mv생성후 mv의 모델에 데이터 담아서 보관
            mappedHandler = this.getHandler(processedRequest);//컨트롤러 찾기
            if (mappedHandler == null) {
                 this.noHandlerFound(processedRequest, response);
                 return;
           }
           HandlerAdapter ha = this.getHandlerAdapter(mappedHandler.getHandler());//컨트롤러 실행시킬 어댑터 찾기
           mv = ha.handle(processedRequest, response, mappedHandler.getHandler());//컨트롤러 실행-> 뷰이름으로된 mv의 모델에 데이터 담긴 후 반환
           processDispatchResult(processedRequest, response, mappedHandler, mv, dispatchException);
        }
    private void processDispatchResult(HttpServletRequest request, HttpServletResponse response, @Nullable HandlerExecutionChain mappedHandler, @Nullable ModelAndView mv, @Nullable Exception exception) throws Exception {
        render(mv, request, response); 
    }
    protected void render(ModelAndView mv, HttpServletRequest request, HttpServletResponse response) throws Exception {
         String viewName = mv.getViewName();
         View view;
         view = this.resolveViewName(viewName, mv.getModelInternal(), locale, request);
         view.render(mv.getModelInternal(), request, response);
    }
}

public interface View {//다양한 구현체 존재(InternalResourceView=RequestDispatcher로 서블릿 이동,TymleafView도 존재)
    void render(@Nullable Map<String, ?> model, HttpServletRequest request, HttpServletResponse response) throws Exception;
}
```

RequestMappingHandlerMapping(url과 컨트롤러 매핑) 이랑 RequestMappingHanlderAdapter(매핑된 컨트롤러 실행→mv반환)은 스프링이 지원해줌/ 따라서 HandlerMapping과 HandlerAdapter는 스프링이 자동으로 해준다는 말임

우선 DispathcerServelet에서 모델을 만들어서파라미터와 함께 컨트롤러로 전달 →컨트롤러에서 기능 수행 후 모델에 데이터 담고 뷰이름 반환 → 전해준 모델에 값이 들어있고 뷰이름을 받았으니 뷰이름을 뷰 리졸버로 물리이름으로 변환→ 뷰에서 모델을 req.setattribute로 HttpReqeustServlet에 담아주고 화면 렌더링작업→ 화면 반환

# 1️⃣ Controller 관점

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740308897699/cbc0ecab-c4f8-43cf-aab9-1a2a8597e56a.png align="center")

애플리케이션을 Model View Controller의 주요 컴포넌트로 나누어 개발하는 방식이다. 각 레이어마다 역할이 다르다. Main메서드는 MVC객체를 각각 생성한다. Controller를 통해 Model의 데이터를 업데이트하고 View를 통해 사용자에게 보여준다.

1. Model : 비즈니스 로직과 데이터 표현을 담당한다. 데이터의 상태를 관리하고 조작하는 기능을 제공한다.
    
2. View : 사용자에게 정보를 표시하는 역할이다. 사용자와의 상호작용을 처리한다.
    
3. Cotroller : Model-View사이 상호작용을 제어한다. 사용자의 입력을 받아 Model을 업데이트 하고, 업데이트된 Model을 View에 전달한다. 사용자의 요청을 해석하고 적절한 동작을 수행한다.
    

---

# 2️⃣ RestController 관점

위 Controller로 View를 반환하거나 ResponseBody로 Json형태로 데이터를 반환할 수 있다.

RestController은 View를 반환하지 않고 단순히 객체만을 **JSON또는 XML형식으로 반환**한다.

## 🙋🏻‍♀️ HTTP 요청-응답으로 변환되는 과정

Http 메세지 컨버터

JSON데이터를 HTTP 메세지 바디에 직접 읽거나 쓰는 경우 사용

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1740998440610/15cd2e59-9784-4650-934e-02b1a45c51eb.png align="center")

1. RequestMapping핸들러 어댑터가 ArgumentResolver호출
    
2. ArgumentResolver가 컨트롤러가 필요하는 파라미터 객체를 Http메세지 컨버터를 이용해서 생성한다.
    
3. RequestMapping핸들러어댑터가 컨트롤러 호출하면서 파라미터 객체를 넘겨준다.
    
4. ReturnValue핸들러가 Http메세지 컨버터를 호출해서 응답값을 변환하고 반환한다.
    

## 👉🏻 차이점

Controller = View 반환

RestController = HttpMessageConverter를 통해 JSON이나 XML형식으로 반환