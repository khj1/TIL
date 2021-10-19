# Spring - Cotroller, RequestMapping
## Spring의 요청 처리
```DispatcherServlet```은 사용자의 요청을 제일 먼저 받는다. 이후 기본 패키지로 지정된 경로를 스캔하여 컨트롤러 클래스를 찾아낸다. 그리고 해당 요청명에 매핑된 메소드를 찾아 요청을 전달하고 비즈니스 로직을 수행한다.

## @Controller
해당 클래스를 컨트롤러로 사용하고 싶을 때 클래스명 앞에 ```@Controller``` 어노테이션을 선언한다. 패키지를 스캔할 때 ```@Controller``` 어노테이션이 있는 클래스를 찾아 요청을 전달한다.

## @RequestMapping
요청명을 매핑한다. 요청명은 서블릿과 마찬가지로 컨텍스트 루트를 제외한 나머지 경로명으로 이루어진다. 요청명의 매핑 정보로 메소드를 호출하게되므로 메소드 명은 큰 의미가 업다. 개발자가 구분하기 좋은 정도의 이름으로 설정하면 된다.

### 요청명의 속성
단순히 요청명만 매핑하는 경우 ```value```, ```method```를 생략할 수 있으나 전송 방식까지 명시해야할 경우에는 속성을 제거하면 에러가 발생한다.
```java
@RequestMapping(value="요청명", method=RequestMethod.전송방식)
```

### ModelAndView 객체
View를 호출하고 View로 전송할 데이터를 저장하는 2가지 기능을 동시에 처리할 수 있는 클래스이다.
```java
public ModelAndView postLogin(
        @RequestParam("user_id") String id,
        @RequestParam("user_pw") String pw
        ) {
    ModelAndView mv = new ModelAndView();
    
    // 뷰의 경로 및 파일명 설정
    mv.setViewName("02RequestMapping/postLogin");
    
    // 속성명과 속성값 지정
    mv.addObject("id", id);
    mv.addObject("pw", pw);
    
    /*
    ModelAndView 객체를 반환하여 뷰를 호출한다.
    해당 메소드의 반환 타입도 ModelAndView로 설정해야한다.
    */
    return mv;
}
```

### @ModelAttribute 어노테이션
뷰로 전달되는 커맨드 객체의 이름을 임의로 변경하고 싶을 때 사용한다.
```java
@RequestMapping("/requestMapping/modelAttribute.do")
	public String studentInfo(
			@ModelAttribute("si") StudentDTO studentDTO) {
		return "02RequestMapping/modelAttribute";
	}
```

## 부록
- [프레임워크와 라이브러리의 차이](https://juyeop.tistory.com/23)