# JSP에서의 파일 업로드
파일 업로드를 구현할 때는 form의 method 속성은 반드시 POST 방식이어야 한다.(GET 방식으로 지정해주면 파일명만 텍스트 형식으로 전송된다.) POST 방식일 경우 아래 2가지 형태의 인코딩 방식이 있다. (인코딩 방식은 ```enctype```에 설정해준다.)
- ```application/x-www-form-urlencode```
  - 파일 이름만 전송됨 (디폴트 값)
- ```multipart/form-data```
  - 파일 이름과 함께 데이터도 전송된다.

## 형식
```html
<form method="post" enctype="multipart/form-data" action="업로드처리 파일경로">
	<input type="file" name="input이름" />
</form>
```
- form의 enctype은 **multipart/form-data**로 지정해야한다.
  - enctype이 **multipart/form-data** 일 경우 폼값은 request 객체로 받는것이 아니라 **MultipartRequest** 객체를 통해 받아야한다.
- form의 하위 요소로는 ```<input type ="file">``` 태그를 사용한다.

## MultipartRequest API를 이용한 업로드
### API 다운로드 및 환경 설정
- http://www.servlets.com/cos/ → cos-26Dec2008.zip 파일을 다운로드한다.
- lib폴더에 있는 cos.jar파일을 복사 하여 WEB-INF/lib폴더에 붙여 넣는다. (배포 목적)

### com.oreilly.servlet.multipart패키지
파일 중복 시 자동으로 인덱스를 부여해주는 기능을 제공한다. (**FileRenamePolicy** 와 **DefaultFileRenamePolicy**)

- 생성자
    ```java
    MultipartRequest(
        javax.servlet.http.HttpServletRequest request,
        java.lang.String saveDirectory,
        int maxPostSize,
        java.lang.String encoding,
        FileRenamePolicy policy
    ) 
    ```
    - saveDirectory
      - 파일이 저장될 경로를 지정한다.
      - ```String saveDirectory = application.getRealPath("/Uploads");```
    - maxPostSize
      - 파일의 최대 크기를 지정한다.
      - 전송한 파일이 3개라면, 3개를 합친 용량이 기준이 된다.
      - 파일 크기가 초과될 때는 IOException이 발생된다.
    - encoding
      - 케릭터 셋을 지정한다.
    - policy
      - 중복된 파일이 있으면 파일명 재정의 정책을 사용한다.
      - 파일명이 중복될 경우 자동으로 파일명 뒤에 인덱스 번호가 붙는다.
- MultipartRequest 객체를 생성하면 파일이 업로드된다.
- 업로드 용량 초과시에는 에러가 발생한다. 즉 MultipartRequest 객체가 생성되지 않는다.
- 첨부파일을 첨부하지 않았을 때는 MultipartRequest 객체가 생성되긴하지만 MultipartRequest의 ```getFilesystemName("파라미터명")```으로 값을 읽어올 때 null이 반환된다.
- 폼에서 파일명을 얻어올 때는 ***MultipartRequest 객체의*** ```getFilesystemName("파라미터명")```을 사용하고, 다른 폼 요소 값을 얻어올 때는 ***MultipartRequest 객체의*** ```getParameter("파라미터명")```으로 얻어온다.
- form에서 encytype을 "multipart/form-data"로 설정하는 경우, 사용자가 입력하는 내용과 업로드되는 파일내용은 한번에 서버에 전달된다. 이때 사용자가 입력한 내용과 파일 내용을 구분하기 위해 특수문자를 사용하는데 이것을 **boundary**라고 한다. 바운더리 문자는 "--"로 시작한다.

## 주요 메소드
- ```String getContentType(name : String)```
  - 파일의 [MIME](#부록)(Multipurpose Internet Mail Extensions) 타입을 리턴한다.
- ```File getFile(name : String)```
  - 업로드되어 서버에 저장된 파일의 File 객체를 리턴한다.
- ```Enumeration getFileNames()```
  - 업로드된 모든 파일의 이름을 리턴한다.
- ```String getFilesystemName(name : String)```
  - 업로드된 파일의 이름을 리턴한다. 중복된 파일을 올릴 경우 새롭게 재정의된 파일 이름을 반환한다.
- ```String getOriginalFileName(name: String)```
  - 재정의 되기 전의 원래 파일명을 리턴
- ```String getParameter(name : String)```
  - 클라이언트로부터 전달된 파라미터 값을 리턴한다(다른 폼 요소 얻어올 때)
- ```Enumeration getParameterNames()```
  - 클라이언트로부터 전달된 파라미터 이름들을 리턴한다.

# JSP에서의 파일 다운로드
## 파일 다운로드를 위한 응답헤더 설정
```java
// 응답헤더를 초기화 시켜준다.
response.reset();

// 응답헤더의 컨텐츠 타입(MIME)을 설정한다.
// 브라우저가 인식하지 못하는 컨텐츠 타입을 설정해준다.
// 이를 통해 브라우저는 반드시 파일 다운로드 창을 띄워준다.
response.setContentType("application/octet-stream");

// 파일명 변경을 위한 응답헤더 설정
response.setHeader("Content-Disposition", 
    "attachment; filename=\"" + originalFileName + "\"");

// 파일 사이h즈 설정
response.setHeader("Content-Length", "" + file.length());
```
- 파일 다운로드 구현시 out 객체를 초기화 시켜줘야한다.
  - JSP가 열린 상태에서 다운로드를 위해 또 다른 JSP를 열면 출력 스트림이 중복으로 생성되어 예외가 발생하게된다. ```out.clear();```

# 부록
- ofile과 sfile
  - ofile
    - Original File Name: 원본 파일명을 저장함.
    - 컴퓨터에게 중요하다.
  - sfile
    - Saved File Name: 서버에 저장된 파일명.
    - 사용자에게 중요하다. 
- Ctrl + Del - 해당 커서에서부터 다음 문자열까지의 공백제거
- MIME 타입이란?
  - Multipurpose Internet Mail Extensions의 약자로 간단히 말하면 파일 변환을 의미한다.현재는 웹을 통해 여러 형태의 파일을 전달하는데 사용하고 있지만 이 용어가 생길 땐 이메일과 함께 동봉할 파일을 텍스트 문자로 전환해서 이메일 시스템을 통해 전달하기 위해 개발되어 Internet Mail Extensions라고 불리기 시작했다고 한다.
- MIME 사용 이유
  - 예전에는 텍스트 파일을 주고 받는데에 ASCII로 공통된 표준에 따르기만 하면 문제가 없었으나 네트워크를 통해 ASCII가 아닌 바이너리 파일을 보내는 경우가 생기게 되었다. 음악파일, 무비파일, 워드파일 등등 ASCII만으로는 전송이 안되기 때문에 기존 시스템에서 문제 없이 전달하기 위해서는 텍스트로의 변환이 필요했다.
  - 텍스트 파일로 변환하는 것을 인코딩(Encoding), 텍스트 파일을 바이너리 파일로 변환하는 것을 디코딩(Decoding)이라고 한다.
  - MIME으로 인코딩한 파일은 Content-type정보를 앞부분에 담게되며 Content-type은 여러가지 타입이 있다.
  - 웹 브라우저에서 서버에 접속하여 html 문서를 요청하면서 html문서에 있는 이미지 파일의 경로를 불러올 수 있다. 이러한 과정에서 이미지의 경로에 있는 파일이 웹브라우저에서 지원되는 MIME-Type이라면 웹브라우저를 이용하여 열어볼 수 있다.
  - 바이너리파일(음악 파일, 무비 파일, 워드 파일 등) 또한 마찬가지 이다. 주로 쓰고 있는 대부분의 포맷인 .gif .jpg .mov 등등의 파일들은 웹 브라우저에서 무리없이 열리게 되는데 브라우저에서 지원하지 못하는 유형은 따로 지정해줘야 한다.

- File.separator란?
  - 파일의 경우 OS에 따라서 파일을 구분하는 구분자가 다양하다.
    - 리눅스 - C:/02Workspaces
	- 윈도우 - C:\02Workspaces
  - 따라서 파일 생성시 File.separator를 사용해서 해당 파일이 실행되는 운영체제 환경에 맞춰 구분자가 유연하게 변할 수 있도록 만들어준다.
    ```java
    File oldFile = new File(saveDirectory + File.separator + fileName);

    //또는
    File oldFile = new File(saveDirectory, fileName);
    ```

- File 클래스
  - 생성자의 매개 변수에 파일의 물리적 경로를 전달해준다.(```getRealPath()```)
  - 해당 경로에 실제로 파일이 존재하면 해당 File 객체로 해당 파일을 수정해줄 수 있고, 실제로 파일이 존재하지 않는다면 해당 경로의 파일 객체만 생성해준다(가상의 파일).
  - File 클래스의 주요 메소드
    - ```renameTo()```
      - 파일을 이동시켜주는 메소드이다. 파일의 경로가 같을 경우 파일의 이름이 바뀐다. 경로가 다르다면 파일이 해당 경로로 이동한다.
      - 파일 이름 변경
        - 변경전 : /files/image/A.jpg
        - 변경후 : /files/image/B.jpg
      - 파일 이동
		- 변경전 : /files/image-a/A.jpg
		- 변경후 : /files/image-b/A.jpg
	
      - 파일 이동 및 파일명 변경
		- 변경전 : /files/image-a/A.jpg
		- 변경후 : /files/image-b/B.jpg
    - ```exists()```
      - 파일이 해당 경로에 실재하는지 판단한다.