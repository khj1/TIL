# Converter.md
## 기본 Converter
### Controller
```java
@Controller
public class ConverterController {
    @GetMapping("/converter-view")
    public String converterView(Model model) {
        model.addAttribute("number", 10000);
        model.addAttribute("ipPort", new IpPort("127.0.0.1", 9090));
        return "converter-view";
    }
}
```

<br>

### View
```html
<li>${number}: <span th:text="${number}" ></span></li>
<li>${{number}}: <span th:text="${{number}}" ></span></li>
<li>${ipPort}: <span th:text="${ipPort}" ></span></li>
<li>${{ipPort}}: <span th:text="${{ipPort}}" ></span></li>
```
- `{}` : 단순한 변수 표현식 - 컨버터 적용 X
  - 객체 출력시 객체의 참조값이 출력된다.
- `{{}}` : 컨버전 서비스 사용 적용

<br>

### 결과
    ${number}: 10000
    ${{number}}: 10000
    ${ipPort}: hello.typeconverter.type.IpPort@59cbf20c
    ${{ipPort}}: 127.0.0.1:9090

***
## Form Converter
***th:field는 Conversion Service 기능을 내장하고 있다.***
### 컨트롤러
```java
@GetMapping("/converter-edit")
public String converterForm(Model model) {
    IpPort ipPort = new IpPort("127.0.0.1", 9090);
    Form form = new Form(ipPort);
    model.addAttribute("form", form);
    return "converter-form";
}
```
<br>

### View
```html
<form th:object="${form}" th:method="post">
    th:field <input type="text" th:field="*{ipPort}"><br/>
    th:value <input type="text" th:value="*{ipPort}">(보여주기 용도)<br/>
    <input type="submit"/>
</form>
```

<br>

### 결과
    th:field = 127.0.0.1:9090
    th:value = hello.typeconverter.type.IpPort@59cbf20c

- `th:field` 값은 Conversion Service가 자동으로 적용되도록 구현되어있다.
