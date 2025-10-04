---
title: "[ Git ] git pull vs git fetch"
datePublished: Sat Jan 18 2025 15:00:00 GMT+0000 (Coordinated Universal Time)
cuid: cm5t8db7u000k0ajp7ytw2m7y
slug: git-git-pull-vs-git-fetch
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1736661358650/08129f78-f624-4507-9929-cf0e2c556936.png
tags: github, git

---

> 목표 : **git pull과 git fetch의 차이점과 각각 어떤 상황에서 사용하는 것이 적절한지 알아보자.**

( codeit 강의를 수강 후 정리한 내용이다.  
출처 : [https://www.codeit.kr/topics/git/lessons/2945](https://www.codeit.kr/topics/git/lessons/2934))

# 1️⃣ git pull ?

만약 내가 로컬레포지토리에서 작업하는 동안 다른 사람이 리모트 레포지토리에 push를 하였다고 가정하자. 그럼 리모트 레포지토리에 변화가 생겼기 때문에 내 로컬에서 git push를 할 수 없다. 이때 git pull을 사용한다. 그러면 다른 사람이 작업했던 내용이 내 로컬 레포지토리에 반영된다.

이때, 다음과 같이 충돌이 발생할 수 있다.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736661580893/6d7dcdfa-4586-4076-8fd2-c7b152ebf5bd.png align="center")

충돌이 나는 이유는 다음과 같다.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736661678278/47c76530-971b-47a8-9f71-f90f48d27240.png align="center")

리모트 레포지토리의 최신 commit들을 로컬 레포지토리에 가져오는데 이걸 그대로 merge시킨다. 따라서 충돌이 발생할 수 있다.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736661774124/8f98beb4-5225-4d48-9444-51d5e624ce5f.png align="center")

충돌을 해결한 후 add, commit명령을 수행하면

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736661880855/b82ea5a4-8064-4cb9-ae2e-d81678b59767.png align="center")

다음과 같이 merge가 잘 수행된 것을 확인할 수 있다. 즉, 리모트 레포지토리의 최신 commit들을 로컬 레포지토리로 가져온 후 merge하는 명령어이다.

---

# 2️⃣ git fetch ?

최신 리모트 레포지토리의 commit들을 가져올때, 가져오기만 하는 명령어이다. pull처럼 merge 하지않는다. 이 명령어는 리모트레포지토리에 있는 브랜치의 내용을 일단 가져와서 살펴본 후에 merge하고 싶을 때 사용한다.

위 예시에서 git pull이 아닌 fetch 를 사용해보자.

```bash
git fetch
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736662231159/185e44cb-14f8-4227-a7c6-22e5079ae3bd.png align="center")

다음과 같이 나오면 성공이다.

```bash
# 두 commit 혹은 brach 간의 차이를 확인한다.
git diff premium origin/premium
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1736662343813/562dad7d-4496-44d2-9c2b-17ed67b32055.png align="center")

리모트 레포지토리의 premium브랜치에서 say\_hello함수는 calculate.py파일에서 적절하지 않는 함수이다. 이때, 해당 내용을 작성한 개발자에게 수정해달라고 부탁할 수도 있고 직접 수정할 수도 있다. 여기서는 직접 수정한다.

```bash
git merge origin/premium # merge후 코드를 수정한다.
git add .
git commit -m "Remove say_hello function in calculator.py"
git push
```

수정 후 add, commit, push을 해주면 리모트 레포지토리에서도 say\_hello함수가 없어진 것을 확인할 수 있다.

## ⚡ 정리

**차이점**  
git fetch는 리모트 레포지토리에서 최신 commit들을 가져오기만 한다.  
git pull는 리모트 레포지토리에서 최신 commit들을 가져온 후 자동으로 merge까지 해주는 명령어이다.

**언제?**  
fetch = 리모트 레포지토리에서 가져온 브랜치 내용을 merge전 점검해야할 필요가 있을때, 리모트 레포지토리에 있는 브랜치 내용과 내가 작성한 코드를 비교해서 잘못된 부분 없는지 검토해야할때  
pull = 리모트 레포지토리의 브랜치를 검토할 필요없이 바로 합치고 싶을때