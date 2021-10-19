# WebClient
외부 API를 호출하기 위해 `WebClient` 객체를 사용한다.

<br>

## 설정
Spring Initializer에서 Spring Reactive Web을 추가해준다.

<br>

**`build.gradle`**
```
dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-webflux'
}
```

<br>

***
## 사용
### WebClient 빈 생성
```java
@Configuration
public class ApiConfig {
    @Bean
    public WebClient webClient(WebClient.Builder builder) {
        return builder.baseUrl("https://kr.api.riotgames.com").build();
    }
}
```
- `WebClient.Builder`를 통해 WebClient의 기본 속성을 편리하게 지정해줄 수 있다.
  - Spring Boot auto-configures a `WebClient.Builder` instance with nice defaults and customizations.
- `.baseUrl`: API를 호출할 기본 Url을 지정해준다.

<br>

#### 사용 예제1
```java
@Component
public class GreetingClient {

	private final WebClient client;

	public GreetingClient(WebClient.Builder builder) {
		this.client = builder.baseUrl("http://localhost:8080").build();
	}

	public Mono<String> getMessage() {
		return this.client
            .get()
            .uri("/hello")
            .accept(MediaType.APPLICATION_JSON)
            .retrieve()
            .bodyToMono(Greeting.class)
            .map(Greeting::getMessage);
	}
}
```
- `.bodyToMono`: API 호출 결과 데이터가 하나일 때 사용한다.
  - 매개 인자로 전달한 클래스에 Body로 전달된 데이터를 적절히 삽입해준다.
  - `getter`/`setter`와 클래스의 필드명을 기준으로 데이터를 할당한다.
- `.bodyToFlux`: API 호출 결과 데이터가 여러개일 때 사용한다.
- `.retrieve()`: API 데이터를 읽어들인다.

<br>

#### 사용 예제2
```java
private final WebClient webClient;

public SummonerAccountDTO getSummonerAccountInfo(String summonerName) {
    return webClient
            .get()
            .uri("/lol/summoner/v4/summoners/by-name/" + summonerName + "?api_key=" + APIConst.apiKey)
            .retrieve()
            .bodyToMono(SummonerAccountDTO.class)
            .block();
}
```
- `.block()`: `Mono<T>`가 아닌 `T` 타입의 데이터가 반환되도록 해준다.

<br>

***
## 앞으로 더 공부해야할 예제

```java
@Component
public class GreetingHandler {

	public Mono<ServerResponse> hello(ServerRequest request) {
		return ServerResponse
            .ok()
            .contentType(MediaType.APPLICATION_JSON)
			.body(BodyInserters.fromValue(new Greeting("Hello, Spring!")));
	}
}
```

<br>

```java
@Configuration(proxyBeanMethods = false)
public class GreetingRouter {

	@Bean
	public RouterFunction<ServerResponse> route(GreetingHandler greetingHandler) {

		return RouterFunctions
			.route(GET("/hello")
            .and(accept(MediaType.APPLICATION_JSON)), greetingHandler::hello);
	}
}
```

<br>

```java
@Service
public class MyService {

	private final WebClient webClient;

	public MyService(WebClient.Builder webClientBuilder) {
		this.webClient = webClientBuilder
                            .baseUrl("http://example.org")
                            .build();
	}

	public Mono<Details> someRestCall(String name) {
		return this.webClient
                    .get()
                    .url("/{name}/details", name)
                    .retrieve()
                    .bodyToMono(Details.class);
	}
}
```