## Merge

git은 서로 다른 작업을 하기 위한 별도의 공간을 생성할 때 브랜치를 생성할 수 있다. 브랜치를 생성한 후 기능 구현을 하고 해당 기능을 main 브랜치에 merge 한다.

### fast-forward

A 브랜치를 B 브랜치로 merge 할 때, A 브랜치의 커밋이 B 브랜치의 커밋 이후로 온전히 이동하는 것을 fast-forwrd 라고 한다. 즉, my-branch 브랜치를 생성하고 작업이 끝난 다음 master에 merge를 진행할 때까지, master에 어떠한 변경도 없다면 fast-forward merge가 진행된다. fast-forward는 서로 다른 상태를 병합하는 것이 아니고 master를 my-branch 위치로 이동한 해도 되는 상태이기 때문에 별도의 merge를 위한 커밋이 발생하지 않는다.

다시 말해 master 브랜치에 my-branch를 merge할 때 my-branch가 master 이후 커밋을 가리키고 있으면 그저 master를 my-branch와 동일한 커밋을 가리키도록 이동시킬 뿐이다.

### 3-way-merge

다른 브랜치에서 작업을 하다가 upstream main 브랜치에서 변경점이 생겼을 때 해당 변경점을 현재 작업 중인 브랜치에 적용시켜야 한다.

3-way-merge를 위해 세 가지 커밋이 필요하다.

1. 내 브랜치 커밋
2. 남의 브랜치 커밋
3. 두 브랜치의 공통 조상이 되는 커밋(Base)

| My | Base | Other | Merge |
| :----: | :----: | :----: | :----: |
| a | a | a' | a' | 
| b | b | b | b | 
| c' | c | c'' | conflict | 
| d' | d | d | d' |

위 와 같이 My 브랜치와 Other 브랜치만을 보고 a가 a'로 변했는지, a'가 a로 변했는지 파악하기 힘들기 때문에 Base 브랜치와 함께 비교한다.

```git
git fetch upsteam main
git merge upstream/main
```
```git
git checkout main
git merge --no-ff feature
git merge --ff-only feature
git merge --squash feature
```

- `--no-ff` : fast-forwar 관계 여부와 상관없이 Merge 커밋을 따로 생성하여 병합한다.
- `--ff-only` : fast-forward 관계인 경우에만 병합을 진행한다. Merge 커밋은 생성되지 않는다.

### Squash and merge

Squash는 여러 개의 커밋을 하나로 합치는 기능을 말한다. 머지할 브랜치의 커밋들을 전부 하나의 커밋으로 합친 뒤 타겟 브랜치에 커밋하는 방식으로 머지를 진행한다.

Squash and Merge 전략은 브랜치의 자잘한 커밋 사항이 남지 않기 때문에 Merge가 되었다는 사실 자체에만 집중한 기록이 남게되고, 그로 인해 변경 사항을 읽기가 수월해진다.

## Rebase

- Merge와의 공통점: 브랜치를 합친다.
- Merge와의 차이점: Merge보다 깨끗한 commit history를 만든다.

Merge는 Merge 브랜치를 생성하여 합치는 것이고, Rebase는 Base를 재설정하여 합치는 것을 의미한다. 즉 현재 브랜치의 Base를 바꾸겠다는 의미이다.

Rebase는 변경될 커밋들을 복사해서 Base 커밋 목록에 연이어 붙이는 것을 말한다. 따라서 커밋 히스토리가 Merge와 다르게 선형적으로 그려진다 또한 커밋이 복사되었기 때문에 Rebase 이후 commit id가 변경된다.

단 Rebase와 Merge 후 코드의 결과는 동일해야 한다.

## Cherry-pick

다른 브랜치에 있는 커밋을 **선택적으로** 내 브랜치에 적용하는 것이다. 여기서 chrry-pick된 커밋들은 복사가 되어 새로운 커밋으로 인식된다.

### Cherry-pick 사용법

```git
git checkout main
git cherry-pick e19c319
git push origin main
```
- develop 브랜치에서 main 브랜치로 merge하고 싶은 기능의 커밋 ID를 사용한다.
- 다만 위와 같이 main 브랜치로 직접적으로 push 하는 방법은 권장되지 않는다.

```git
git checkout main
git checkout -b cherry
git cherry-pick e19c319
git push origin cherry
```
- 위와 같이 main을 base로 하는 cherry 브랜치를 생성하고
- cherry 브랜치에 main 브랜치로 merge하고 싶은 기능의 커밋을 cherry-pick 한 후
- main 브랜치로 PR을 보내는 방법이 좋다.

#### 여러 개의 커밋을 가져오고 싶을 때

```git
git cherry-pick e19c319 e18c22
```

#### 연속된 커밋을 가져오고 싶을 때

```git
git cherry-pick e19c319..e18c22
```

### conflict

Cherry-pick 진행 시 충돌이 발생한다면?

1. conflict를 해결하기 위해 코드를 수정한다
2. git add 명령으로 수정된 코드를 staging 한다
3. `git cherry-pic --continue` 명령을 수행한다.

## 출처
- [10분 테코톡 - 오리와 코린의 Merge, Rebase, Cherry-pick](https://www.youtube.com/watch?v=b72mDco4g78)
- [3-way-merge 이해하기](https://wonyong-jang.github.io/git/2021/02/05/Github-Merge.html)