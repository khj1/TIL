# Mulitpart File
## 파일 업로드
### HTML 폼 전송 방식
#### `application/x-www-form-urlencoded`
- HTML 폼 데이터를 서버로 전송하는 가장 기본적인 방식
- Form 태그에 별도의 `enctype` 옵션이 없으면 웹브라우저는 요청 HTTP 메시지의 헤더에 `Content-Type: application/x-www-form-urlencoded`를 추가한다.
- 폼 데이터는 HTTP Body에 문자로 `username=kim&age=20` 와 같이 전송된다.

<br>

#### `application/x-www-form-urlencoded` 문제점
- 파일을 업로드 하려면 파일은 문자가 아니라 **바이너리 데이터**를 전송해야 한다.
- 보통 폼 데이터로 파일만 전송하는 경우보다는 문자와 바이너리를 동시에 전송해야 하는 경우가 더 많다.
- 이 문제를 해결하기 위해 HTTP는 `multipart/form-data` 전송 방식을 제공한다.

<br>

#### `multipart/form-data`
![multipart/form-data](../../img/Multipart%20form-data.png)
- `multipart/form-data` 방식은 다른 종류의 여러 파일과 폼의 내용을 함께 전송할 수 있다.
- `multipart/form-data`를 사용하기 위해선 `Form` 태그에 `enctype="multipart/form-data"`를 지정해야한다.
- HTTP 메시지를 보면 각각의 전송 항목이 `boundary`를 기준으로 구분되어 있다.
- 각 항목에는 `Content-Disposition`이라는 헤더가 추가되어 있고, 여기에 부가 정보가 담겨있다.
- 파일이 전송되는 경우 파일 이름과 `Content-Type`이 추가된다.

<br>

### 서블릿 파일 업로드
#### 로그 설정
    logging.level.org.apache.coyote.http11=debug

- HTTP 요청 메시지를 로그로 확인하기 위해 설정해준다.
<br>

#### 컨트롤러
```java
@Slf4j
@Controller
@RequestMapping("/servlet/v1")
public class ServletUploadControllerV1 {

    @PostMapping("/upload")
    public String saveFileV1(HttpServletRequest request) throws ServletException, IOException {
        String itemName = request.getParameter("itemName");
        Collection<Part> parts = request.getParts();

        return "upload-form";
    }
}
```
- `request.getParts()`
  - `multipart/form-data` 전송 방식에서 각각 나누어진 부분을 받아서 확인할 수 있다.

<br>

#### 로그 결과
    Content-Type: multipart/form-data; boundary=----xxxx
    
    ------xxxx
    Content-Disposition: form-data; name="itemName"
    
    Spring
    ------xxxx
    Content-Disposition: form-data; name="file"; filename="test.data"
    Content-Type: application/octet-stream
    
    sdklajkljdf...

<br>

#### 멀티 파트 사용 옵션
**업로드 사이즈 제한**
    spring.servlet.multipart.max-file-size=1MB
    spring.servlet.multipart.max-request-size=10MB

큰 파일을 무제한 업로드하게 둘 수는 없기 때문에 업로드 사이즈를 제한할 수 있다.<br>
지정한 사이즈를 넘으면 `SizeLimitExceededException`예외가 발생한다.
- `max-file-size` : 파일 하나의 최대 사이즈, 기본 1MB
- `max-request-size` : 멀티파트 요청 하나에 여러 파일을 업로드 할 수 있는데, 그 전체 합이다. 기본 10MB

<br>

> **참고**<br>
> `spring.servlet.multipart.enabled=false` 로 지정하면 멀티 파트 데이터를 처리하지 않는다.( 기본값 `true` )<br><br>
> 일반적인 request : `HttpServletRequest` 객체 = `RequestFacade` <br>
> Mulipart request : `HttpServletRequest` 객체 = `StandardMultipartHttpServletRequest`
> <br><br>
> `spring.servlet.multipart.enabled` 옵션을 켜면 스프링의 `DispatcherServlet` 에서 멀티파트
리졸버( MultipartResolver )를 실행한다.<br>
> 멀티파트 리졸버는 멀티파트 요청인 경우 서블릿 컨테이너가 전달하는 일반적인 `HttpServletRequest` 를 `MultipartHttpServletRequest` 로 변환해서 반환한다.<br>
> `MultipartHttpServletRequest` 는 `HttpServletRequest` 의 자식 인터페이스이고, 멀티파트와 관련된
추가 기능을 제공한다.
><br><br>
> 스프링이 제공하는 기본 멀티파트 리졸버는 `MultipartHttpServletRequest` 인터페이스를 구현한
`StandardMultipartHttpServletRequest` 를 반환한다.
> 이제 컨트롤러에서 `HttpServletRequest` 대신에 `MultipartHttpServletRequest` 를 주입받을 수
있는데, 이것을 사용하면 멀티파트와 관련된 여러가지 처리를 편리하게 할 수 있다. 
> <br><br>
> 그런데 `MultipartFile` 이라는 것을 사용하는 것이 더 편하기 때문에 `MultipartHttpServletRequest`
를 잘 사용하지는 않는다. 더 자세한 내용은 `MultipartResolver` 를 검색해보자.

<br>

### 서블릿의 `Part` 객체
#### 파일 업로드 경로 설정
파일을 업로드 하려면 실제 파일이 저장되는 경로가 필요하다.<br>
**`application.properties`**
    
    file.dir=파일 업로드 경로 설정(예): /Users/kimyounghan/study/file/

**주의**<br>
- 해당 경로에 실제 폴더를 미리 만들어둬야 한다.
- `application.properties` 에서 설정할 때 마지막에 `/` (슬래시)가 포함된 것에 주의하자.

<br>

#### 컨트롤러
```java
@Controller
@RequestMapping("/servlet/v2")
public class ServletUploadControllerV2 {

    @Value("${file.dir}")
    private String fileDir;

    @PostMapping("/upload")
    public String saveFileV2(HttpServletRequest request) throws ServletException, IOException {

        String itemName = request.getParameter("itemName");
        Collection<Part> parts = request.getParts();

        for (Part part : parts) {
            Collection<String> headerNames = part.getHeaderNames();

            for (String headerName : headerNames) {
                log.info("header {} : {}", headerName, part.getHeader(headerName));
            }

            // 편의 메서드
            // content-disposition; filename
            log.info("submittedFileName={}", part.getSubmittedFileName());
            log.info("size={}", part.getSize());

            //데이터 읽기
            InputStream inputStream = part.getInputStream();
            String body = StreamUtils.copyToString(inputStream, StandardCharsets.UTF_8);
            log.info("body={}", body);

            //파일에 저장하기
            if (StringUtils.hasText(part.getSubmittedFileName())) {
                String fullPath = fileDir + part.getSubmittedFileName();
                log.info("파일 저장 fullPath={}", fullPath);
                part.write(fullPath);
            }
        }

        return "upload-form";
    }
}
```
- `@Value("${file.dir}")` 를 통해 `application.properties`에서 설정한 `file.dir` 값을 주입한다.
- 멀티파트 형식은 전송 데이터를 하나하나 각각 부분( `Part` )으로 나누어 전송한다.

<br>

#### `Part` 주요 메서드
- `part.getSubmittedFileName()` : 클라이언트가 전달한 파일명
- `part.getInputStream()`: Part의 전송 데이터를 읽을 수 있다.
- `part.write(...)`: Part를 통해 전송된 데이터를 저장할 수 있다.

<br>

#### 문제점
서블릿이 제공하는 `Part` 는 편하기는 하지만, `HttpServletRequest` 를 사용해야 하고, 추가로 파일
부분만 구분하려면 여러가지 코드를 넣어야 한다.

<br>

### 스프링 파일 업로드
스프링은 `MultipartFile` 인터페이스로 멀티파트 파일을 편리하게 지원한다.<br>

#### 컨트롤러
```java
@Slf4j
@Controller
@RequestMapping("/spring")
public class SpringUploadController {

    @Value("${file.dir}")
    private String fileDir;

    @PostMapping("/upload")
    public String saveFile(@RequestParam String itemName,
                           @RequestParam MultipartFile file, HttpServletRequest request) throws IOException {

        if (!file.isEmpty()) {
            String fullPath = fileDir + file.getOriginalFilename();
            file.transferTo(new File(fullPath));
        }

        return "upload-form";
    }
}
```
- `MultipartFile`은 `@RequestParam`뿐만 아니라 `@ModelAttribute`에서도 동일하게 사용할 수 있다.
  
<br>

#### `MultipartFile` 주요 메서드
- `file.getOriginalFilename()` : 업로드 파일 명
- `file.transferTo(...)` : 파일 저장

<br>

***
## 예제로 구현하는 파일 업로드와 다운로드
### 요구 사항
- 상품 이름
- 첨부 파일 하나
  - 첨부 파일을 업로드, 다운로드 할 수 있다.
- 이미지 파일 여러개
  - 업로드한 이미지를 웹 브라우저에서 확인할 수 있다.

<br>

### Item - 상품 도메인
```java
@Data
public class Item {

    private Long id;
    private String itemName;
    private UploadFile attachFile;
    private List<UploadFile> imageFiles;
}
```

<br>

### UploadFile - 업로드 파일 정보 보관
```java
@Data
public class UploadFile {
    private String uploadFileName;
    private String storeFileName;
    
    public UploadFile(String uploadFileName, String storeFileName) {
        this.uploadFileName = uploadFileName;
        this.storeFileName = storeFileName;
    }
}
```
- `uploadFileName` : 고객이 업로드한 파일명
- `storeFileName` : 서버 내부에서 관리하는 파일명
  - 고객이 업로드한 파일명으로 서버 내부에 파일을 저장하면 안된다.
  - 서로다른 고객이 같은 파일이름을 업로드하는 경우 기존 파일 이름과 충돌이 날 수 있다.
  - 서버에서는 저장할 파일명이 겹치지 않도록 내부에서 관리하는 별도의 파일명이 필요하다.

<br>

### ItemRepository
```java
@Repository
public class ItemRepository {

    // Map을 DB로 사용
    private final Map<Long, Item> store = new HashMap<>();
    private Long sequence = 0L;

    public Item save(Item item) {
        item.setId(++sequence);
        store.put(item.getId(), item);
        return item;
    }

    public Item findById(Long id) {
        return store.get(id);
    }
}
```

<br>

### FileStore - 파일 저장과 관련된 업무 처리
```java
@Component
public class FileStore {

    @Value("${file.dir}")
    private String fileDir;

    public String getFullPath(String fileName) {
        return fileDir + fileName;
    }

    public List<UploadFile> storeFiles(List<MultipartFile> multipartFiles) throws IOException {

            List<UploadFile> storeFileResult  = new ArrayList<>();

            for (MultipartFile multipartFile : multipartFiles) {
                if (!multipartFile.isEmpty()) {
                    storeFileResult.add(storeFile(multipartFile));
                }
            }
            return storeFileResult;
        }

    public UploadFile storeFile(MultipartFile multipartFile) throws IOException {
        if (multipartFile.isEmpty()) {
            return null;
        }

        String originalFilename = multipartFile.getOriginalFilename();
        String storeFileName = creatreStoreFileName(originalFilename);
        multipartFile.transferTo(new File(getFullPath(storeFileName)));

        return new UploadFile(originalFilename, storeFileName);
    }

    private String creatreStoreFileName(String originalFilename) {
        String uuid = UUID.randomUUID().toString();
        String ext = extractExt(originalFilename);
        return uuid + "." + ext;
    }

    private String extractExt(String originalFilename) {
        int pos = originalFilename.lastIndexOf(".");
        return originalFilename.substring(pos + 1);
    }
}
```

<br>

### ItemForm - 상품 저장용 폼
```java
@Data
public class ItemForm {
    private Long itemId;
    private String itemName;
    private List<MultipartFile> imageFiles;
    private MultipartFile attachFile;
}
```
- `Item` 도메인은 리스트에 `UploadFile`을 받았음을 기억하자.

<br>

### ItemController
```java
@Slf4j
@Controller
@RequiredArgsConstructor
public class ItemController {

    private final ItemRepository itemRepository;
    private final FileStore fileStore;

    @GetMapping("/items/new")
    public String newItem(@ModelAttribute ItemForm form) {
        return "item-form";
    }

    @PostMapping("/items/new")
    public String saveItem(@ModelAttribute ItemForm form, RedirectAttributes redirectAttributes) throws IOException {
        UploadFile attachFile = fileStore.storeFile(form.getAttachFile());
        List<UploadFile> imageFiles = fileStore.storeFiles(form.getImageFiles());

        // 정적 팩토리 메서드 or 생성자 or 빌더를 사용하는게 더 효율적이다.
        Item item = new Item();
        item.setItemName(form.getItemName());
        item.setAttachFile(attachFile);
        item.setImageFiles(imageFiles);
        
        itemRepository.save(item);

        redirectAttributes.addAttribute("itemId", item.getId());

        return "redirect:/items/{itemId}";
    }

    @GetMapping("/items/{itemId}")
    public String file(@PathVariable Long itemId, Model model) {
        Item item = itemRepository.findById(itemId);
        model.addAttribute("item", item);

        return "item-view";
    }

    // 화면에 이미지 출력
    @ResponseBody
    @GetMapping("/images/{filename}")
    public Resource downloadImage(@PathVariable String filename) throws MalformedURLException {
        return new UrlResource("file:" + fileStore.getFullPath(filename));
    }

    // 첨부 파일 다운로드
    @GetMapping("/attach/{itemId}")
    public ResponseEntity<Resource> downloadAttach(@PathVariable Long itemId) throws MalformedURLException {
        Item item = itemRepository.findById(itemId);
        String storeFileName = item.getAttachFile().getStoreFileName();
        String uploadFileName = item.getAttachFile().getUploadFileName();

        UrlResource resource = new UrlResource("file:" + fileStore.getFullPath(storeFileName));

        log.info("uploadFileName={}", uploadFileName);

        String encodedUploadFileName = UriUtils.encode(uploadFileName, StandardCharsets.UTF_8);
        String contentDisposition = "attachment; filename=\"" + encodedUploadFileName + "\"";

        return ResponseEntity.ok()
                .header(HttpHeaders.CONTENT_DISPOSITION, contentDisposition)
                .body(resource);
    }
}
```
- `UrlResource`로 이미지 파일을 읽어서 `@ResponseBody` 로 이미지 바이너리를 반환한다.
- 파일 다운로드시에는 고객이 업로드한 파일 이름으로 다운로드 하는게 좋다. 이때는 `Content-Disposition` 해더에 `attachment; filename="업로드 파일명"` 값을 주면 된다.

<br>

> **참고** <br>
> 다중 파일을 업로드 하기 위해서는 Form 태그내에 `multiple="multiple"`을 추가해주면 된다.