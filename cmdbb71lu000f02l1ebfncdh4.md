---
title: "[Project] 🌤️ 날씨에 맞는 옷 추천 서비스를 위한 Gemini API 도입기(with.Spring)"
datePublished: Sat Aug 16 2025 15:00:00 GMT+0000 (Coordinated Universal Time)
cuid: cmdbb71lu000f02l1ebfncdh4
slug: project-gemini-api-withspring
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1752993966338/d9ad8be1-e645-47f4-91ba-4f995f94fab2.png
tags: api, springboot, gemini

---

## 👋 글을 쓰게 된 이유

날씨에 따라 적절한 옷차림을 추천해주는 서비스를 만들기 위해 **Google의 Gemini API**를 도입하게 되었습니다. 이 과정에서 API 키를 발급받는 초기 단계부터 기존과 다른 UI나 문서 구조로 인해 혼란을 겪었습니다.

구글링으로 찾은 정보들이 현재와 맞지 않거나 생략된 경우가 많아, `2025년 기준으로 Gemini API를 Spring 프로젝트에 연동`하는 방법을 정리해보고자 합니다.

---

### 📍 예전엔 이렇게 보였다?

많은 블로그나 튜토리얼에서는 `Create API key in new project` 옵션을 클릭해서 키를 쉽게 발급받는 모습을 보여줍니다. 하지만 최근에는 이 UI가 바뀌었거나, 키 발급 절차 자체가 통합 또는 변경된 것으로 보였습니다.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1752991006905/16aa01ff-e022-451d-9357-2c532201007c.png align="center")

---

## 🔐 API Key 발급 & 삽질기

### 📌 1단계: 프로젝트 선택

앞서 언급했듯이 예전에는 "Create API key in new project" 버튼 하나면 쉽게 끝났던 과정이었습니다. 하지만 **2025년 기준**으로는 더 이상 그런 선택지가 없습니다. 반드시 **기존 프로젝트 중 하나를 선택하거나, 직접 새 프로젝트를 생성한 후 진행**해야 합니다.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1752990922621/773c5e6c-d7e5-49bb-97bf-0a89568ec60c.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1752990949208/35bfbb43-44c1-450f-9c36-0fcd024dbbdf.png align="center")

### 🛠️ 2단계: 프로젝트 만들기 & API 사용 설정

1. Google Cloud Console에 접속합니다.
    
2. 상단 메뉴에서 **프로젝트 선택 → 새 프로젝트 만들기**를 클릭합니다.  
    (저는 조직을 따로 선택하지 않고 만들었습니다.)
    
3. ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1752991379873/378f6473-ada8-471a-98ec-7fbe75fe76c2.png align="center")
    
    새 프로젝트가 생성되면, **검색창에** `Gemini API`를 입력하여 API를 찾아줍니다.
    
4. Gemini API 페이지에서 **‘사용’ 버튼을 클릭**합니다.
    
5. 몇 분 기다리면 사용 설정이 완료됩니다.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1752991455649/6c6e69a4-e10d-4fe0-a35c-11f0418f896d.png align="center")

### 🔑 3단계: API 키 발급

1. 왼쪽 메뉴에서 `API 및 서비스` → `사용자 인증 정보(Credentials)`로 이동합니다.
    
2. 상단의 `사용자 인증 정보 만들기 → API 키`를 클릭하면 바로 키가 발급됩니다.
    

이렇게 하면 **Gemini API를 호출할 수 있는 키**를 손에 넣게 됩니다.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1752991863450/ae09c345-35fc-4754-a9b6-8f1d891068d1.png align="center")

## ⚠️ 삽질로그: 403 Forbidden 오류 발생

처음에 발급받은 API 키를 프로젝트에 적용하니 아래와 같은 오류가 발생했습니다.

`403 Forbidden: "Method doesn't allow unregistered callers (callers without established identity). Please use API Key or other form of API consumer identity to call this API."`

처음엔 요청 방식이 잘못된 줄 알고 코드를 여러 번 수정했지만, 원인은 `잘못된 API 키`를 사용하고 있었던 것이었습니다.

오류 원인을 해석해보면 Gemini API 요청에 API Key가 포함되지 않았거나, 잘못된 방식으로 포함된 것입니다.

### ❗ 실제 원인

* 저는 이미 **VertexAI**도 같은 계정에서 사용하고 있었는데,
    
* 무심코 **VertexAI용 API 키**를 사용해버린 것이 문제였습니다.
    

즉, Gemini API 호출에는 **Gemini API를 활성화한 프로젝트에서 발급받은 API 키**를 사용해야 합니다.

### ✅ 마지막 단계

이제 아까 Key 발급 받는 사이트에 들어가서 프로젝트 선택 후 키를 발급 받으면 끝입니다.

**Gemini API 사용 설정된 프로젝트** 선택

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1752992272443/bf477f73-9e67-4ce6-b652-b8c1c98dde5e.png align="center")

* 결과
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1752992936094/b9410471-38dc-4799-927e-0aa36f35d077.png align="center")

출처

%[https://languagestory.tistory.com/315] 

%[https://wewegh.tistory.com/116]