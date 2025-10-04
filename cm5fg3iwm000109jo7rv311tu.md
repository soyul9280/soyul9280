---
title: "[Git] SSH오류"
datePublished: Thu Apr 10 2025 15:00:00 GMT+0000 (Coordinated Universal Time)
cuid: cm5fg3iwm000109jo7rv311tu
slug: git-ssh
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1736663141255/daf23195-91e9-4dc0-8e9c-1745ae764d12.png
tags: ssh

---

## ❓ssh를 설정할 때 denied 된다? clone 할 때 암호를 요구한다?

<div data-node-type="callout">
<div data-node-type="callout-emoji">💡</div>
<div data-node-type="callout-text">위와 같은 문제를 해결하는 것이 목표이다.</div>
</div>

1. 문제 해결→먼저 로컬에 있는 ssh파일을 삭제한다.
    

```bash
rm -rf .ssh
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1735828484105/dd18e384-90c2-4004-8184-cb82be8919ad.png align="center")

다음과 같은 결과가 나와야한다. 혹은 ls -alh 명령어를 내렸을 때 .ssh파일이 없어야 한다.

2. 이제 SSH를 생성한다.
    

```bash
ssh-keygen -t ed25519 -C "email@email.com"
```

위 명령어를 입력하면 암호화 파일이 어디 저장되는지, 비밀번호를 설정할 수 있다.  
이후 , 이 경로로 접근하면 id\_ed25519(비공개 키이다.) 와 id\_ed25519.pub(공개 키이다.)이 생성된것을 확인할 수 있다.  
cat 공개키 후 내용을 Github 사이트에 등록한다. (설정 → SSH and GPG key→SSH keys에 등록)

3. SSH에 접속해본다.
    

```bash
ssh -T git@github.com
```

“Hi &lt;username&gt;! You’ve successfully……”출력되면 성공이다.

4. 이제 clone할 디렉토리(git\_team으로 지정하겠다.)를 만들어 준 후 clone해보자.
    

```bash
mkdir git_team
cd git_team # git_team 폴더로 이동한다.
git init #필수다!! 이거 안하면 git branch 의 명령어들 사용 불가!!!(ex.git branch -a)
git clone git@github.com:myname/myrepo.git
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1735829034458/468a3598-ba8d-4bdc-b77e-595444d8a1e4.png align="center")

문제 해결→SSH 키를 인증했기 때문에 SSH프로토콜로 진행했어야 했다. 다음과 같이 뜨면 성공이다.

5. 이제 remote와 pull을 진행해준다. (git branch -r : branch 목록을 보여준다.)
    

```bash
git remote add origin git@github.com:myname/myrepo.git # origin의 이름으로 주소 설정
git pull origin dev
git ls-files # 파일들을 확인한다.
git checkout [branch이름] # 해당 branch로 이동한다.
echo "...." > [파일명]
git add .
git commit -m "..."
git push origin [branch이름]
```

해당 명령어를 진행하면 Github페이지에 파일이 올라간 것을 확인할 수 있다.