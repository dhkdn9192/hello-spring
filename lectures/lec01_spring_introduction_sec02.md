## 섹션2. 스프링 웹 개발 기초

* 웹 개발의 큰 3가지 방법
  * 정적 컨텐츠
  * MVC와 템플릿 엔진
  * API


### 2-1. 정적 컨텐츠
* [Spring Boot에선 정적 컨텐츠 기능을 제공한다.](https://docs.spring.io/spring-boot/docs/2.3.1.RELEASE/reference/html/spring-boot-features.html#boot-features-spring-mvc-static-content)
* `resources/static` 하위에 저장한 html 파일이 정적으로 제공된다.
* 정적 컨텐츠가 웹 브라우저를 통해 전달되는 과정
  1. 브라우저는 스프링 부트에 내장된 tomcat 서버로 localhost:8080/hello-static.html 을 요청한다.
  2. tomcat은 요청받으면 우선적으로 hello-static에 관련된 컨트롤러가 있는저 먼저 찾는다. (컨트롤러가 정적컨텐츠보다 우선순위)
  3. 컨트롤러를 찾지 못한 경우 resources/static 하위에서 hello-static.html 이라는 정적 컨텐츠를 찾아 웹 브라우저로 그대로 반환해준다.


### 2-2. MVC와 템플릿 엔진
* MVC
  * Model, View, Controller 의 약자
  * 시스템의 구조를 역할별로 나눠 할당하기 위한 디자인
* Controller
  * 비즈니스 로직을 담당하는 java 애플리케이션 코드
  ```java
  @Controller
  public class HelloController {

    @GetMapping("hello-mvc")
    public String helloMvc(@RequestParam("name") String name, Model model) {
      model.addAttribute("name", name);
      return "hello-template";
    }
  
  }
  ```
* View
  * 화면을 그리는 역할을 담당
  ```html
  <html xmlns:th="http://www.thymeleaf.org">
  <body>
  <p th:text="'hello ' + ${name}">hello! empty</p>
  </body>
  </html>
  ```
* 브라우저가 화면을 그리는 과정
  1. 브라우저는 스프링 부트에 내장된 tomcat 서버로 localhost:8080/hello-mvc 을 요청한다.
  2. tomcat은 요청받으면 우선적으로 hello-mvc에 관련된 컨트롤러가 있는저 먼저 찾는다. (컨트롤러가 정적컨텐츠보다 우선순위)
  3. 스프링에선 컨트롤러에서 매핑된 메서드를 찾아 호출해준다. ("hello-template" 값을 반환하면서 name이라는 키와 키로 들어온 값의 쌍을 같이 전달)
  4. viewResolver가 결과를 전달받고, 템플릿을 변환한 html을 웹 브라우저에 전달한다.


### 2-3. API
* @ResponseBody 어노테이션을 사용해 구현하며, 앞의 MVC 구조와 달리 viewResolver 를 사용하지 않는다.
* 문자를 반환하는 API 예시. 웹 브라우저로 볼 경우, HTTP의 BODY에 html 태그 없이 문자 내용을 직접 반환한다.
```java
    @GetMapping("hello-string")
    @ResponseBody
    public String helloString(@RequestParam("name") String name) {
        return "hello " + name;
        // @ResponseBody를 쓰면 return 값을 반환할 때  view를 쓰지 않음
        // 따라서 웹 브라우저로 확인해도 html 템플릿 없이 위 문자열만 그대로 전달됨
    }
```
* 객체를 반환하는 API 예시. 문자열과 달리 객체를 반환하면 디폴트로 JSON 형태로 데이터를 변환한다.
```java
    @GetMapping("hello-api")
    @ResponseBody
    public Hello helloApi(@RequestParam("name") String name) {
        Hello hello = new Hello();
        hello.setName(name);
        return hello;
        // 위에서 ResponseBody가 문자열을 반환하는 경우, 그대로 반환했지만
        // 객체를 반환할 땐 디폴트로 JSON 구조로 데이터를 만들어 반환하도록 설계됨
    }

    static class Hello {
        private String name;
        public String getName() {
            return name;
        }
        public void setName(String name) {
            this.name = name;
        }
    }
```
* @ResponseBody 사용 원리
  1. tomcat을 통해 컨트롤러가 요청을 받는것까진 동일
  2. 컨트롤러는 @ResponseBody 어노테이션으로 인해 viewResolver가 아니라 HttpMessageConverter 가 동작한다.
  3. 기본 문자 처리는 StringConverter (StringHttpMessageConverter), 기본 객체 처리는 JsonConverter (MappingJackson2HttpMessageConverter)
  4. 최종적으로 컨버터가 처리한 데이터(문자열 혹은 JSON 데이터)가 웹 브라우저로 반환된다.


