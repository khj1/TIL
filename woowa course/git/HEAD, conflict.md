## HEAD

현재 checkout된 커밋을 의미한다. 현재 작업 tree의 최신 커밋을 가리킨다.

### 상대 참조

```git
git switch HEAD^
```
- 현재 HEAD가 위치한 커밋의 이전 커밋으로 HEAD를 이동시킨다.
- `^`: Caret이라고 읽으면 된다.

```git
git branch -f step2 step1~2
```
- step2 브랜치를 step1의 현재 커밋 2단계 이전으로 위치시킨다는 의미다.
- `~`: Tilde라고 읽으면 된다.
- `~2`는 `^^`와 동일하다.

### 작업 되돌리기

```git
git reset HEAD~1
```
- 최종 커밋 한개만 제거된다.
- 단 이 방법은 커밋 히스토리를 고쳐쓰는 방법이기 때문에 함께 작업하는 remote 브랜치에서는 사용해선 안된다.

```git
git revert HEAD
```
- 예를 들어 HEAD가 커밋A를 가리키고 있었다고 했을 때
- 위의 커맨드를 입력해주면 커밋A 기록은 그대로 남은 상태로
- 커밋A의 작업 내역이 제거된 새로운 커밋A`가 만들어진다.

### 삭제된 커밋, 브랜치 복구

커밋이나 브랜치를 삭제하고 변경해도 그 이전의 기록이 Hash 값으로 남게된다.
이 점을 이용해 삭제된 커밋과 브랜치를 복구할 수 있다.

```git
git reflog
git reset --hard <되돌리고 싶은 커밋의 Hash>
git switch -c <새로운 브랜치 이름> <복구하고 싶은 브랜치의 커밋 Hash>
```
- `reflog` 커맨드로 모든 커밋 내역과 Hash 값을 확인할 수 있다.
- 기존에 reset했던 작업들도 모두 복구시킬 수 있어 매우 유용하다.

## 브랜치를 최신화하지 않고 작업을 해버렸을 떄

- 미션 저장소와 미션을 진행하기 위한 로컬 저장소가 따로 있다고 가정하자
- 미션 저장소에서 clone을 한 후 로컬 저장소에서 작업 후 미션 저장소로 merge 했다.
- 그런데 여기서 로컬 저장소의 커밋을 미션 저장소의 커밋으로 최신화 시키지 않고 그대로 다시 작업을 진행해버렸다.
- 2차 작업을 완료 후 다시 미션 저장소로 merge 하려고 하니 conflict가 발생한 상황

### 원격 저장소의 브랜치 얻기

문제를 해결하기에 앞서 원격 저장소의 브랜치를 가져오는 방법을 알아보자.

```git
git add upstream <upstream 주소>
git fetch upstream <브랜치>
git pull upstream <브랜치>
git push origin <브랜치>
```
- 미션 저장소를 upstream으로 지정
- fetch로 upstream의 브랜치 정보를 가져오고
- pull로 upstream의 브랜치를 로컬 저장소에 생성한다.
- 그러면 local에도 upstream의 브랜치가 생성된다.
- 이후 origin에 브랜치를 push 하면 origin 원격 저장소에도 동일한 브랜치가 생성된다.

### 문제 해결 방법

1. 원격 저장소(미션 저장소)에서 merge된 브랜치 내용을 가져온다.
2. 그런데 로컬 저장소에는 이미 동일한 이름의 브랜치가 존재한다.
3. 따라서 로컬 저장소의 브랜치를 새로운 이름으로 복사한 뒤 기존의 로컬 브랜치는 제거한다.
4. 이후 원격 저장소의 merge된 브랜치를 정상적으로 가져온 후
5. 복사해둔 브랜치에서 작업한 커밋들을 원격 저장소에서 새롭게 가져온 브랜치에 cherry-pick해오면 된다.
6. 작업이 끝나면 로컬 저장소에서 원격 저장소로 push한 후 PR을 작성한다.

## 추천 학습 자료

- [브랜치 실습](https://www.youtube.com/watch?v=JsRD2AWxxFg)

## 출처

- [10분 테코톡 - 와일더의 Git Commands](https://www.youtube.com/watch?v=JsRD2AWxxFg)

