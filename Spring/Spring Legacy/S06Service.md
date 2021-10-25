# Spring - Service
## Spring의 service 객체
Service 객체는 Controller와 Model 사이에서 비즈니스 로직을 실행하는 역할을 한다.
- **Controller** : 요청 분석 후 서비스 객체 호출
- **Service** : 요청을 처리할 비즈니스 로직 실행
- **Model** : DB관련 CRUD 작업

> ### ```@Service``` 어노테이션
servlet-context.xml에서 component-scan으로 설정된 기본패키지에 서비스 객체의 역할을 부여하기 위한 어노테이션이다.  스프링 컨테이너 시작 시 자동으로 스캔되어 빈이 생성된다.
> ### ```@Repository``` 어노테이션
servlet-context.xml에서 component-scan으로 설정된 기본패키지에 모델의 역할을 부여하기 위한 어노테이션이다.  스프링 컨테이너 시작 시 자동으로 스캔되어 빈이 생성된다.
***
## 예시
> ### Model
```java
@Repository
public class MyDAO {
	// CRUD 작업을 진행한다.
	public void selectList() { 리스트 출력 실행부 }
}
```
> ### Service
```java
@Service
public class MyService {
	
    // Model을 주입받아 사용한다.
    @Autowired
	MyDAO myDAO;
	
	/*
	해당 요청에 대한 비즈니스 로직을 처리한다.
	처리 중 DB 작업이 필요하다면 Model( DAO )를 호출한다.
	 */
	public void execute() { 
		myDAO.selectList();
	}
}
```
> ### Controller
```java
@Controller
public class ServiceController {

	// 서비스 객체를 주입받는다.
	@Autowired
	MyService myService;
	
	/*
	컨트롤러는 요청을 분석한 후 적절한 서비스 객체를 호출한다.
	이때 컨트롤러가 받은 모든 요청( request 객체 )을 모두 전달한다.
	 */
	@RequestMapping("/service/myService.do")
	public String myService() {
		myService.execute();
		
		return "07Service/myService";
	}
}
```