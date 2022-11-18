# Spring FrameWork


##  스프링 프레임워크(22.11.18)
1. EJB 
: Enterprise(기업) Java Beans
* 분산환경에서 일할 수 있게. 
> 멀티태스킹, 멀티스레딩, 트랜젝션 등 서포트해줌.

2. POJO
: Plain Old Java Object
* EJB에서 POJO로 넘어감. 

3. Spring boot
: 스프링 프레임워크 서브 프로젝트로 만들어짐.
* 사용하기는 쉬운데, 설정이 어려움. (Xml 설정)
* 초기 스프링은 단순한 컨테이너였음!.
> 현재는 거대한 플랫폼이 되었음.

## 스프링 부트 기본 소개
> [루비 온 레일즈, 노드-익스프레스]!=> 웹개발을 위해 만들어진건 아님.
1. 발생 배경
* 프레임워크들이 많아지게 되면서, 설정이 복잡해줌
> 이를 해결하기 위해, "Spring Boot"라는 서브 프로젝트 발생.

2. 장점
* 라이브러리 관리 자동화
* 라이브러리 버전 관리 자동화
* 자동 설정
* 테스트 환경 제공
* 내장 서버 -> 설정 간소화
> 톰캣을 가지고 있음(War)
> JAR 형태로 그대로 실행
* 독립적 실행 가능한 JAR

### 실습 환경 설정
* https://spring.io/tools 여기서 다운로드


* 수업관련 메모
```
여기 항목은, 그냥 수업 편의를 위해 하는 것 뿐임.
* C:\STS4WorkSpace
> 얘를 워크스페이스로 설정하기
* Develop Kit
> Spring tool -> 이 폴더에 압축풀기
```

* Maven vs Gradle
> 도와주는 관리 툴. * 책에서 Maven 사용하니 일단 따라가기.

* DI
> 일단은 Web만 

* 스프링부트 프로젝트 항목 별 설명
```
* resources -> application.properties
: 설정파일.
-. 외부에서 설정해줘야 할 내용들(자동으로 설정 힘든 항목들) 설정하는 곳.
-. 부트 실행 시 필요한 기본적인 환경정보

* static, templates
: HTML로 저장됨.
-. 변하는 데이터/ 변하지 않는 데이터

* JRE System Library
: Java 라이브러리 전부 들어가있음.

* Maven Dependencies
: 라이브러리 도와주는 툴. 
-. Maven이 없으면, jar 다 각각 다운받아서 해줘야함 -> 이거 자동화 해줌.
-. Gradle도 논리는 같은데, 인덴테이션 등 표현 형태가 좀 다름.

* pom.xml
-. dependencies 수정해줄거임. 필요시 -> Maven이 수정!
-. 수정 할 경우, 수정된 대로 재구성 됨.

* src 폴더 아래에, 뭐가 많이 없다.
-> 부트는 기본적으로 JSP를 지원하지 않음
-> "가급적이면 JSP 쓰지 마라!"
*** Library : Jar 파일
```

* 프론트엔드에 데이터 던져주는 용도로만 써라.
```
이게 기본적인 부트의 컨셉.
- 그래서 기본적으로 Json으로 데이터를 던져줌.
- 그러면 프론트엔드에서 받아서 처리하도록 됨
```

3. 실행
* [프로젝트 우클릭] → [Run as] → [Spring Boot App]
* 종료
- 방법1. Stop
- 방법2. Boot Dashboar에 ■▷ 눌러줌. (종료 및 재실행까지 해줌.)

### 자바 애플리케이션 실행하기
  1. Chapter01Application.java
    - 조금 변경해줬음.
    ```
    package com.rubypaper;

    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.WebApplicationType;
    import org.springframework.boot.autoconfigure.SpringBootApplication;


    @SpringBootApplication
    public class Chapter01Application {

      public static void main(String[] args) {
        SpringApplication application=
            new SpringApplication(Chapter01Application.class);
        application.setWebApplicationType(WebApplicationType.NONE);
        application.run(args);
        
        System.out.println("Done");
      }
    }
    ```
    - 타입 서블릿으로 변경
    ```
    package com.rubypaper;

    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.WebApplicationType;
    import org.springframework.boot.autoconfigure.SpringBootApplication;


    @SpringBootApplication
    public class Chapter01Application {

      public static void main(String[] args) {
        SpringApplication application=
            new SpringApplication(Chapter01Application.class);
        application.setWebApplicationType(WebApplicationType.SERVLET);
        application.run(args);
        
        System.out.println("Done");
      }
    }
    ```

    * 스레드와 프로세스의 차이
      - 프로세스
      : 프로그램 실행 시,
      ex) 구구단 계산 프로그램
      ```
      ㅁ. 버튼 클릭 → 2단~9단 : 한 단에 1,000만번 계산.
        ㅇ. 순차적으로 실행 할 것임. --> 1프로세스 1스레드 방식.
          -. 스레드 : 작업 단위.
        ㅇ. 각각의 단위에 대해 다 스레드를 각각 만들면?
          -. 싱글 스레드 : 한줄로 ~~~
          -. 멀티 스레드 : 한개의 Tasking 을 여러개의 작업단위로 쪼개서 한번에 처리
            > cpu 9개가 한번에 돌아감.
            > 요즘은 다 멀티 스레드로 돌아감.
          * 멀티 스레드 -> 훨씬 빠름.
          * 멀티 스레드 -> CPU 잡아먹는것도 훨씬 큼.
          ** 코어/스레드 --> 나중에 찾아보기.
      ㅁ. 자바는, 부모 스레드가 죽어도 자식 스레드가 일을 계속 함.
        ㅇ. 자바 -> 메인 스레드 끝나도 자식 스레드 계속 살아있음.
        ㅇ. 일반적으로는, 스레드 다 죽는게 일반적.
      ㅁ. None vs Servlet
        ㅇ. None : 일반 Application 모드로 사용.
        ㅇ. Servlet : 서버.
      ```

  2. application.properties
  ### 배너 모드 끄기
  ```
  spring.main.web-application-type=servlet
  spring.main.banner-mode=off
  ```
  - 이러면 배너 창 안뜸

  ### 서버 포트 설정
  ```
  spring.main.web-application-type=servlet
  spring.main.banner-mode=off
  server.port = 8080
  ```
  - 톰캣을 내장하고 있기 때문에, 8080포트 사용.
  - 가급적 안바꿔주는게 맞음.

  ### Hello 출력
```
package com.rubypaper.controller;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class BoardController {
	public BoardController() {
		System.out.println("===> BoardController 생성");
	}
	
	@GetMapping("/hello")
	public String hello(String name) {
		return "Hello :" + name;
	}
}
 ``` 

  ### CRUD
  - C : Post
  - R : Get
  - U : Put
  - D : Delete
  * Default : Get
```
  package com.rubypaper.controller;

  import org.springframework.web.bind.annotation.DeleteMapping;
  import org.springframework.web.bind.annotation.GetMapping;
  import org.springframework.web.bind.annotation.PostMapping;
  import org.springframework.web.bind.annotation.PutMapping;
  import org.springframework.web.bind.annotation.RestController;

  @RestController
  public class BoardController {
    public BoardController() {
      System.out.println("===> BoardController 생성");
    }
    
    @PostMapping("/hello")
    public String hello1(String name) {
      return "Post Hello :" + name;
    }

    @GetMapping("/hello")
    public String hello2(String name) {
      return "Get Hello :" + name;
    }

    @PutMapping("/hello")
    public String hello3(String name) {
      return "Put Hello :" + name;
    }
    
    @DeleteMapping("/hello")
    public String hello4(String name) {
      return "Delete Hello :" + name;
    }
  }
```
  
* @RestController
  - Rest방식의 응답을 처리하는 
  - Http 등등 없다.

* GetMapping
  - 주소와 관련된 어노테이션

* 특징
  - 서블릿에 비해 편함.
  - req.getParameter 등등 없음. -> 자동으로 해줌.
  - 데이터를 던져줬는데 그냥 브라우저에 바로 뜸.
    - 서블릿에서는, 디스패처 해주고 req,resp 해주고 포워딩 해줬음.
    - 이건 그런거 없이 걍 뜸.
    - 나중에 View쪽 가서 공부하면, 실제로 JSP이름을 던지고 넘겨주고 디스플레이도 됨(스포)
  - 이거들 다 RestController이 해주는 거임.
    