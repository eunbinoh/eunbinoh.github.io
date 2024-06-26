---
layout: post
title: "JUnit_Test"
date: 2021-09-08 00:00:00
post-image: "https://img-c.udemycdn.com/course/750x422/4693842_0f19.jpg"
tags: [JUnit, Test, SpringTest, TDD]
---

# 최근 알게된 단위 테스트의 중요성! JUnit을 공부해봅시다!

### 토비의 스프링에서 배우는 JUnit (배경지식 참고)   
→  [Spring](https://eunbinoh.github.io/spring%20&%20java/2021/09/07/Spring-study2.html){: width="100%" height="100%"}   
   
### JUNIT5 SETTING
  - JUnit 5 = JUnit Platform + JUnit Jupiter + JUnit Vintage(engine) (모듈집합)
  - Trendy : junit4 -> junit5 (호환 위해 junit-engine 필요)   

  ![Image name](https://eunbinoh.github.io//images/junit3.png){: width="100%" height="100%"}           

  - MVN Repository 에서 1) spring-test, 2) junit5 api, 3) junit-engine get!
  - pom.xml에 set! (@test cannot resolved Error : spring-test scope 제거 ok, version check!)

### path
  ![Image name](https://eunbinoh.github.io//images/junit4.png){: width="20%" height="30%"}    
  - src/main/java/com.pakage.*/TestClass.java
  - src/test/java/com.pakage.*/ApplicationContext.xml (Bean등록장소)    
  - JUnit5 DI 자동 빌드(4->5)
   

- - -

### TEST PROCEDURE
    
#### 1. DB 연결 셋팅
  ![Image name](https://eunbinoh.github.io//images/junit7.png){: width="100%" height="100%"}        
    
  - @Test : 테스트 메소드 객체 생성
  - @RunWith(SpringJUnit4ClassRunner.class) : ApplicationContext를 만들고 싱글톤 관리
  - @ContextConfiguration(locations = "classpath:xml 파일 위치")
  - DataSource : DB 연결
  - ApplicationContext :스프링 빈(Bean) 설정 파일의 위치를 지정
  - getBean : 컨테이너에 담겨진 Bean 객체 호출 
  - Driver, url, id, pwd 입력
     
#### 2. DB insert 테스트  
   ![Image name](https://eunbinoh.github.io//images/junit5.png){: width="100%" height="100%"}     
   - conn = DriverManager.getConnection(URL,UID,PWD);
   - if(conn!=null){ }else{ } :커넥션 성공/실패 
   - try-catch-finally close()     

#### 3. DB select 테스트  
   ![Image name](https://eunbinoh.github.io//images/junit6.png){: width="100%" height="100%"}     
   - conn = DriverManager.getConnection(URL,UID,PWD);
   - String id = "user02"; String phone = selectName(conn,id);
   - System.out.println("id:"+id+", phone:"+phone);
   - selct테스트 위해 insert테스트에 @Disabled 추가 (사용하지 않음)   


### 테스트 어노테이션
  ![Image name](https://eunbinoh.github.io//images/junit2.png){: width="100%" height="100%"}        
  - @DisplayName(" ") : 테스트 메소드의 이름 지정,_ 띄어쓰기 공백
  - @Order() : 테스트 순서 지정
  - @BeforeEach : 각 테스트 메소드가 실행되기전 먼저 실행
  - @AfterEach : 각 테스트 메소드가 실행되고 난 후 실행
  - @BeforeAll : 테스트가 시작하기 전 딱 한 번만 실행
  - @AfterAll : 테스트가 완전히 끝난 후 마지막 한 번만 실행
  - @Tag : 테스트 필터링
  - @Disabled : 테스트 클래스, 테스트 메소드를 비활성화. JUnit4의 @Ignore와 같은 역할
  - @Nested : test 클래스안에 Nested(,inner) 테스트 클래스를 작성할 때 사용 (static X)
   
### Assertions
- org.junit.jupiter.api.Assertions 클래스 안에 있는 정적 메소드   
    
```java   

  class AssertionsTest {
    private final Calculator calculator = new Calculator();
    private final Person person = new Person("eunbi", "noh");

    @Test
    void standardAssertions() {
        assertEquals(2, calculator.add(1, 1));
        assertEquals(4, calculator.multiply(2, 2),
        assertTrue('a' < 'b', () -> "Assertion messages");
    }

    @Test
    void groupedAssertions() {
        assertAll("person",
                () -> assertEquals("Noh", person.getFirstName()),
                () -> assertEquals("eunbi", person.getLastName()));
    }

    @Test
    void exceptionTesting() {
        Exception exception = assertThrows(ArithmeticException.class, 
                                            () -> calculator.divide(1, 0));
        assertEquals("/ by zero", exception.getMessage());
    }

    @Test
    void timeoutNotExceeded() {
        assertTimeout(ofMinutes(2), () -> {
            // 2분 미만으로 실행되는 동작
        });
    }

    @Test
    void timeoutNotExceededWithResult() {
        String actualResult = assertTimeout(ofMinutes(2), () -> {
            return "a result"; //성공시 리턴값
        });
        assertEquals("a result", actualResult);
    }

    @Test
    void timeoutExceeded() {
        assertTimeout(ofMillis(10), () -> {
            // 10ms 이상 걸리는 작업
            Thread.sleep(100); // -- Fail
        });
    }
}   
```   
   
---
                      
