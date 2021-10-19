# JSP (Java Server Page)
## 지시어(Directive)
```jsp
<%@ %>
```
### 페이지의 속성을 지정하거나 외부 문서를 포함할 때 사용한다.
### page
- contentType
- import
- info
- language
- session
- buffer
    -  해당 JSP에서 버퍼를 사용할 지 여부를 결정한다.
    -  8kb가 기본값이고 none으로 설정 시 버퍼를 사용하지 않게된다.
- autoflush
    - 버퍼가 채워졌을 때 자동으로 flush(버퍼에 남아있는 데이터를 모두 내보냄)할 지 결정한다. false로 지정할 경우 예외를 발생시키고 작업을 중지한다.
    - buffer는 공간이 가득찼다고 error를 발생시키지 않는다. 다만 autoflush가 false로 지정되있다면 자동적으로 flush 되지 않으므로 예외가 발생한다.
    - 자바에서는 buffer가 가득 차기 전에 프로그램이 종료되면 buffer에 남아있는 내용이 파일에 쓰여지지 않는다. 따라서 flush()를 통해 buffer의 내용을 강제적으로 꺼내줘야한다.
    - 자바의 close() 함수는 flush() 메소드와 비슷해보인다. 실제로도 close() 함수가 내부적으로 flush()함수를 호출한다.
- errorPage
- isErrorPage
- pageEncoding
- trimDirectiveWhitespaces

### include
- 해당 페이지의 소스가 그대로 삽입된다.
- 모든 jsp 페이지에서 공유하는 변수를 선언하거나 메소드를 정의할 때 사용한다.
- HTML로 해석된 결과가 아니라 소스 자체가 그대로 포함되어 자바코드로 변환된다.
- 지시어 안에서는 JSP코드(스크립팅 원소)는 사용이 불가하다.

### taglib