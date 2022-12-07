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
- 히스토리에서 언제 추가되거나 변경됐는지 찾아보기. 
  - `-S` 옵션. 해당 문자열이 추가된 커밋과 없어진 커밋만 검색할 수 있다.
  ```
  git log -S haha --oneline
  ```
- 라인 히스토리 검색 (매우 유용)
  - `-L` 옵션. 어떤 함수나 한 라인의 히스토리를 볼 수 있다.
  - 함수의 시작과 끝을 인식해서 함수에서 일어난 모든 히스토리를 함수가 처음 만들어진 때부터 Patch를 나열하여 보여준다. 
  - ex) zlib.c 파일에 있는 git_delfate_bound 함수의 모든 변경사항을 보기
  ```
  git log -L :git_deflate_bound:zlib.c
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
- 워킹 디렉터리 청소하기
  - 작업하고 있는 파일들을 stash하지 않고 단순히 그 파일들을 치워버리고 싶을 때
  - 보통은 merge나 외부 도구가 만들어낸 파일을 지우거나 이전 빌드 작업으로 생성된 각종 파일을 지우는데 필요하다.
  - 신중해야 하는 명령어. 이 명령을 사용하면 워킹 디렉터리의 추적하고 있지 않은 모든 파일이 지워지기 때문. 복구 불가능. `git stash -all` 명령을 이용하면 지우는 건 똑같지만, 먼저 모든 파일을 stash 하므로 좀 더 안전하다. 
  - 추적 중이지 않은 정보를 워킹 디렉터리에서 지우고 싶다면. 하위 디렉터리까지 모두 지움. `-f` 옵션은 강제(force)의 의미.
  ```
  git clean -f -d
  ```
  - 명령 실행 시 결과를 미리 보고 싶다면 `-n` 옵션
  ```
  git clean -d -n
  ```
  - 무시된 파일까지 함께 지우려면 `-x` 옵션. 원래 .gitignore에 명시했거나 무시되는 파일은 지우지 않는다.
  ```
  git clean -n -d
  ```
## 검색 (grep)
- 함수 정의나 함수가 호출되는 곳 검색해야 하는 경우 편함
- `-n` 옵션을 주면 찾은 문자열이 위치한 라인 번호도 같이 출력
```
git grep -n myfunction
```
- `--count` 옵션은 결과 대신 어떤 파일에서 몇 개나 찾았는지만 알고 싶을 경우
```
git grep --count myfunction
```
- `-p` 옵션은 매칭되는 라인이 있는 함수나 메서드 찾고 싶을 때 
  - ex) date.c 라는 파일에서 myfunction 함수를 ~~ 에서 호출하고 있는 것을 확인 가능
```
git grep -p myfunction *.c
```
## 충돌해결


## 히스토리 단장하기
- 모든 것은 다른 사람과 코드를 공유하기 전에 커밋 히스토리를 예쁘게 단장해야 한다.
- 마지막 커밋 메세지 수정하기
```
git commit --amend
```
- 커밋 후에 새로 만든 파일이나 수정한 파일을 가장 최근 커밋에 집어 넣기.
  - 파일 수정후 git add로 staging area에 넣는다. 
  - 그 후에 git commit --amend 로 커밋 하면 된다. 이 명령은 Staging Area의 내용을 이용해서 수정한다.
  - 이 때 SHA-1 값이 바뀌므로 과거의 커밋을 변경할 때 주의해야 함. Rebase와 같이 이미 Push한 커밋은 수정하면 안된다.

- 커밋 메시지 여러 개 수정하기
  - `-i`옵션으로 대화형실행. HEAD~3 은 어느 시점부터 HEAD까지의 커밋 3개를 수정.
  ```
  git rebase -i HEAD~3
  ```
  - 그러면 각 커밋 앞에 'pick' 이 붙어 있는데 이것을 edit으로 수정후에.
  ```
  git commit --amend // 후에 커밋 메시지 수정후 텍스트 편집기 종료.
  git rebase --continue
  ```
  - 후에 나머지 두 개의 커밋에 적용하면 끝. 다른 것도 pick을 edit으로 수정해서 하면 된다. 

- 커밋 순서 바꾸기
  - 아래 명령어 실행 후, 한 라인을 지우거나 각각의 라인의 순서를 변경해도 된다.
  ```
  git rebase -i HEAD~3
  ```
- 커밋 합치기
  - 아래 명령어 실행 후 pick이나 edit 말고 squash 를 입력하면 git은 해당 커밋과 바로 이전 커밋을 합칠 것이고 커밋 메시지도 수정한다. 
  ```
  git rebase -i HEAD~3
  ```
  - 만약 3개의 커밋을 모두 합치려면 스크립트를 아래와 같이 수정한다.
  ```
  pick f7f3f6d changed my name a bit
  squash 310154e upated README formatting and added blame
  squash a5f4a0d added cat-file
  ```
  - 저장 후 편집기 종료 하면 git은 3개의 커밋 메시지를 merge할 수 있도록 에디터를 바로 실행해주고, 메시지 저장하면 3개의 커밋이 합쳐진 하나의 커밋만 남는다.

- 커밋 분리하기
  - 아래 명령어 실행시.
  ```
  git rebase -i HEAD~3
  ```
  - 아래와 같이 나왔을 때, 두 번째 커밋인 "updated README formatting and added blame"을 "updated README formatting"과 "added blame"으로 분리하는 것이다. 
  ```
  pick f7f3f6d changed my name a bit
  squash 310154e upated README formatting and added blame
  squash a5f4a0d added cat-file
  ```
  - 그러면 아래와 같이 변경
  ```
  pick f7f3f6d changed my name a bit
  edit 310154e upated README formatting and added blame
  pick a5f4a0d added cat-file
  ```
  - 저장후에, 명령 프롬프트로 넘어간 후 그 커밋을 해제하고 그 내용을 다시 두 개로 나눠서 커밋하면 된다. 저장하고 편집기를 종료하면 Git은 제일 오래된 커밋의 부모로 이동하고서 f7f3f6d과 310154e을 처리하고 콘솔 프롬프트를 보여준다. 여기서 커밋을 해제하는 `git reset HEAD^` 라는 명령으로 커밋을 해제 하면 수정했던 파일은 Unstaged 상태가 된다. 그 후에 파일을 Stage 한 후 커밋하는 일을 원하는 만큼 반복하고 나서 `git rebase --continue` 라는 명령을 실행하면 남은 Rebase 작업이 끝난다.
  ```
  git reset HEAD^
  git add README
  git commit -m "updated README formatting"
  git add newfile4.txt
  git commit -m "updated newfile4.txt
  git rebase --continue
  ```
