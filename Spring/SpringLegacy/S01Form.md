# Spring - Form
## 폼값을 받는 방법
### 일반적인 방식
- 파라미터로 전달되는 값을 ```HttpServletRequest``` 객체를 통해 받는다.
- JSP와 Servlet에서의 방법과 동일하다.
    ```java
    @RequestMapping("/form/servletRequest")
	public String loginMember(HttpServletRequest req, Model model) {
		String id = req.getParameter("id");
		String pw = req.getParameter("pw");
		
		model.addAttribute("id", id);
		model.addAttribute("pw", pw);
		
		return "01Form/servletRequest";
	}
    ```

### @RequestParam 어노테이션
- 파라미터 형식으로 아래와 같이 사용한다.
- 이 방식을 활용하면 해당 메소드 내에서 변수명을 그대로 사용할 수 있다.
    ```java
    @RequestMapping("/form/requestParam.do")
	public String joinMember(Model model,
				@RequestParam("id") String id,
				@RequestParam("pw") String pw {
		
		MemberDTO memberDTO = new MemberDTO();
		memberDTO.setId(id);
		memberDTO.setPw(pw);
        
		model.addAttribute(memberDTO);
		
		return "01Form/requestParam";
	}
    ```

### 커맨드 객체
- 커맨드 객체는 편하지만 조건이 있다.
  - 커맨드 객체를 사용할 때는 매개변수의 이름을 해당 클래스명의 맨 앞글자만 소문자로 바꿔서 사용해야한다.
    - 예를 들면 ```MemberDTO memberDTO```
  - 커맨드 객체를 만들 때 파라미터의 갯수는 달라도 상관 없으나 파라미터 명과 클래스의 멤버 변수 명은 반드시 동일해야한다.
  - 해당 클래스에 ```getter/setter```가 반드시 존재해야한다.
    ```java
    @RequestMapping("/form/commandObjGet.do")
	public String commandObjGet(MemberDTO memberDTO) {
		return "01Form/commandObjGet";
	}
    ```
- ***궁금증? 파일 업로드할 때도 커맨드 객체를 사용할 수 있을까?***

### @PathVariable 어노테이션
- 요청명 뒤에 경로처럼 붙은 값이 실제 사용 가능한 파라미터가 된다.
- 이때 주의해야할 점은 웹브라우저는 요청명을 경로로 인식하므로 리소스를 사용할 때 경로에 주의해야한다.
- 전달되는 파라미터의 갯수가 달라지면 404에러가 발생한다.
    ```java
    @RequestMapping("/form/{memberId}/{memberName}")
	public String pathVariable(Model model,
			@PathVariable String memberId,
			@PathVariable String memberName) {
		
		model.addAttribute("memberId", memberId);
		model.addAttribute("memberName", memberName);
		
		return "01Form/pathVariable";
	}
    ```

## 폼값 검증
### 폼값 검증을 위한 컨트롤러 준비
```java
@RequestMapping("/validate/registProc.do")
	public String registProc(
			@ModelAttribute("mInfo") MemberDTO memberDTO,
			BindingResult result,
			Model model){ 

        String viewPage = "03Validate/memberDone";

        MemberValidator validator = new MemberValidator();
		validator.validate(memberDTO, result);

        if(result.hasErrors()) {
			System.out.println("유효성 체크 실패: " + result.toString());
			model.addAttribute("formError", "폼값 유효성 체크에 실패했습니다.");
			viewPage = "03Validate/memberRegist";
		}
		
		return viewPage;
    }
```
- 첫번째 파라미터
  - 폼값을 커맨드 객체로 한번에 받는다.
  - 어노테이션을 통해 모델 객체로 이름을 바꿔서 저장한다.
- 두번째 파라미터
  - ```BindingResult``` 객체는 ```validator```를 통해 폼값을 검증한 후 해당 결과를 반환받을 객체이다.
  - 폼값에 문제가 있다면 해당 객체를 통해 확인할 수 있다.
- 세번째 파라미터
  - 데이터를 저장할 ```Model``` 객체

### 폼값 검증을 위한 validator 클래스
- 유효성 검증을 위한 클래스 정의를 위해 Validator 인터페이스를 구현한다.
- supports(), validate() 두개의 메소드를 오버라이딩 한다.
    ```java
    public class MemberValidator implements Validator{
        
        /*
        supports()
            매개변수로 전달된 객체가 유효성 검증을 지원할 수 있는 커맨드 객체인지 판단한다.
            만약 해당 메소드를 통과하지 못하면 유효성 검증을 위한 validate() 메소드는 
            호출되지 않는다. 해당 메소드는 자동으로 호출된다.
        */
        @Override
        public boolean supports(Class<?> clazz) {
            return MemberDTO.class.isAssignableFrom(clazz);
        }
        
        /*
        validate()
            supports() 가 true를 반환하는 경우에만 호출되는 메소드로
            실제 폼값에 대한 검증을 진행한다.
                매개변수1 : 폼값을 저장한 커맨드 객체
                매개변수2 : 에러정보를 저장할 Errors 타입의 변수
        */
        @Override
        public void validate(Object obj, Errors errors) {
            System.out.println("validate() 메소드 호출됨");
            
            MemberDTO memberDTO = (MemberDTO)obj;
            
            // 검증1 : 단순 if문을 사용하는 방식(아이디 검증)
            String member_id = memberDTO.getId();
            if(member_id == null || member_id.trim().isEmpty()) {
                System.out.println("아이디가 null 입니다.");
                // 검증에 실패한 경우 해당 메소드를 통해 검증 결과를 반환한다.
                errors.rejectValue("id", "idError");
            }
            
            // 검증2 : 내장 메소드를 통한 검증(패스워드 검증)
            /*
            유효성 검증을 위해 전달되는 객체가 커맨드 객체가 아니라면 validate()
            메소드 진입 자체가 블가능하므로, 해당 함수에서 "pw"라는 문장은 getter()를
            호출하여 저장된 패스워드를 얻어온다고 생각하면 된다. 
            */
            ValidationUtils.rejectIfEmptyOrWhitespace(errors, "pw", "pwError");
            
            
            // 검증 3 : 사용자 정의 메소드를 통해 검증(이름 검증)
            boolean nameValidate =
                    myEmptyOrWhitespace(memberDTO.getName());
            if(nameValidate == false) {
                System.out.println("이름이 null 입니다.");
                errors.rejectValue("name", "nameError");
            }
            
        }

        private boolean myEmptyOrWhitespace(String value) {
            if(value==null || value.trim().length()==0)
                return false;
            else
                return true;
        }
    }
    ```