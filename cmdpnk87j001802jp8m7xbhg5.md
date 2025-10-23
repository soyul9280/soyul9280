---
title: "[Project] 날씨에 맞는 옷 추천 서비스 : 지그재그 크롤링 여정 기록 (1) ChromeDriver를 EC2에 설치하기"
datePublished: Wed Jul 30 2025 07:36:49 GMT+0000 (Coordinated Universal Time)
cuid: cmdpnk87j001802jp8m7xbhg5
slug: project-1-chromedriver-ec2
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1753843082352/c2452b33-97a4-4148-8ba4-750e5eee6aff.png
tags: ec2, cloudfront, selenium, jsoup, chrome-cj73auo4o0012c3wted1yb7a1

---

## ✍️ 작성하게 된 이유

무신사, 29cm는 `Jsoup`으로 충분히 크롤링이 가능했기 때문에, **ZigZag도 당연히 Jsoup으로 처리될 것**이라 생각했다. 무신사, 29cm와 마찬가지로 필요한 데이터는 모두 `<script>` 태그 안에 들어있었다.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1753850937721/7db85e3f-f5f3-469d-ac73-2e2710c40e25.png align="center")

하지만… **예상은 보기 좋게 빗나갔다.**

## 🧪 현상

### ✅ 로컬 크롤링 → 정상 작동

* Jsoup으로 `script` 태그 내에서 대표 이미지와 상품명을 잘 추출
    
* 로컬 환경에서는 아무 문제 없이 작동
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1753849280332/7a69bfe3-3f92-4a29-b870-9da8f23d17d4.png align="center")

### ❌ AWS EC2 배포 환경 → 실패

* **커스텀 예외**가 발생하며 크롤링 실패
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1753852680194/a28f9aa8-cc5d-463b-a1bd-7a1e4530dbf1.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1753852518815/e640c685-39e7-44e5-9906-ff47f3649574.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1753852534033/3cc6a570-cba5-4240-93c4-93e32667d2c7.png align="center")

* 처음엔 `User-Agent`가 의심되어 명시적으로 지정했다.
    
    `.userAgent("Mozilla/5.0 ... Chrome/137.0.0.0 ...")`
    
    하지만 여전히 실패했다.
    

## 🔍 원인 분석: EC2에서 직접 curl 시도

`curl -A "Mozilla/5.0 (Linux; Android 6.0; Nexus 5 Build/MRA58N) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/137.0.0.0 Mobile Safari/537.36" https://zigzag.kr/your-product-url`

📸 **결과: 403 Forbidden (CloudFront 에러)**

* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1753853315810/431d2de2-8855-4d35-89cf-f5bc7d398650.png align="center")
    
    AWS EC2의 공용 IP가 **ZigZag 측 CloudFront + WAF**에 의해 차단됨
    
* ZigZag는 **봇 탐지 또는 AWS IP 대역 제한**을 통해 접근을 막고 있었던 것
    

## 🧭 해결 방법을 고민하며

> 일단 프록시 서버를 써야 하나? VPN을 붙여야 하나? 라는 고민이 들었다.

하지만 크롤링 관련 커뮤니티 글 중 다음과 같은 조언을 확인했다:

링크 : [Ip 차단을 피하려면 어떻게 해야 하나요?](https://www.inflearn.com/community/questions/559948/ip-%EC%B0%A8%EB%8B%A8%EC%9D%84-%ED%94%BC%ED%95%98%EB%A0%A4%EB%A9%B4-%EC%96%B4%EB%96%BB%EA%B2%8C-%ED%95%B4%EC%95%BC-%ED%95%98%EB%82%98%EC%9A%94?srsltid=AfmBOoqNTsXBl0OuWol1kIXlrF9wF20o5GbeMYyFbBPmJIEeOXDE0xfp)

> “저도 크롤링을 막는 서비스를 강제로 우회해서 뚫는 시도는 사실 하고 있지 않거든요.
> 
> selenium 을 사용하면, 거의 사용자 환경을 그대로 구현하는 것이라서 조금 나을 것은 같은데요. 그렇지 않다면, 너무 이게 해킹과 유사한 느낌이라 저도 조금 애매합니다. 우선은 selenium 으로 한번 시도해보시는 것까지는 한번 해보시면 좋을 것 같고요.”

그래서 다시 Selenium을 도입해보기로 결정했다.

## 🐞 SessionNotCreatedException

이전 글 내용 중, EC2에서 `Selenium` 사용 시 다음과 같은 오류가 있었다.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1753854041829/f686ec3a-22ed-4c24-b305-49c08547df1d.png align="center")

먼저, EC2에 Google Chrome이 깔려있는지 확인해보았다.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1753857483694/a6738339-e2d4-4f3e-a22e-abe695157a35.png align="center")

원인 : EC2 인스턴스에 **Chrome이 설치되지 않아** Selenium이 내부적으로 Chrome을 실행하지 못해 발생한 에러였다.

## 🛠️ EC2에 Chrome + ChromeDriver 설치

현재 EC2정보이다.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1753854228735/4108f87a-4dfa-45e3-b530-38906d757de1.png align="center")

### 1️⃣ Chrome 설치

1. 필요한 라이브러리 설치 후 google-chrome 설치
    
    wget : 웹 서버로부터 콘텐츠를 가져오는 리눅스 명령어로 HTTP, FTP 등의 프로토콜 사용 가능, URL을 통해 간편하게 파일을 받을 수 있어 자주 쓰이는 명령어
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1753854430283/acb2aa43-4393-45e8-847f-b4f5855e2e8c.png align="center")

* 설치 확인: `google-chrome --version` (필자는 `138.0.7204.168`버전을 다운받았다.)
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1753854509867/08e7ba99-6cc3-4744-b84c-9e84f23d5099.png align="center")

### 2️⃣ ChromeDriver 설치

* 현재 Chrome버전과 맞는 chromedriver를 다운받았다.
    
* 상태확인 사이트 : [ChromeDriver버전 확인](https://googlechromelabs.github.io/chrome-for-testing/)
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1753854539131/e5d987f4-0822-4281-8b5b-7c0e903c76b5.png align="center")
    
* Google은 최근부터 **Chrome for Testing 전용 버전 체계를 도입**하여 다운로드 경로가 변경되었다.
    
    `chrome-for-testing-public` 경로에서 맞는 버전 다운로드
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1753856125983/eec38beb-72de-49ee-a207-81f736b1d305.png align="center")
    
    압축 해제 : `unzip ~/Downloads/chromedriver-linux64.zip`
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1753857641915/b9cab45a-ba1c-470b-aeea-f34d0051af2f.png align="center")
    
* 전역 경로 등록(`chromedriver`**실행 파일을 시스템에서 인식**할 수 있도록)
    
    `sudo mv ~/Downloads/chromedriver-linux64/chromedriver /usr/local/bin/`
    
    `sudo chmod +x /usr/local/bin/chromedriver` : 권한 부여
    

이렇게 하면 Selenium이 new ChromeDriver() 호출 시 자동으로 해당 경로의 드라이버를 사용한다.

## ❗ 여전히 실패: CloudFront 403

Selenium으로도 크롤링은 실패했다.  
다시 `curl`로 요청해본 결과, 동일한 403 에러가 발생했다.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1753858220445/3d5ff9de-0229-45c6-b194-0f8181116a9b.png align="center")

### 📌 원인 추정

* 동일한 고정 IP로 **지속적인 크롤링 시도 CloudFront + WAF 보호 정책으로 차단**
    
* **CloudFront + WAF 보호 정책**은 다음처럼 작동한다.
    
    * 반복 요청, 비정상적인 User-Agent, 쿠키 미존재 등 감지 시 자동 IP 차단
        

## 💡 이번 실패에서 배운 점

* 무리한 크롤링은 권장하지 않는다. → 정책 준수하기
    
* 로컬에서 된다고 서버에서도 된다는 보장은 없다.
    
* CloudFront + WAF는 단순한 User-Agent 조작만으로 우회할 수 없다
    
* 정적 HTML 구조라고 해도, 외부 요청을 차단할 수 있는 다양한 보안 장치가 존재한다.
    

출처

[Chrom](http://zigzag.kr)[eDriver버전 확인](https://googlechromelabs.github.io/chrome-for-testing/)

[Ip 차단을 피하려면 어떻게 해야 하나요?](https://www.inflearn.com/community/questions/559948/ip-%EC%B0%A8%EB%8B%A8%EC%9D%84-%ED%94%BC%ED%95%98%EB%A0%A4%EB%A9%B4-%EC%96%B4%EB%96%BB%EA%B2%8C-%ED%95%B4%EC%95%BC-%ED%95%98%EB%82%98%EC%9A%94?srsltid=AfmBOoqNTsXBl0OuWol1kIXlrF9wF20o5GbeMYyFbBPmJIEeOXDE0xfp)

[https://js990317.tistory.com/16](https://js990317.tistory.com/16)