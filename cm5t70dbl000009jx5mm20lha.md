---
title: "[ Git ] git rebase vs git merge"
datePublished: Sat Jan 11 2025 15:00:00 GMT+0000 (Coordinated Universal Time)
cuid: cm5t70dbl000009jx5mm20lha
slug: git-git-rebase-vs-git-merge
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1736660795519/0367ac23-d351-4096-a8ec-08f09379c8d6.png
tags: github, git

---

> 목표 : **git rebase와 git merge의 차이점과 각각 어떤 상황에서 사용하는 것이 적절한지 알아보자.**

( codeit 강의를 수강 후 정리한 내용이다.  
출처 : [https://www.codeit.kr/topics/git/lessons/2945](https://www.codeit.kr/topics/git/lessons/2945))

# 1️⃣ git merge 란?

다른 브랜치의 변경 사항을 현재 브랜치로 병합한다.

예를 들어, Premium 브랜치에서는 간단한 코드들을 바로 반영하고 test브랜치에서는 복잡한 코드를 실험 후 추가한다고 하자.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736658172546/2daeb2f3-c59c-4df2-acb8-4026b202e93c.png align="center")

```python
def add(a,b):
    retur a+b
def get_Remainder(a,b):
    return a//b
```

premium 브랜치에서 다음 코드를 작성 후

```bash
git add .
git commit -m "Add get_Remider function"
```

commit 시켜준다.

이제 테스트 브랜치에서 작업을 진행해보자.

```python
def add(a,b):
    retur a+b
def get_Median(a,b):
    return (a+b)/2
```

다음과 같은 코드를 작업하고

```bash
git add . # 여기서 브랜치 test이다 ! git checkout test로 먼저 이동 후 작업한다.
git commit -m "Add get_Median function"
```

commit 해준다. 이제 회사에서 코드 테스트를 진행한다. 허락(?)ㅎㅎ 이 나면 test 브랜치에서 추가했던 get\_Median함수를 premium 브랜치에 추가해보자.

```bash
git checkout premium #우리는 premium브랜치에 함수를 추가하니 이동한다.
git merge test # 충돌 발생
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736659108706/c6c2c41c-7138-43d7-bdcc-5de9b5c9f517.png align="center")

다음과 같이 충돌이 발생하면 정상이다.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736659142380/366748f5-4ac4-4e98-8aff-252e6d144af5.png align="center")

두 함수를 모두 남기는 방식으로 해결한다. 즉, «« 부분과 »» 의 화살표 줄만 없애고 함수는 남긴다는 의미이다. 그 후 add, commit을 해주면 merge완성이다 !!! 그래프로 history를 확인해보자.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736659271693/d5969e3d-2a69-4351-8e85-333b813d72bf.png align="center")

다음과 같이 merge된 상황을 확인할 수 있다.

위 그래프는 다음 그림과 같다. merge를 시키면 새로운 merge commit이 생성된다.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736659705602/3281acb0-9641-48d5-bc96-50425b4ef9d8.png align="center")

---

# 2️⃣ git rebase 란 ?

rebase 란 베이스를 다시 지정하다란 의미로 commit을 재배치한다. 위 예시에서 적용해보면 premium 브랜치의 베이스를 test 브랜치로 재지정한다고 생각하면 된다.

위 예시에서 git merge test가 아닌 rebase 를 사용해보자.

```bash
git rebase test
```

이 명령어를 입력하면 충돌이 나고 위 예시와 같이 해결해준다.

```bash
git add .
# "충돌이 발생해서 제대로 진행하지 못한 rebase를 계속 진행해라"
git rebase --continue # merge와 다른점이다. commit을 하지 않는다.
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736659918099/c94f3a35-1997-4e57-86c1-3682f31c51dc.png align="center")

history를 살펴보면 merge보다 좀 더 깔끔한 것을 확인할 수 있다. 그림으로 살펴보자.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736659760981/414481ca-d950-46f8-a67e-0e6d6997db79.png align="center")

rebase를 하면 premium 브랜치가 “Add get\_Median function”을 거쳐온 것 처럼 history구조 자체가 변한다.

## ⚡ 정리

**차이점** : 둘의 결과는 같지만 , rebase 는 merge와 달리 새로운 commit을 만들지 않는다. rebase로 만들어진 commit history는 merge로 만든 history보다 깔끔하다.

**언제?** : merge = 두 브랜치를 합쳤다는 정보가 commit history에 남아야하는 경우  
rebase = commit history를 깔끔하게 유지하는 경우