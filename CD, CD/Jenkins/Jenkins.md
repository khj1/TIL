# Jenkins
## 젠킨스 개발환경 및 CI/CD 기본 동작의 이해
### 개발환경의 종류
- 개발자가 개발을 하는 Local 환경
- 통합 테스트를 위한 Development 환경 ( **Dev** )
- 개발이 끝나고 QA 엔지니어 및 내부 사용자들이 사용해 보기 위한 QA 환경 ( **QA** )
- 실제 유저가 사용하는 Production 환경 ( **Prod** )

<br>

### 개발 프로세스
1. 개발자가 자신의 PC에서 개발을 한다.
2. 다른 개발자가 작성한 코드와 차이가 발생하지 않는지 내부 테스트를 진행한다.
   - Github action으로 로컬 환경에서 미리 테스트를 진행할 수 있다.
3. 진행한 내용을 다른 개발자들과 공유하기 위해 git과 같은 SCM에 올린다. ( 예를 들어 dev 브랜치 )
4. Dev 브랜치의 내용을 개발 환경에 배포하기 전에 테스트와 Lint 등 코드 포맷팅을 한다.
5. 배포하기위한 빌드 과정을 거친다.
6. 코드를 배포한다.
7. 테스트를 진행한다.

<br>

### 여러 배포 환경의 관리
여러 배포환경에서의 핵심은 **인프라를 모듈화하여 어떤것이 변수인지 잘 설정하고 이를 효율적으로 설계하는 것이다.** <br>
서비스 내부의 변수 뿐만 아니라 **클라우드 리소스를 많이 활용해서 개발하는 요즘에는 클라우드 리소스 내에서 인프라별 키 관리가 매우 중요하다.** aws system manager의 parameter store와 같은 키 관리 서비스를 사용하는 것을 권장한다.

<br>

### 젠킨스 설치
**AWS 인스턴스 내에서 실습한다.**
1. jenkins 설치 이전에 자바를 설치해줘야한다.
2. `sudo apt-get update`
3. `java -version`으로 자바가 설치되어있는지 확인한다.
4. `sudo apt-get install openjdk-11-jdk`
5. 또는 `sudo apt-get install openjdk-11-jre`

> 참고 <br>
> jdk ( java development kit )= jre( java runtime environment ) + @ <br>
> jre는 읽기 전용이고 jdk는 읽기, 쓰기 전용이다. jdk는 프로그래밍 도구를 포함하고 있다.

<br>

6. `sudo apt-get install jenkins`
7. `sudo service jenkins start`
8. `sudo service jenkins status`로 잘 설치되었는지 확인 ( active 확인 )
9. `~{aws}~:8080`으로 접속해서 Jenkins 화면이 뜨면 설치 성공
10. `sudo cat /var/lib/jenkins/secrets/initialAdminPassword`로 들어가서 jenkins 비밀번호 확인

<br>

### 젠킨스 사용
1. credentials 플러그인을 설정해준다.
2. add credentials 선택
3. github에서 repository를 생성해준다.
4. github 프로필의 settings로 넘어가서 develop setting
5. personal access token을 생성한다. ( repo 정도만 체크 )
6. 해당 토큰을 credintial password로 설정해준다.
7. username은 내 깃헙 아이디
8. jenkins 설정 파일 (Jenkinsfile)을 열고 설정 시작


<br>

#### aws access key 발급
- AWS의 IAM( Identity Access Manager ) 서비스에 접속
- IAM 서비스에서 사용자 탭으로 이동하여 Jenkin 사용자를 새로 생성해준다.
  - 프로그래밍 방식 엑세스 체크
- 권한 설정으로 넘어와서 해당 엑세스키의 권한을 설정해준다
  - 보통의 경우 젠킨스가 DB를 지워버리는 등의 문제가 발생시키면 안되기 때문에 정교하게 권한 설정을 해줄 필요가 있다.
  - 하지만 실습의 과정에서는 **기존 정책 직접 연결 - Administrator acess**권한을 부여해 모든 작업을 할 수 있게 설정해줬다.
- 사용자를 생성해주면 Access key와 secret access key가 발급된다.
- 발급받은 key를 젠킨스에 등록해줘야한다.
- 젠킨스의 add credential로 이동
- Credenital 종류를 Secret text로 설정

   ![jenkins-secret-text](../img/jenkins-secret-text.png)

- 먼저 엑세스 키를 등록한다. Secret에 엑세스키를 입력
- Credential에서 지정한 ID로 젠킨스 파일에서 해당 엑세스 키를 가져올 수 있다.
  - 예제에서는 awsAccessKeyId 로 등록했다.



<br>

```java
environment { // 해당 파이프라인에서 사용할 환경변수 설정
      AWS_ACCESS_KEY_ID = credentials('awsAccessKeyId') // 젠킨스 파일에서 aws 명령어를 사용하기 위한 설정
      AWS_SECRET_ACCESS_KEY = credentials('awsSecretAccessKey')
      AWS_DEFAULT_REGION = 'ap-northeast-2'// 서울로 지역을 설정
      HOME = '.' // Avoid npm root owned
    }
```

<br>

#### 젠킨스에 플러그인을 추가해주자
- Docker
  - Docker
  - Docker Pipeline
  - Docker 플러그인 설치후 Xshell에서 `sudo apt-get install docker`
  - `sudo groupadd docker`
  - `sudo usermod -aG docker ${USER}`
  - `sudo usermod -aG docker jenkins`
  - `logout` 입력 후 다시 연결
  - `sudo service docker start`
