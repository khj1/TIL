# Spring - Lombok
롬복(Lombok)이란, BoilerPlate 코드를 줄여주는 자바 라이브러리이다. BoilerPlate 코드란 꼭 필요하지만 간단한 기능을 수행하는 반복적인 코드를 의미한다. 자바로 어플리케이션을 개발하는 경우, 데이터를 저장하는 클래스를 자바빈 규칙에 맞게 작성하게 된다.

예를들어 ```DTO``` 또는 ```VO```와 같이 데이터를 저장하는 클래스를 정의했을때 멤버 변수에 변화가 생기면 그때마다 ```getter```/```setter```를 적절히 수정해줘야한다. 이러한 번거로움을 해결하기 위해 만들어진 것이 롬복이다.

## Lombok 설정
> ### pom.xml( 의존 설정 )
```xml
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.20</version>
</dependency>
```
> ### lombok 설치
- 의존 설정 후에 ```lombok```을 설치해줘야한다.
- cmd 창을 통해 설치하는 방법
  - ```C:\Users\본인의계정명\.m2\repository\org\projectlombok\lombok\버전```
  - ```C:\> java -jar lombok-버전.jar``` 명령을 실행한다.
  - ```Specify location``` 버튼을 누른후 이클립스가 설치된 위치를 찾아 실행파일인 eclipse.exe를 선택한다. 

***
## Lombok 사용
- ```@Data``` 어노테이션 만으로 ```getter```/```setter```/생성자/```toString``` 등을 자동으로 생성할 수 있다.
    ```java
    @Data
    public class ParameterDTO {
        private String user_id;
        private String user_pass;
    }
    ```