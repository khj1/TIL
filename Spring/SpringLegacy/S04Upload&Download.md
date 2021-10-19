# Spring - 파일 업로드
## 기본 설정
- ```servlet-context.xml``` 에 빈을 생성해줘야 한다.
    ```xml
    <!-- 파일 업로드를 위한 빈 생성 -->
	<beans:bean id="multipartResolver"
		class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
		<!-- 업로드 할 파일의 최대 용량 : 1Mb -->
		<beans:property name="maxUploadSize" value="1000000" />
	</beans:bean>
	<!-- 파일 다운로드를 위한 빈 생성 -->
    <!-- FileDownLoadView 클래스는 직접 만들어야한다. -->
	<beans:bean id="fileDownloadView" class="common.FileDownLoadView" />
	<beans:bean id="fileViewResolver" 
		class="org.springframework.web.servlet.view.BeanNameViewResolver">
		<beans:property name="order" value="0" />
	</beans:bean>
    ```

## 서버의 물리적 경로 얻어오기
```java
@RequestMapping("/fileUpload/uploadPath.do")
    public void uploadPath(HttpServletRequest req, HttpServletResponse resp) throws IOException{
        // request 객체를 통해 upload 폴더의 물리적 경로를 얻어온다.
        // "/resources/upload"는 파일이 업로드 되는 곳이다.
        String path = req.getSession().getServletContext().getRealPath("/resources/upload");
        
        // response 객체를 통해 MIME 타입을 설정한다.
        resp.setContentType("text/html; charset=utf-8");
        
        // View를 호출하지 않고 컨트롤러에서 내용을 즉시 출력한다.
        PrintWriter pw = resp.getWriter();
        pw.print("/upload 디렉토리의 물리적 경로: " + path);
    }
```
***
## 파일 업로드
파일 업로드는 반드시 **POST** 방식으로 전송해야 한다. 따라서 컨트롤러 매핑 시 ```method```, ```value``` 두 가지 속성을 모두 기술해줘야 한다.
```java
@RequestMapping(value="/fileUpload/uploadAction.do", method=RequestMethod.POST)
    public String uploadAction(Model model, MultipartHttpServletRequest req) {
        
        // 업로드 폴더의 물리적 경로 얻어오기
        String path = req.getSession().getServletContext().getRealPath("/resources/upload");
        
        // 폼값과 파일명을 저장 후 View로 전달하기 위한 맵 컬렉션
        Map returnObj = new HashMap();
        try {
            // 업로드 폼의 file 속성 필드를 얻어온다. (전송된 파일이 여러개인 경우)
            // getFileNames() 메소드는 Iterator를 반환한다.
            Iterator<String> itr = req.getFileNames();
            
            // 업로드 처리를 위한 각종 변수 생성
            MultipartFile mfile = null;
            String fileName = "";
            List resultList = new ArrayList();
            
            // 파일 외 폼값을 전송받음
            String title = req.getParameter("title");
            
            /*
            물리적 경로를 기반으로 File 객체를 생성한 후 지정된 디렉토리가 존재하는지
            확인한다. 만약 없다면 디렉토리를 생성한다.
                */
            File directory = new File(path);
            if(!directory.isDirectory()) {
                directory.mkdirs();
            }
            
            // 업로드 폼의 file 필드 갯수만큼 반복
            while(itr.hasNext()) {
                // 전송된 파일의 이름을 읽어온다.
                fileName = itr.next();
                mfile = req.getFile(fileName);
                
                // 한글 깨짐 방지 처리 후 파일명을 가져옴
                String originalName = 
                        new String(mfile.getOriginalFilename().getBytes(), "UTF-8");
                
                // 만약 서버로 전송된 파일이 없다면 while 문의 처음으로 돌아간다.
                if("".equals(originalName)) {
                    continue;
                }
                
                // 파일의 확장자를 얻어온다.
                String ext = originalName.substring(originalName.lastIndexOf("."));
                
                // UUID를 통해 얻어온 문자열에 확장자를 붙여준다.
                String saveFileName = getUuid() + ext;
                
                // 물리적 경로에 새롭게 생성된 파일명으로 파일을 저장한다.
                File serverFullName = new File(path + File.separator + saveFileName);
                mfile.transferTo(serverFullName);
                
                Map file = new HashMap();
                file.put("originalName", originalName);
                file.put("saveFileName", saveFileName);
                file.put("serverFullName", serverFullName);
                file.put("title", title);
                
                resultList.add(file);
            }
            returnObj.put("files", resultList);
        }
        catch (IOException e) {
            e.printStackTrace();
        }
        catch (Exception e) {
            e.printStackTrace();
        }
        
        model.addAttribute("returnObj", returnObj);
        return "06FileUpload/uploadAction";
    }
```

***
## 서버에 저장된 파일 목록보기
```java
@RequestMapping("/fileUpload/uploadList.do")
public String uploadList(HttpServletRequest req, Model model) {
    // 서버의 물리적 경로 얻어오기
    String path = req.getSession().getServletContext().getRealPath("/resources/upload");
    
    // 경로를 기반으로 파일 객체 생성
    File file = new File(path);
    
    // 파일의 목록을 배열 형태로 얻어옴
    File[] fileArray = file.listFiles();
    
    // 뷰로 전달할 파일 목록을 저장하기 위해 Map 컬렉션 생성
    Map<String, Integer> fileMap = new HashMap<String, Integer>();
    for(File f : fileArray) {
        // Key 값으로 파일명, value 값으로 파일의 용량을 저장
        fileMap.put(f.getName(), (int)Math.ceil(f.length()/1024.0));
    }
    model.addAttribute("fileMap", fileMap);
    
    return "06FileUpload/uploadList";
}
```
***
## 파일 다운로드
- 컨트롤러
```java
@RequestMapping("/fileDownload/download.do")
public ModelAndView download(HttpServletRequest req, HttpServletResponse resp) throws Exception{
    // 저장된 파일명
    String fileName = req.getParameter("fileName");
    
    // 원본 파일명
    String oriFileName = req.getParameter("oriFileName");
    
    // 물리적 경로
    String saveDirectory = req.getSession().getServletContext().getRealPath("/resources/upload");
    
    // 경로와 파일명을 통해 파일 객체 생성
    File downloadFile = new File(saveDirectory + "/" + fileName);
    
    // 해당 경로에 파일이 있는지 확인
    if(!downloadFile.canRead()) {
        throw new Exception("파일을 찾을 수 없습니다.");
    }
    
    ModelAndView mv = new ModelAndView();
    mv.setViewName("fileDownloadView");
    mv.addObject("downloadFile", downloadFile);
    mv.addObject("oriFileName", oriFileName);
    
    return mv;
}
```
- ```FileDownLoadView``` 클래스
```java
public class FileDownLoadView extends AbstractView {
	
	// 생성자에서 MIME 타입 설정
    public FileDownLoadView(){
        setContentType("application/download; charset=utf-8");
    }
    
    @Override
    protected void renderMergedOutputModel(Map<String, Object> model,
            HttpServletRequest req, HttpServletResponse res) throws Exception {
       
        File file = (File) model.get("downloadFile");
        String oriFileName = (String) model.get("oriFileName");

        /* model 객체에서 파일명을 얻어온 후 다운로드를 진행한다. */
        
        // 저장된 파일명을 원본 파일명으로 변경한 후 다운로드 할 수 있도록
        // response 객체를 통해 설정한다.
        res.setContentType(getContentType());
        res.setContentLength((int) file.length());
        res.setHeader("Content-Disposition", "attachment; filename=\"" + 
                java.net.URLEncoder.encode(oriFileName, "utf-8") + "\";");
        res.setHeader("Content-Transfer-Encoding", "binary");
        
        OutputStream out = res.getOutputStream();
        FileInputStream fis = null;
        
        try {          
            fis = new FileInputStream(file);
            FileCopyUtils.copy(fis, out);            
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            if(fis != null) {
                try { 
                    fis.close(); 
                }catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }        
        out.flush();
    }
}
```

***
## 부록
- UUID (Universally Unique IDentifier)
    - UUID는 **범용 고유 식별자**이다.
    - 서버에 파일을 저장할 때 파일명이 한글인 경우 문제가 생길 수 있다. 이를 방지하기 위해 UUID를 사용해 파일명을 재정의 해준다.
    - ```UUID.randomUUID()``` 를 통해 문자열을 생성한다.
    - 하이픈이 4개 포함된 32자의 랜덤하고 유니크한 문자열이 생성된다.
    - JDK에서 기본 클래스로 제공한다.
    ```java
    public static String getUuid() {
        String uuid = UUID.randomUUID().toString();
        System.out.println("생성된 UUID-1: " + uuid);
        // uuid 중간에 포함된 하이픈을 제거한다.
        uuid = uuid.replaceAll("-", "");
        System.out.println("생성된 UUID-2: " + uuid);
        return uuid;
    }
    ```