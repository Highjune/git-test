# git-test
> 회사에서 intelliJ 툴을 통해서만 깃을 사용하니 이해력이 떨어지고 툴 의존성만 높아져서 명령어 다시 공부.
- 회사에서 작업할 때도 최대한 터미널로 작업하려고 노력하자.
- git 공부
- 여러 명령어 만지작 만지작


# 명령어 (기본적으로 git (명령어) -help 를 통해 직접 확인하는 습관을 가지자)
## config 설정
- 설정값들 확인하기
  - alias 등 내가 설정한 값들을 확인할 수 있다.
```
git config --list
```
- git alias 설정하기
  - 아래처럼 설정하면 ~/.gitconfig 파일에 저장된다.
  - 설정후에는 `git log` 명령어로만 편하게 사용 가능하다.
```
git config -global alias.lg log --oneline --decorate --graph --all
```
## git log, 커밋 조회하기
- 짧고 중복되지 않는 해시값으로 로그 확인하기 `--abbrev-commit`
```
git log --abbrev-commit --pretty=oneline
```
- 커밋 조회 (아래 3개는 다 같음. 단, 짧은 해시값이 다른 커밋과 중복되지 않다고 가정)
```
git show 7b2a5f9937165a081bef7670a0c0d4b174106c72
git show 7b2a5f9937165a081b
git show 7b2a5f
```
- main 브런치가 7b2a5f9 를 가리키고 있으면 아래 명령어는 같다.
```
git show main
git show 7b2a5f9
```
- refLog 보여주기
  - Git은 자동으로 브랜치와 HEAD가 지난 몇 달 동안에 가리켰었던 커밋 모두 보여줌
  - Git은 브랜치가 가리키는 것이 달라질 때마다 그 정보를 임시 영역에 저장한다. 예전에 가리키던 것이 무엇인지 확인 가능
  - Reflog의 일은 모두 로컬의 일이므로 동료의 저장소에는 없다.
  ```
  git reflog
  ```
  - @{n} 규칙으로 HEAD가 5번 전에 가리켰던 것을 알 수 있다.
  ```
  git show HEAD@{5}
  ```
  - 시간도 사용가능
    - 이 명령은 Reflog에 남아있을 때만 조회할 수 있기 때문에 너무 오래된 커밋은 조회할 수 없다.
  ```
  git show master@{yesterday}
  ```
  - git reflog 결과를 git log 명령과 같은 형태로 볼 수 있다.
  ```
  git log -g 
  ```
- merge 하기 전 또는 push 하기 전에 무엇이 변경되었는지 확인해보기
  - double dot(..). 현재 experiment 브랜치에는 없고 main 브랜치에만 있는 커밋들 확인하기
  ```
  git log experiment.main
  ```
  - origin 저장소의 master 브랜치에는 없고 현재 checkout 중인 브랜치에만 있는 커밋 보여주기. checkout한 브런치가 master/origin이라면 아래 명령어가 보여주는 커밋이 push하면 서버에 전송될 커밋들이다.
  ```
  git log origin/master..HEAD
  ```
## 브런치 조작
- remote 서버(ex. origin)와 동기화
  - fetch는 서버로부터 데이터를 가져와서 저장해두고 사용자가 merge하도록 준비만 해둔다. 워킹 디렉토리의 파일 내용은 변경하지 않고 그대로 남는다. 
  - fetch 만 후에 git log 로 어떤 작업들을 했는지 확인해볼 수 있음. 그 후에 merge 
```
git fetch origin --all
git fetch --all 도 마찬가지
```
- 동기화 후 실제 merge (ex. origin 리모트 서버의 develop 브런치와 develop). 로컬에서 develop 으로 체크아웃한 후 실행하기.
```
git merge origin/develop
```
- fetch와 merge를 동시에
```
git pull origin master
```
- 리모트 트래킹 브랜치에서 시작하는 새 브랜치를 만들 때. 새로운 기능추가 작업을 할 때 release 브런치를 기반으로 생성한 후 다 작업이 끝나면 develop에 merge할 브런치 생성하는 것
```
git checkout -b feature/PROD-11111 origin/release
```

- 현재 추적 브런치가 어떻게 설정되어 있는가 (git fetch --all 명령어로 서버로부터 최신 데이터를 받아온 후에 추적 상황 확인하기)
```
git fetch --all
git branch -vv
```

- 리모트 브런치 삭제
  - 서버에서 브랜치(즉 커밋을 가리키는 포인터) 
```
git push origin --delete hotfixbranch
```

## stash. 작업 중 다른 브랜치로 checkout 해야 할 경우
- 완료하지 않은 일을 커밋하고 checkout 하기에는 애매한 경우. stash
- stash할 때와 같은 브랜치에 적용해야 하는 것 아니다. A 브런치에서 stash 한 것을 다른 B 브런치에서 stash 를 복원해도 된다.
- 꼭 깨끗한 워킹 디렉터리가 아니어도 된다.
- 워킹 디렉토리에서 수정한 파일들만 저장(즉, 추적중인 파일만 저장)
  - Modified이면서 Tracked 상태인 파일과 Staging Area에 있는 파일들을 보관해두는 장소다. (새로 생성한 파일은 X)
  ```
  git stash
  git stash save
  ```
  - 추적 중이지 않은 파일을 같이 저장하려면.(아래 두 명령어는 같다.)
  ```
  git stash -u
  git stash --include-untracked
  ```
- stash에 저장한 파일 확인
```
git stash list
```
- stash 에 저장한 파일을 복원
  ```
  git stash pop
  git stash apply
  ```
  - staged 에 있던 파일들을 stash 에 담은 후 다시 그대로(위 명령어) 꺼내면 unstaged가 된다. 만약 staged 상태를 유지하고 싶다면 --index
  ```
  git stash apply --index
  ```
- stash 에 저장한 파일들 중 일부분만 복원
  - 우선 list 확인해서 제일 앞에 stash@{n} 으로 숫자 나와있음
  ```
  git stash list
  ```
  - 해당하는 것만 복원
  ```
  git stash apply stash@{n}
  ```
- staging area에 들어있는 파일들 제외 stash 에 넣기
  - 많은 파일들을 변경했지만 몇몇 파일만 커밋하고 나머지 파일은 나중에 처리하고 싶을 때 유용하다. 
  ```
  git stash --keep-index
  ```

- stash 제거
  - git stash apply 는 단순히 stash를 적용하는 것뿐이다. stash는 여전히 스택에 남아 있다.
  ```
  git stash drop
  ```
  - 참고로 git stash pop은 stash를 적용하고 나서 바로 스택에서 제거해준다.
- stash를 적용한 브랜치 만들기
  - 보통 stash 에 저장하면 한동안 그대로 유지한 채로 그 브랜치에서 계속 새로운 일을 한다. 그러면 이제 저장한 Stash를 적용하는 것이 문제가 된다. 수정한 파일에 stash를 적용하면 충돌이 일어날 수도 있고 그러면 또 충돌을 해결해야 한다. 필요한 것은 stash한 것을 다시 테스트하는 것. 아래 명령어를 사용하면 stash할 당시의 커밋을 checkout한 후 새로운 브랜치를 만들고 여기에 적용한다. 이 모든 것이 성공하면 stash를 삭제한다.
  - 브랜치를 새로 만들고 stash를 복원해주는 매우 편리한 도구다.
  ```
  git stash branch testchanges
  ```

## 충돌해결