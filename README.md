# git-test
- 회사에서 intelliJ 툴을 통해서만 깃을 사용하니 이해력이 떨어지고 툴 의존성만 높아져서 명령어 다시 공부.
- 회사에서 작업할 때도 최대한 터미널로 작업하려고 노력하자.
- git 공부
- 여러 명령어 만지작 만지작


## 명령어 (기본적으로 git (명령어) -help 를 통해 직접 확인하는 습관을 가지자)
- remote 서버(ex. origin)와 동기화
```
git fetch origin --all
```
- 동기화 후 실제 merge (ex. origin 리모트 서버의 develop 브런치와 develop). 로컬에서 develop 으로 체크아웃한 후 실행하기.
```
git merge origin/develop
```

- 리모트 트래킹 브랜치에서 시작하는 새 브랜치를 만들 때. 새로운 기능추가 작업을 할 때 release 브런치에서 생성한 후 다 작업이 끝나면 develop에 merge할 브런치 생성하는 것
```
git checkout -b feature/PROD-11111 origin/release
```

- 현재 추적 브런치가 어떻게 설정되어 있는가 (git fetch --all 명령어로 서버로부터 최신 데이터를 받아온 후에 추적 상황 확인하기)
```
git fetch --all
git branch -vv
```
