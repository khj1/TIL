# EC2
독립된 컴퓨터 한 대를 통째로 임대해주는 서비스.

## 인스턴스
인스턴스는 컴퓨터 한 대를 의미한다. <br>

<br>

### 인스턴스 생성과정
1. AMI 선택
   - 인스턴스의 운영 체제를 선택한다.
2. 인스턴스 유형 선택
   - 인스턴스의 사양을 선택한다.
     - Instance Type
       - m~: 같은 가격 대비 메모리에 우위를 가지고있는 인스턴스
       - c~: 같은 가격 대비 CPU에 우위를 가지고있는 인스턴스
     - vCPUs: 가상화된 CPU의 갯수
     - Memory: 메모리의 크기
     - Instance Storage
     - EBS-Optimized Available
3. 인스턴스 설정
4. 스토리지 추가
   - IOP: 저장 장치의 속도를 직접 지정할 수 있다.
5. 태그 추가
   - 해당 인스턴스에 대한 설명을 추가할 수 있다.
6. 보안 그룹 구성
   - 원격 제어 구성
     - SSH: 리눅스 환경의 인스턴스에서 사용하는 원격 제어 방식
     - RDP: 윈도우 환경의 인스턴스에서 사용하는 원격 제어 방식
     - HTTP: 웹 환경에서 사용하는 원격 제어 방식
7. 인스턴스 검토
   - 네트워크를 통해서 해당 인스턴스에 접속하기 위해 비밀번호를 발급받는다.

<br>

### 인스턴스 타입
#### 온 디맨드 인스턴스
상시로 켜고 끌 수 있는 인스턴스
- 일시정지 시켰을 땐 과금이 발생하지 않는다.

#### 예약 인스턴스
온 디맨드 인스턴스 요금에 비해 상당한 할인 혜택을 제공한다.

#### 스팟 인스턴스
아마존에서 노는 컴퓨터( 꺼져있는 컴퓨터 )가 많아질 수록 스팟 인스턴스 가격이 하락한다. 단 꺼져있는 컴퓨터가 별로 없으면 인스턴스 가격이 상승한다.

<br>

***
## EC2 운영체제별로 원격 제어하는 방법
### 윈도우 -> 리눅스 EC2 인스턴스로 접속
리눅스는 SSH를 통해서 원격 제어를 해야하는데 윈도우는 SSH를 갖고있지 않다. <br> 따라서 윈도우에 SSH 역할을 해줄 프로그램을 직접 다운로드 받아야 한다.

> **참조**<br>
> [Xshell](https://www.netsarang.com/ko/free-for-home-school/)\

<br>

### Xshell 명령어
`sudo apt-get`: 특정한 프로그램을 다운로드 받을 수 있게해준다.
  - `sudo`란: super do의 약자로 관리자 권한으로 강제 실행한다는 뜻이다.
`sudo apt-get install apache2`: 아파치 톰캣 다운로드
`sudo apt-get update`: 가상 앱스토어를 업데이트 해준다.

<br>

정상적으로 설치된 것을 확인하기 위해 인스턴스의 도메인으로 접속한다.<br>

> **참조**<br>
> 인스턴스의 도메인은 인스턴스 세부사항 정보에서 확인할 수 있다.

<br>

**Xshell 에서**
1. `cd /var/www/html`로 디렉토리 이동
2. `sudo rm index.html`로 톰캣 기본 페이지를 삭제하고
3. `sudo nano index.html`로 새로운 index.html 페이지를 작성한다.

<br>

인스턴스의 도메인으로 다시 접속해보면 작성한 index.html 파일을 확인할 수 있다.

***
## AMIs( Amazon Machine Images )
인스턴스를 이미지화 해놓는 작업을 의미한다. 기존 인스턴스를 똑같이 복제해서 백업해 놓는다는 의미에서 굉장히 유용하다. AMIs를 통해 인스턴스를 복제할 때 보안 그룹 설정은 그대로 가져와지지 않기 때문에 다시 설정해줘야 한다.

<br>

***
## 부록
### docker 없이 jenkins를 설치했을 때 백그라운드로 실행시키는 법
- linux 환경: `nohup java -jar jenkins.war --httpPort=8888 & `
  - 우선적으로 jenkins.war 가 설치된 디렉토리로 이동해야한다.

### ssh으로 다른 인스턴스에 접근 시 permission denied 에러처리
https://typingdog.tistory.com/102

### jenkins에서 build시 gradlew permission 관련 문제
git bash에서 `git update-index --chmod=+x gradlew`를 입력해서 gradlew permission을 업데이트해준다.

### jar파일이 여러개라서 jenkins 빌드가 실패할 때
스프링부트 2.5버전 이후로 jar 파일이 2개씩 생성된다.
- build.gradle
   ```
   jar {
      enabled = false
   }
   ```
- Dockerfile의 `COPY ${JAR_FILE} app.jar`, `ENTRYPOINT ["java", "-jar", "/app.jar"]`에 좀 더 명확한 경로를 지정해준다.


### 도메인뒤에 포트번호를 생략하고 싶을때 ( 포트 리다이렉트 )
- 현재 포트 상황 확인 `sudo iptables -t nat -L`
- 80포트를 8080포트로 리다이렉트
  - `sudo iptables -t nat -A PREROUTING -p tcp --dport 80 -j REDIRECT --to-ports 8080`