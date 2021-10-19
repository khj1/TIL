# 깃헙 템플릿
## Commit Template
### 커밋 메시지 작성
**`.gitmessage.txt`**
```
##### 제목 - 50자 이내로 요약!

### [커밋 타입]: [작업내용]

##### 본문 - 한 줄에 최대 72 글자까지만 입력하기

# 1. 무엇을 수정했는지
# 2. 왜 수정했는지

# 꼬릿말은 아래에 작성: ex) #이슈 번호
-
#   [커밋 타입]  리스트
#   feat      : 기능 (새로운 기능)
#   fix       : 버그 (버그 수정)
#   refactor  : 리팩토링
#   style     : 스타일 (코드 형식, 세미콜론 추가: 비즈니스 로직에 변경 없음)
#   docs      : 문서 (문서 추가, 수정, 삭제)
#   test      : 테스트 (테스트 코드 추가, 수정, 삭제: 비즈니스 로직에 변경 없음)
#   chore     : 기타 변경사항 (빌드 스크립트 수정 등)
#   post     : 블로그 포스트 추가 (신규 포스트 작성 및 수정)
# ------------------
#   [체크리스트]
#     제목 첫 글자는 대문자로 작성했나요?
#     제목은 명령문으로 작성했나요?
#     제목 끝에 마침표(.) 금지
#     제목과 본문을 한 줄 띄워 분리하기
#     본문에 여러줄의 메시지를 작성할 땐 "-"로 구분했나요?
# ------------------
```

<br>

### 커밋 메시지 설정 저장
`$ git config --global commit.template ~/.gitmessage.txt`

<br>

***
## Issue Template
### Issue Template 정의하기
- 마크 다운 형식으로 파일 생성
- `name`, `about`, `title`, `labels`, `assignees`를 미리 지정할 수 있음
- `labels` 의 경우 위에서 정의 한 `label name`을 지정하면 됨

<br>

```md
---
name: Bug Report Template
about: 버그 리포트 템플릿입니다.
title: ''
labels: 'Status: To Do, Priority: Medium, Type: Bugfix/Function, Type: Bugfix/UI'
assignees: ''
---
<!-- name, about, title, labels, assignees를 미리 지정할 수 있음 -->

<!-- 버그와 관련된 이슈 템플릿 -->
<!-- 이 이슈 카드에 대한 설명, 최대한 다른 사람이 알아 볼 수 있도록 적어주자 -->
### 어떤 버그가 발생했나요?

### 예상했던 결과는 무엇인가요?

<!-- 필요에 따라서 이 이슈를 위한 체크박스를 만들어도 됨 -->
### 어떤 식으로 발생했는지 순서대로 적어주세요.
1. 어디를 가서 '...'
2. 어떤걸 클릭했더니 '...'
3. 어떤 화면에 '...'
4. 어떤 에러가 나왔다.

### 화면 캡쳐
가능하면 캡쳐된 화면으로 설명해주세요.
```

<br>

### Issue Template 적용하기
- 해당 리포지토리에 위치한 `.github/ISSUE_TEMPATE/` 디렉토리 내에 삽입해준다.

<br>

***
## Label Template
### Label 정의하기
```json
[
    {
        "name": "Priority: Critical",
        "color": "8c001a",
        "description": "우선순위 긴급"
    }
]
```
- `labes.json` 파일에 JSON Array 형태로 정의
- `name`, `color`, `description` 을 정의할 수 있음

<br>

### 정의된 Label 적용하기
- `액세스 토큰`, `계정명`, `저장소 이름`을 자신의 것으로 변경

    npx github-label-sync --access-token [액세스 토큰] --labels labels.json [계정명]/[저장소 이름]

<br>

***
## PR Template