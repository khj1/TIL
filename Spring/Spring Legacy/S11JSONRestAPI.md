# Spring - JSONRestAPI
jackson 라이브러리와 ```@ResponseBody``` 어노테이션을 통해 ```Map``` 컬렉션은 ```JSON``` 객체로, ```List``` 컬렉션은 ```JSON``` 배열로 출력할 수 있다.

## 설정하기
> ### pom.xml( Spring Legacy )
```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.5.1</version>
</dependency>
```

***
## 사용하기
```ajax```와 함께 사용하면 더 좋은 시너지를 기대할 수 있다.
> ### Controller
```java
@GetMapping("/jsonUse/jsonView.do")
@ResponseBody
public Map<String, Object> resposeBodyView(){

    Map<String, Object> map = new HashMap<String, Object>();
    map.put("Number", 1004);
    map.put("Message", "JSON은 Jackson 의존설정이 필요하다.");
    
    ArrayList<String> list = new ArrayList<String>();
    list.add("편하다.");
    
    map.put("Collection", list);
    
    // 출력 결과는 JSON 객체 형태이다.
    // list를 출력하면 JSON 배열 형태로 출력된다.
    return map;
}
```