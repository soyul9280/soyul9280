---
title: "[Project] 날씨에 맞는 옷 추천 프로젝트: Selenium은 정말 필요한 선택이었을까? - 크롤링 삽질 기록"
datePublished: Mon Jul 28 2025 08:44:26 GMT+0000 (Coordinated Universal Time)
cuid: cmdmv3gzh000302kyfwky3tjv
slug: project-selenium
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1753343114273/b32cc35e-a2e6-4085-a132-26c72f8792d9.png
tags: selenium, jsoup

---

## ✍️ 작성하게 된 이유

날씨에 따라 옷을 추천해주는 서비스를 만들면서, 사용자가 입력한 **구매 링크에서 옷 정보( 대표이미지, 상품명 )를 불러오는 기능**이 필요했다. 처음에 해당 페이지를 **동적 페이지**로 판단했고, 자연스럽게 `Selenium`을 도입했다.

하지만 이 결정이 과연 최선이었는지는 수많은 시행착오 끝에야 알 수 있었다.

## 🕸️ Selenium을 선택한 이유

**동적 페이지**는 `Jsoup`으로 크롤링이 어렵다는 인식으로 처음부터 `Selenium`을 선택했다. `Selenium`은 **브라우저 환경**을 구동하여 실제 동작하는 것 처럼 행동할 수 있다. 대부분의 사이트들은 동적페이지라는 인식이 있어 해당 라이브러리를 사용했다.

> "정적 페이지 = Jsoup, 동적 페이지 = Selenium"이라는 단순한 기준으로 접근했다.

### 🧪 그런데 실제 사용해보니?

### 1️⃣ 너무 느리다 (최소 12초)

* 예시로 29cm 페이지를 보면, 다른 작업이 완료되지 않고 단순히 크롤링 대상 페이지만 로드하는 데도 **3초**, 전체 요청은 **12초** 이상 소요되었다.
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1753344138549/b3dbf361-3fa6-4331-86cd-443d35150b62.png align="center")
    
    29cm 페이지 기준 한 번 요청당 **12초**가 소요됨
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1757895164204/3d7858c0-64fc-40c9-ba89-84ad3c2e1a0a.png align="center")

### 2\. 서버 환경에선 오류 발생

로컬에서는 정상적으로 작동하던 코드가 **AWS 환경에 배포**하자 다음과 같은 오류를 뿜기 시작했다.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1753343569326/8c9df29f-a53b-45aa-ab3d-e007b89b40b9.png align="center")

→ 이는 Selenium WebDriver가 Chrome 브라우저 인스턴스를 생성하지 못해서 발생한 오류였다.

### 3\. OOM (Out Of Memory)까지 발생

* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1753766332081/eb54176b-7836-42ad-8271-bbafc4c353f7.png align="center")
    
* 상황
    
    t2.micro(프리티어)인스턴스의 메모리 기준 1GiB 중 70% 이상 사용됨(RAM 용량 부족)
    
    그에 따라, OOM이 발생하여, 인스턴스가 부하를 버티지 못해 종료하게 됨
    
    남아있는 부하로 인하여 Rolling 재배포 시도에도 OOM발생으로 문제 발생 이후 재배포 또한 실패하는 상황 발생
    
* 원인
    
    매 요청 마다 Chrome 인스턴스를 매번 새로 생성( 밑 코드 예시 참고 ) → Chrome 브라우저 로드 후 크롤링 진행
    
    정보를 가져오는 양이 많고 요청이 동시에 여러개 들어오게 되면서 메모리 사용량 폭증
    
    GUI 없이 실행해도 크롬은 상당한 메모리를 사용
    
* 해결 방법
    
    배포 인스턴스 업그레이드 ( t2.micro → t3a.medium) : vCPU 1→2(2배 증가), 메모리 1GiB→4G1B(4배증가)
    
    Jsoup으로 변환 ( 밑 방법 참고 )
    

## 🧠 크롬이 메모리를 많이 사용하는 이유?

크롬은 각 탭마다 **독립적인 프로세스**를 생성해 빠른 속도를 확보하는 구조이다. 이 구조는 메모리가 넉넉할 때 다중 탭(하나의 창에 여러 탭 = 프리렌더링)을 활용하여 빠르지만, **메모리가 부족한 서버 환경**에서는 오히려 느려지고 오류가 발생할 수 있다.

> 프리렌더링 기술은 메모리가 넉넉할 땐 빠름, 그렇지 않으면 성능 저하!

## 🔍 현재 프로젝트 코드로 본 문제점

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1753343795889/1025a5cb-dbc8-48bc-8a58-d54883e3e034.png align="center")

* 매번 Chrome 브라우저를 새로 실행하므로, 메모리 낭비
    
* Headless 모드라도 자원을 크게 소모
    
* 대량 요청 시 리소스 누수 및 OOM 위험 증가
    
* 많은 요청이 동시에 들어오면 GC 되기 전에 크롬 인스턴스가 계속 늘어남
    

## 🤔 정말 Selenium이 필요했을까?

Selenium의 장점은 분명하다.

✅ 로그인, 클릭, 스크롤 등 사용자 조작 자동화  
✅ 자바스크립트 실행 이후 DOM까지 접근 가능  
✅ 실제 브라우저와 유사한 환경 제공

하지만, 내가 만든 기능은?

* 사용자는 **URL만 입력**
    
* 로그인, 클릭, 스크롤 ❌
    
* 필요한 데이터( 대표이미지 URL, 상품명 )는 **HTML 내 script 태그 안**에 모두 존재  
    → 즉, **렌더링이 완료될 때까지 기다릴 필요가 전혀 없음**
    

## 📚 Jsoup으로 전환 결심

### 🟢 Jsoup이란?

`Jsoup`은 Java 기반 HTML 파서로, 다음과 같은 특징이 있다:

* 간단한 API로 HTML 가져오기 (URL, 파일, 문자열 등)
    
* DOM 탐색 / CSS 선택자로 데이터 추출
    
* HTML 요소, 속성, 텍스트 조작 가능
    
* 깨진 HTML도 자동 보정
    
* 정제된 HTML 출력 지원
    

## 🔎 29cm 페이지 분석

구매 링크로 들어가면 `대표 이미지 URL`, `상품명` 등 필요한 정보는 모두 `<script>` 태그 내부에 **정적 데이터**로 존재했다.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1753345849876/3b2ca524-2807-410c-a1f4-b2718174eb17.png align="left")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1753345855816/a9ef6a9a-48d9-4b23-8e10-e30cf62613b7.png align="center")

👉 즉, `Jsoup`만으로도 충분히 접근 가능  
👉 별도의 렌더링이나 브라우저 제어 필요 없음

## 🛠️ Jsoup 적용 방법

### 1️⃣ 의존성 추가

> Gradle에서 `jsoup` 라이브러리 추가

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1753508695989/5a84645e-19c7-4bbf-8997-4ef1fc664760.png align="center")

### 2️⃣ HTML 문서 가져오기

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1753508904494/9c516a46-b984-4a42-83ac-6aa3089de1dc.png align="center")

* `tiemout`: 대기시간은 15초이다. 15초가 넘어가면 에러가 난다.
    
* `userAgent`: 웹 브라우저가 서버에 요청을 보낼 때 자신의 정보를 담아 보내는 문자열이다.
    
* `parser.extract(document)`: html문서 전체를 extranct 함수로 넘겨준다. 각 사이트에 따른 정보 추출을 진행한다.
    
* 필자는 Spring Retry를 사용했기에 e 그대로 예외를 던진다.
    

### 3️⃣ 원하는 요소 파싱(29cm)

* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1757895365397/b469d67b-0ca6-4b6d-8731-3b2a88d63a6b.png align="center")
    
    `Document`: HTML 전체 문서 객체
    

* `Elements`: Element들의 리스트
    
* `Element`: HTML DOM 요소 하나
    
* 필자는 사이트별 Parser를 따로 두고 파싱함
    

## ⚡ 결과: 속도 개선 : 기존 대비 <mark>약 40배의 크롤링 시간 감소</mark>

| 대상 사이트 | 기존(Selenium) | 변경 후(Jsoup) |
| --- | --- | --- |
| 29cm | 12초 | 0.3초 |

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1757895430242/69207592-7aa3-4bd3-8796-090f58ff152d.png align="center")

## ✅ 결론

이번 경험을 통해 깨달은 점

> **"Selenium을 쓰기 전에 진짜로 브라우저 조작이 필요한지 먼저 확인하자!"**

단순히 HTML 내 데이터 추출이라면,  
가벼운 도구를 사용하는 것이 **속도**, **안정성**, **운영 효율성** 모두에서 더 낫다.

출처

1. [ChromeDriver의 메모리 누수현상](https://blog.naver.com/altmshfkgudtjr/221664887420)
    
2. [Jsoup 공식 문서](https://jsoup.org/)
    

%[https://macguyver.kr/707]