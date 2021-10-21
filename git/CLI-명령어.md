# Git 명령어
### `$ git add <파일이름>`
Working directory -> Staging Area

### 첫 커밋 취소하기
```
git update-ref -d HEAD
git rm --cached -r .
```

<br>

### `$ git commit`
Staging Area -> Repository

<br>

### `$ git commit -m "커밋메시지"`
짧은 commit message로 빠르게 commit하는 경우

<br>

### `$ git commit`
길고 자세한 commit message를 남겨야하는 경우

<br>

### `$ git init` 
디렉토리 내에서 우클릭 -> **Git Bash Here** -> 해당 파일에서 버전관리를 시작하겠다고 선언
- 해당폴더에 `.git`이라는 숨겨진 폴더가 생성된다( 로컬 저장소 )
- 로컬 저장소에 만든 내가 만든 정보, 원격 저장소 주소등이 저장된다. 
- 한 폴더에 하나의 로컬 저장소만 유지해야한다.

<br>

### `$ ls -al`
해당 디렉토리 내에서 숨김 파일을 포함한 모든 파일을 확인

<br>

### `$ git status`
해당 디렉토리의 상태를 표시해주는 명령어

<br>

### `$ git rm --cached <file>..."`
to unstage

<br>

### `$ git add .`
해당 디렉토리의 모든 파일들을 add하도록 하는 명령어

<br>

### `$ git log`
지금까지 커밋된 사항들을 확인할 수 있다.

<br>

### `$ git commit -am "커밋메시지"`
- `add`와 `commit`을 동시에 실행
- 단 한번이라도 `commit`한 파일에만 사용 가능

<br>

### `$ git config --global user.name` 
"Github 닉네임" 입력해서 해당 git과 git허브를 연결해준다.
### `$ git config --global user.email` 
"Github 이메일" 입력해서 해당 git과 git허브를 연결해준다

<br>

### `$ git tag 0.1`
- 0.1 버전이라고 표시해주는 명령어
- 주로 master 또는 main 브랜치가 업데이트됐을 때 사용한다.

<br>

### `$ git branch`
브랜치 목록 확인

<br>

### `$ git branch <브랜치이름>`
새로운 브랜치 생성

<br>

### `$ git branch -d <브랜치이름>`
브랜치를 삭제한다.

<br>

### `$ git checkout <브랜치이름>`
해당 브랜치로 switch

<br>

### `$ git merge <브랜치이름>`
현재 브랜치와 합치고 싶은 브랜치 이름을 작성

<br>

### `$ git checkcout -b <브랜치이름>`
새로운 브랜치를 생성함과 동시에 해당 브랜치로 이동 ( `checkout` )

### `$ git remote (-v)`
내 로컬 repository와 상호작용하고 있는 (혹은 할 수 있는) 원격 저장소들의 목록을 조회한다.
- `-v` 옵션: 단축이름과 URL 같이보기

<br>

### `$ git remote add origin <url>`
\<url>에 있는 원격 저장소를 `origin` 이라는 이름으로 추가한다.
- `$git remote add hanjun <url>` 으로 추가해도 된다.

<br>

### `$ git push -u origin master`
- 내 로컬 저장소의 내용을 원격 저장소에 저장할때 `push` 한다.
- `-u` 디폴트 설정
  - 앞으로 `$git push`만 작성해주면 알아서 `origin`과 `master`의 상호작용으로 이해한다.

<br>

### `$ git pull (origin master)`
- `origin`을 내 원격 저장소의 `master` 브랜치로 갖고와라( `merge` )

<br>

### `$ git fetch (origin master)`
- 동기화시키지는 말고 ( `merge`는 하지 말고 )
- `origin`을 내 원격 저장소의 `master` 브랜치로 일단 갖고온다.

<br>

### `$ git clone <url>`
\<url>에 있는 원격 저장소의 내용을 현재 디렉토리에 복사해오기

<br>

### `$ git remote rm <단축이름>`
로컬 저장소와 연결된 원격 저장소를 제거한다.

<br>

#### `$ git merge --no-ff release/0.2`
- `merge` 커밋을 남기기위한 명령어
- `--no-ff`는 `fast-forward`를 하지 말라는 명령어이다
- 브랜치가 너무 많아지는 것을 방지하는 역할을 한다.

<br>

### `$ git branch -d release/0.2`
해당 브랜치를 제거한다.

<br>

### `$ git push -d origin <branch_name>`
원격 브랜치를 제거한다

<br>

### `$ git fetch --all --prune` 또는 `$ git remote prune origin`
- 원격 저장소에서는 branch를 정상적으로 삭제했으나 해당 내용이 로컬에 반영되지 않을 때가 있다.
- 이런 경우 해당 명령어를 통해 동기화 시켜줄 수 있다.

<br>

### `$ git diff`
`git diff`는 변경내역들끼리의 비교 결과를 보여준다. <br>
예를 들면, 우리가 버전을 관리하는 도중에 다음과 같은 상황이 발생할 수 있다.<br>

> 1. 10번 `commit` 중 1번 `commit`과 2번 `commit`을 비교하고 싶어!
> 2. 지금 `push`한 `commit`과 이전 `commit`을 비교하고 싶어!
> 3. 현재 `staging area`에 있는 변경 내역과 직전의 `commit`을 비교하고 싶어!
> 4. `branch1` 브랜치에 있는 `commit`과 `master` 브랜치에 있는 `commit`을 비교하고 싶어!

이럴 때 사용할 수 있는게 `diff`. 말 그대로 패치간의 차이점을 알 수 있게 해준다.

#### 두 커밋 간의 비교
예를 들어 현재 저장소에 `test.txt` 달랑 하나 있고, 1번부터 5번 `commit`이 있으며, 각각의 `commit`은 다음과 같이 작성되어 있다고 가정해보자.

    1번 commit : test.txt에 A 라는 문자열 하나 저장
    2번 commit : test.txt에 B문자 추가
    3번 commit : test.txt에 C문자 추가
    4번 commit : test.txt에 D문자 추가
    5번 commit : test.txt에 E문자 추가

여기서 `log`를 보게 된다면 아래와 같은 화면이 나오게 된다.

    $ git log
    commit f7fe32715c4bd705110196134271d8f873384316 (HEAD -> master)
    Author: Kang Minchul <tegongkang@gmail.com>
    Date:   Tue Dec 1 23:06:03 2020 +0900

        5

    commit 2845ce53054627b3381c9f2515dc7545cff2347b
    Author: Kang Minchul <tegongkang@gmail.com>
    Date:   Tue Dec 1 23:05:49 2020 +0900

        4

    commit de6d5c1148981e15617999c7ecaa6ec2ea21ff29
    Author: Kang Minchul <tegongkang@gmail.com>
    Date:   Tue Dec 1 23:05:38 2020 +0900

        3

    commit 1d7fce052aafb388ddf092ea315835f5154683f7
    Author: Kang Minchul <tegongkang@gmail.com>
    Date:   Tue Dec 1 23:04:52 2020 +0900

        2

    commit 6958b6b21e15aa0be36736016c5bc955b57a61be
    Author: Kang Minchul <tegongkang@gmail.com>
    Date:   Tue Dec 1 23:04:38 2020 +0900

        1

commit 옆에 마구잡이로 생긴 문자열, (f7fe32715c4bd705110196134271d8f873384316 등등…) <br>
이건 각각의 `commit`을 나타내는 `commit hash`입니다. 이 `commit`을 가리키는 말이라고 보시면 돼요.<br><br>

**git diff는 아래와 같은 명령어로 사용할 수 있습니다.** <br>

    git diff 비교대상commit 기준commit

- `git diff <이 commit에 비해> <이 commit은 무엇이 달라졌니?>` 

만약 4번 commit에 비해 5번 commit이 무엇이 달라졌는지를 알고싶다면 다음과 같이 작성한다.

    $ git diff f7fe32715c4bd705110196134271d8f873384316 2845ce53054627b3381c9f2515dc7545cff2347b  

그럼 아래와 같은 결과를 볼 수 있다.

```bash
$ git diff 2845ce53054627b3381c9f2515dc7545cff2347b f7fe32715c4bd705110196134271d8f873384316
diff --git a/test.txt b/test.txt
index 8422d40..8fda00d 100644
--- a/test.txt
+++ b/test.txt
@@ -2,3 +2,4 @@ A
 B
 C
 D
+E          # 4번 Commit에 비해 E라는 문자열이 추가되었다(+ 표시)
```
<br>

#### 원격 저장소와 로컬 저장소 간의 비교
위와 같은 사용예는 원격 저장소에서도 동일하게 적용할 수 있다.

    git diff <비교대상 branch이름> origin\<branch 이름> 

#### 이전 commit과 전전 commit의 비교
근데 매번 `commit hash`를 쓰는게 귀찮다면 아래와 같이 써도 된다. <br>

    git diff HEAD HEAD^ 

- `HEAD`는 현재 `branch`의 가장 최근 `commit`을 가리킨다. 
- `HEAD^`는 현재 `branch`의 가장 최근 `commit`에서 하나 이전의 `commit`을 가리킨다.

> **참고** <br>
> `HEAD`에서 `^`의 개수는 현재 commit에서 몇 번째 떨어진 `commit`인지를 나타냅니다. <br>

위 코드의 결과는 다음과 같다.

    $ git diff HEAD^ HEAD
    diff --git a/test.txt b/test.txt
    index 8422d40..8fda00d 100644
    --- a/test.txt
    +++ b/test.txt
    @@ -2,3 +2,4 @@ A
    B
    C
    D
    +E

<br>

#### 이전 commit과 현재 수정된 내용 비교
아직 commit하지 않은, 수정된 내용과 이전 commit ( 예제에서는 5번 commit )과 비교하기 위해 아래 명령어를 사용할 수 있다.

    git diff HEAD

예를 들어 `test.txt`에 F라는 문자를 추가하고, 위 명령어를 친다면 아래와 같은 결과를 받아볼 수 있다.

    $ git diff HEAD
    diff --git a/test.txt b/test.txt
    index 8fda00d..cead32e 100644
    --- a/test.txt
    +++ b/test.txt
    @@ -3,3 +3,4 @@ B
    C
    D
    E
    +F

즉, 아직 `commit`하지 않은 현재 작업 중인 내용을 가장 최근 `commit`한 내용과 비교한 결과이다. <br>

#### 브랜치간의 비교
아래의 명령어를 통해 `branch`끼리의 비교도 가능하다.

    git diff <비교대상 branch 이름> <기준 branch 이름> 

<br>

### `$ git revert`
git revert도 reset과 동일하게 commit을 되돌리는 명령어이다.

    $ git revert <되돌아가고 싶은 commit>

위 예시에서 3번 commit으로 revert하고 싶다면

    git revert de6d5c1148981e15617999c7ecaa6ec2ea21ff29

<br>

> **소스트리** <br>
> `HEAD`에서 우클릭 후 **커밋 되돌리기** 버튼 클릭

<br>

#### reset과의 차이점
`git reset`과 `git revert`는 `commit`을 과거 시점으로 되돌려준다는 점에서 결과적으로 같은 결과를 내지만, **큰 차이가 있다면 되돌리는 `commit`까지의 이력이 사라지느냐의 여부에 있습니다.**
<br>

- `git reset`은 **되돌린 버전 이후의 버전들이 모두 사라지게 된다.**
- `git revert`는 **되돌린 버전 이후의 버전들은 모두 유지되고, `revert`되었다는 사실을 담은 `commit`만 새로 추가되게 됩니다.**

<br>

즉, reset은 과거 자체를 바꾸는 명령어이고, revert는 과거를 변경시켰다는 내용을 담은 새로운 commit을 만드는 명령어인 것이다.

- `revert`는 `reset`보다 더 안전하게 `commit`을 되돌리는 방법
- `reset`은 `revert`보다 `commit log`를 깔끔하게 유지해주면서 `commit`을 되돌리는 방법
