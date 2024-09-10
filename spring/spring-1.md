# Spring 어노테이션

## 1. _Spring & Spring Boot_

| Annotation                                                                                       | Detail                                                                                                                                                                                                                                                                                 |
| ------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| @SpringBootApplication                                                                           | <ul><li>프로젝트의 최상위 Application 클래스에 선언</li><li>@SpringBootApplication이 있는 위치부터 설정을 읽어가므로 프로젝트의 최상단에 위치</li><li>스프링부트의 자동설정, 스프링Bean읽기와 생성을 모두 자동으로 설정</li></ul>                                                                                                                         |
| @RestController                                                                                  | <ul><li> 컨트롤러 클래스에 선언 ( JSON을 반환 ) </li></ul>                                                                                                                                                                                                                                          |
| <p>@GetMapping</p><p>@PostMapping</p><p>@PutMapping</p><p>@DeleteMapping</p><p>@PatchMapping</p> | <ul><li> HTTP Method에 매칭되는 메소드에 선언</li><li>@RequestMappng(method = RequestMethod.GET)</li></ul>                                                                                                                                                                                        |
| <p>@RequestBody<br>@ResponseBody</p>                                                             | <ul><li>@RequestBody는 메소드의 인자 앞에 선언<br>- HTTP 요청의 body 내용을 자바 객체로 매핑</li><li>@ResponseBody는 메소드에 선언<br>- 자바 객체를 HTTP 요청의 body 내용으로 매핑<br></li></ul>                                                                                                                                    |
| @WebMvcTest                                                                                      | <ul><li>테스트 클래스에 선언 (Web(Spring MVC) )</li><li>모든 자동설정을 비활성화시키고, MVC테스트에 관련된 설정만 적용</li><li>이를 명시하고, MockMvc를 @Autowired하면 해당 객체를 통해 MVC테스트<br>- @Controller / @ControllerAdvice / @JsonComponent ...<br>- 함께 사용불가능: @Service / @Component / @Repository / @SpringBootTest 등</li></ul> |
| @Autowired                                                                                       | <ul><li>스프링 컨테이너가 관리하는 Bean을 주입</li></ul>                                                                                                                                                                                                                                              |
| @RequestParam                                                                                    | <ul><li>파라미터 부분에 선언  ( 쿼리스트링을 가져오는 어노테이션 )</li></ul>                                                                                                                                                                                                                                   |
| @PathVariable                                                                                    | <ul><li>파라미터 부분에 선언 ( Mapping Annotation에서 지정한 변수를 가져오는 어노테이션 )</li></ul>                                                                                                                                                                                                              |
| @Transactional                                                                                   | <ul><li>트랜잭션 기능이 적용된 프록시 객체가 생성</li><li> @Transactional이 포함된 메소드가 호출 될 경우, PlatformTransactionManager를 사용하여 트랜잭션을 시작하고, 정상 여부에 따라 Commit 또는 Rollback<br>- MySQL일 경우 Inno일때만 트랜잭션이 작동 </li><li>proxy를 사용할 때 반드시 public 메소드에 사용</li></ul>                                                |
| Model                                                                                            | <p></p><ul><li>서버 템플릿 엔진에서 사용할 수 있는 객체 저장 </li></ul>                                                                                                                                                                                                                                   |
| @Bean / @Component                                                                               | <ul><li> IOC Container에 등록할 자바 객체들에 선언</li><li>@Bean: 개발자가 컨트롤할 수 없는 외부 클래스를 Bean으로 등록할 때 </li><li>@Component: 개발자가 컨트롤할 수 있는 클래스(직접 작성한 클래스)를 등록할 때 </li></ul><p> </p>                                                                                                                |

## 2. Spring Boot Test

| Annotation                                          | Detail                                                                                                                                                                                                                                                                                                                                                                                                                    |
| --------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| @WebMvcTest                                         | <ul><li>테스트 클래스에 선언 (Web(Spring MVC) )</li><li>모든 자동설정을 비활성화시키고, MVC테스트에 관련된 설정만 적용</li><li>이를 명시하고, MockMvc를 @Autowired하면 해당 객체를 통해 MVC테스트<br>- @Controller / @ControllerAdvice / @JsonComponent ...<br>- 함께 사용불가능: @Service / @Component / @Repository / @SpringBootTest 등</li></ul>                                                                                                                                    |
| <p>@MockBean</p><p>@MockBean("덮어쓸 기존 Bean의 이름")</p> | <ul><li>테스트 코드에서, 필요한 인스턴스에 선언</li><li>MockBean을 선언한 인스턴스의 메소드를 사용하고, thenReturn(개발자 지정 리턴값) 메소드를 체이닝함으로써 반환값을 조작할 수 있다.</li></ul>                                                                                                                                                                                                                                                                                        |
| MockMvc                                             | <ul><li>Web Application을 서버에 배포하지 않고도 Web API를 테스트</li></ul>                                                                                                                                                                                                                                                                                                                                                              |
| perform( )                                          | <ul><li>MockMvc 객체의 perfomr( ) 메소드를 이용하여 HTTP Request에 대한 요청 진행</li></ul>                                                                                                                                                                                                                                                                                                                                                 |
| param( )                                            | <ul><li>MockMvc 객체의 perform 메소드 안에서, http method 메소드에 체이닝되어 사용</li></ul><p>mvc.perform(get("/hello/dto")<br>    .param("name", name) <br>    .param("amount", <br>            String.valueOf(amount)))            </p><p>    .andExpect(status().isOk())       </p><p>    .andExpect(jsonPath("$.name", is(name)))       </p><p>     .andExpect(jsonPath("$.amount", </p><p>                            is(amount)));</p> |
| jsonPath( )                                         | <ul><li>JSON 응답값을 필드별로 검증할 수 있는 메소드</li><li>$를 기준으로 필드명 명시</li></ul>                                                                                                                                                                                                                                                                                                                                                      |

## 3. Java

| Annotation | Detail                                                                                                                                                                                                                                                                                                               |
| ---------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| @interface | <ul><li>interface 클래스 생성<br></li></ul><p>@Target(ElementType.PARAMETER)</p><p>@Retention(RetentionPolicy.RUNTIME)</p><p>public @interface LoginUser{</p><p>}</p>                                                                                                                                                     |
| @Target    | <ul><li>어노테이션이 위치할 수 있는 곳을 결정- -</li><li>ElementType.PARAMETER의 경우, 메소드의 파라미터로 선언된 객체에서만 사용가능</li></ul>                                                                                                                                                                                                              |
| @Retention | <ul><li>어노테이션이 어떤 시점까지 유지될 수 있는지 결정</li><li>값 미지정 시 <br>default = RetentionPolicy.CLASS</li><li><p> 종류  </p><ul><li>CLASS: 컴파일러에 의해 클래스 파일에선 유지되지만, 런타임 시 클래스를 메모리로 읽으면 사라진다</li><li>RUNTIME: 런타임까지 유지되므로, reflectively하게 읽힐 수 있다 -> 코드에서 이 정보를 바탕으로 로직을 구현할 수 있다</li><li>SOURCE: 컴파일러에 의해 버려진다 </li></ul></li></ul> |
| @Override  | <ul><li>오버라이딩하는 메소드에 선언</li><li>어노테이션이 없는 오버라이딩과의 유일한 차이: 안전장치 (실수방지)</li></ul>                                                                                                                                                                                                                                      |
| @Aspect    | <ul><li>Aspect로 사용할 클래스에 선언</li><li>AspectJ에 의해 Aspect로 사용될 것임을 의미</li></ul>                                                                                                                                                                                                                                         |
|            |                                                                                                                                                                                                                                                                                                                      |

### &#x20;