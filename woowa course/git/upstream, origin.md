## upstream, origin

upstream은 '상류'를 의미하고 origin은 '원천'을 의미한다. 두 용어는 git에서의 관계를 나타내는 상대적인 개념이다. 

<br />

origin -> push/pull -> local

- origin: fork한 저장소
- push/pull: 나의 원격 저장소
- local: 코드를 작업하는 로컬 공간

```git
git remote add origin [원격 저장소 주소]
```
- origin이라는 이름으로 원격 저장소를 저장해서 관리하겠다는 의미.

```git
git push -u origin main
```
- origin 원격 저장소로 main 브랜치를 push한다는 의미
- `-u`는 `--set-upstream`을 의미한다.

상류에서 하류로 강물이 흐르면서 여러 갈래로 나뉠 수 있다. 하나의 원격 저장소에도 수 많은 로컬 저장소가 생길 수 있다. 그렇기에 상류와 하류의 위계질서를 확실히 정해주기 위해 최초에 upstream 관계를 명시해줘야 한다.

- 다만 github에서 clone을 하면 자동으로 upsteam과 downstream의 관계가 설정된다.