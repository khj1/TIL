# 커밋 메세지

## 커밋 메세지 수정 방법

### 가장 최근에 작성한 커밋 메세지를 수정하는 방법
```
git commit --amend
```

```
git commit --amend -m "메세지 내용"
```

### 이전에 작성한 커밋 메세지를 수정하는 방법
```
git rebase -i
git rebase -i HEAD~3
```
- HEAD~3 옵션은 가장 최근부터 총 3개의 내역을 불러옵니다.

## 출처
https://jw910911.tistory.com/77 - Git 커밋한 메세지 수정하기

