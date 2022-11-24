# DI/IOC
## 개념
- 상황 : 클래스 2개 ( class A, class SubA) 
  -  a = new SubA();
  - SubA 를 SubA-1로 바꿀 경우, class A 자체를 수정해야함
- 해결하기 위해 interface 사용
  - interface intA
  ```
  intA a;
  일케 두면, class A를 수정하지 않아도 됨.
  ```

   - [추가] class Main에서
   ```
   class Main{
    a1 = new SubA(1);
    A a = new A(a1);
   }

   public A(intA a){
    this.a = a;
   }
   ```
   ### DI
   밖에서 new로 만들어서 참조변수로 넣어준다!

   ### IOC (Inversion of controller)
   - 서블릿 할때, 서블릿 컨테이너 배웠음
    - 관리할 수 있는 공간
   ```
   톰캣 구동 이후, 외부에서 url 요청 들어오면, 현재 로드된 서블릿 있는지 확인.
   --> 가지고 있는 공간을 '서블릿 컨테이너' 라고 한다. [해시맵 형태 - url, 레퍼런스 주소 등]
   ```
   - IOC 컨테이너
    - 스프링 프레임워크 구동 시, 인스턴스를 저장하는 공간 생성.
    - xml에 지정을 다 해줌. [엄~~청 많은 xml이 존재함.]
      - 스프링 프레임웤 실행 이후, "이 xml로부터 객체를 얻고싶어!"

  - 외부에서 xml에서 호출 등 하는거 : IOC라고 함.
    - 스프링이 알아서 인스턴스 만들어서 넣어줌.


```
스프링 부트에서는 xml을 .
* DI, IOC
```

! 부트에서는 xml이 어떻게 구현되어 있는지 이해해야함
- @Component 
```
@Component
classA{

}
```
  - 붙여놓으면, 자동으로 서블릿 컨테이너 안에 들어감
  - xml 만들고, 수정하고 이런거 안해도 됨.


```
[브라우저] --(request)--> [컨트롤러] ----> [서비스] -----> [DAO] ----> [DB]

[컨트롤러] --(response)--> [뷰] ----> [브라우저]

* 우리는 지금 뷰 안통하고 바로 controller 에서 Brower 로 감.
* [서비스] 가 트랜잭션 단위

```

- @Controller 
  - Rest형태 제공 시 @RestController
  - 스프링 부트 초기 구동 시, Controller들이 미리 IOC에 올라가 있고, 디스패처 서블릿에서 상황맞춰 주입하는 식으로.

- @Service
  - 기본적으로 트랜잭션 단위로 묶임.

- @Repository
  - 우리가 'DAO'라고 알고 있는 것.
  - 이름은 다른데, 컴포넌트와 동일함.
**** 이것들 다~~~ @Component로 둬도 돌아는 간다.
```
유지보수를 위해서 Annotation 구별해주는 것이 편함. (클래스의 정체성 부여.)
```