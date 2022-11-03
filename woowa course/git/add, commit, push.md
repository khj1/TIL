## add, commit, push

add, commit, push를 기계적으로 하고 있지 않은가?

- `git init`은 현재 디렉토리를 로컬 저장소로 지정해준다.
    - `.git` 파일이 깃의 로컬 저장소를 의미한다.

> 여러가지 커맨드들의 사용법을 확인하는 방법. <br/>
> `-help` 명령어를 사용한다. <br/>
> - ex) `git init -help`

### git의 객체

- blob
    - 파일
- commit
    - 저장 단위 (게임에서의 세이브 포인트와 같은 개념이다.)
    - tree + blob + 메타정보
- tree
    - blob을 묶어서 관리 (디렉토리 구조와 유사)
- tag
    - 커밋에 대한 참조이지만 설명이 추가되는 객체

branch는 git의 객체가 아니다. commit을 참조하는 reference일 뿐이다.

### git add를 했을 때 어떤 일이 벌어질까?

```git
git add a.txt
```
- git이 a.txt를 관리 대상으로 인식한다. (staging)
- .git에 index와 objects가 추가된다.

### add와 commit을 나눈 이유가 뭘까?

- add 이후 추가적으로 commit을 하면 objects 디렉토리에 2개의 객체가 추가로 생성된다.

#### 파일의 내용을 수정하고 커밋하면 git은 공간 효율을 위해 변경사항만 저장할까?

아니다. git은 변경사항을 저장하지 않는다. 변경사항을 인식하고 저장하면 변경사항을 추적하고 비교하는 알고리즘이 추가되야 하기 때문에 용량적으로는 유리할 수 있어도 속도적인 측면에서 불리하다. 따라서 git은 파일을 통째로 저장한다.