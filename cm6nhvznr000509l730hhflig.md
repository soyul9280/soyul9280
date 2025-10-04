---
title: "[JAVA] O(n) vs O(log n)"
datePublished: Sat Feb 15 2025 15:00:00 GMT+0000 (Coordinated Universal Time)
cuid: cm6nhvznr000509l730hhflig
slug: java-on-vs-olog-n
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1738492027198/93284065-0a23-4b25-80dd-1cb665f18f7e.png
tags: on

---

> 목표 : **O(n)과 O(log n)의 성능 차이를 실생활 예시를 들어 확인하고, 데이터의 크기가 1백만 개일 때 각각 대략 몇 번의 연산이 필요한지 비교하기.**
> 
> (사진 출처 : [https://cordcat.tistory.com/75](https://cordcat.tistory.com/75))

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1738492486462/ab35666a-2d13-4b95-bd82-a7b46b539800.png align="center")

# 1️⃣ O(n)

선형 시간이다. 데이터에 비례하여 시간이 늘어난다.

실생활 예시 : 책이 정렬되지 않은 도서관에서 찾고 싶은 책을 하나하나 비교하며 찾을때

데이터 크기 1000000일때 : 최악의 경우 1000000번 확인해야 할 수 있다.

---

# 2️⃣ O(log n)

단순 순환이 아닌 1/2씩 줄여가며 탐색한다.

실생활 예시 : 제목순으로 책이 정렬된 도서관에서 특정 책을 찾을 때, 특정 책의 제목이 중간에서 앞인지 뒤인지 확인한다. 앞일 경우, 뒷부분은 버리고 나머지에서 해당 절차를 반복한다.

데이터 크기 1000000일때 : 약 20번만 확인해도 된다.

## ⚡ 정리

**O(n)**  
선형 시간, 데이터에 비례하여 시간 늘어남

**O(log n)**  
1/2씩 줄여가며 탐색