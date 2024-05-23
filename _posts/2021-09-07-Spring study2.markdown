---
layout: post
title:  "토비의 스프링 3.1 - (2) "
post-image: "https://cdn.inflearn.com/public/courses/329974/cover/fe5d2e63-04c3-472e-9bd5-9b26857629a8/329974-eng.png"
tags: [Spring, Toby ]
---

## Vol.1 스프링의 이해와 원리
### 2장. 테스트   

#### 2.1) UserDaoTest 문제점

* 수동 확인 작업의 번거로움 - 테스트 자동화 과정마다 체크, 수동적 검증 필요
* 실행 작업의 번거로움 - main()메소드
* DAO, Service, Controller, view 등의 레이어 기능을 다 만든후에야 테스트 가능   
→ 단위 테스트의 필요성
- 대상, 조건을 명확하게, 작은 단위로 제한해서 기능 테스트  

- - -

#### 2.2) 개선 및 테스트 검증

* 테스트 검증 자동화 
- add() 등록, get() 검증, print() 결과값 확인 - xUnit
* 테스트 메소드 ( main -> 일반 메소드)
- @Test
- public void addAndGet() { }
- assertThat() : Param - mathcer 비교해서 일치하면 패스, 실패하면 테스트 실패처리
- ex. assertThat(user2.pwd(), is(user1.pwd()));
  
  
- - -
#### 2.3) JUnit - 개발자를 위한 테스팅 프레임워크

**JUnit 테스트 실행**
```JAVA
public static void main(String[] args){
    JUnitCore.main("spingboot.user.dao.UserDaoTest"); }
```
- assertThat() 검증 : 성공, 실패, 에러 갯수, 총 테스트 갯수, 수행시간
- 자바의 표준 테스팅 프레임워크 (이클립스 내장된 JUnit 테스트 지원 도구 사용)

**빌드 툴**
- ANT, Maven - JUnit 플러그인, 테스트 -> 실행결과는 HTML / txt.
* 테스트결과의 일관성
- 외부 상태에 따라 성공/실패 - 앞의 테스트 DB삭제 필요(중복)
- addAndGet()테스트 후 반드시 등록자 정보 삭제, 테스트 이전상태로 리셋해주기 권장       
-> deleteAll, getCount() - 테스트 순서 보장X 순서에 상관없이 동일한 결과 보장X(개선할 점)

**addAndGet()**
- .add() 후 레코드 갯수 확인, get() 값 비교 검증
- user1, user2 테스트 갯수를 하나 더 늘려서 연속 동작 기능 검증     

**get() 예외조건에 대한 테스트**
- 전달된 정보가 없는 경우의 결과값 출력
- 1.EmptyResultDataAccessException 예외 이용
- 2.초기화, if(is.next()){} 조건절로 예외발생 방지

**_TDD_ _(Test Driven Development)_**
- 테스트 주도 개발 : 만들고자 하는 기능 + 코드 검증이 가능한 테스트 코드 먼저 생성 
- '실패한 테스트를 성공시키기 위한 목적이 아닌 코드는 만들지 않는다' 원칙
- 장점 : 누락된 테스트 없이 꼼꼼하게 체크, 에러 파악 용이, 테스트작성 주기 감소
- 테스트 작성 후 성공시키는 코드를 만드는 작업 주기를 가능한 짧게 가지도록 권장


**리팩토링 + JUnit**
- @Before : JUnit 제공 어노테이션(@Test실행되기 전 먼저 실행해야하는 메소드 정의)
- public void setUp(){} :중복 코드를 넣을 메소드, 로컬변수는 인스턴스변수로 변경(private dao)
* JUnit 테스트 수행방식
- 1.@Test public void, 파라미터 없는 테스트 메소드 모두 찾기
- 2.@테스트 클래스의 NEW 오브젝트 1개 생성 (독립적 실행, 서로 영향X)
- 3.@Before 메소드가 있으면 먼저 실행
- 4.@Test 메소드 1개 호출, 테스트 결과 저장
- 5.@After 메소드가 있으면 실행
- 6.나머지 테스트 메소드에 대해 2~5번 반복
- 7.모든 테스트의 결과를 종합해서 리턴.

- - -   

#### 2.4) 스프링 테스트 적용   

```JAVA   
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations="/applicationContext.xml")
public class UserDaoTest {
	@Autowired
	private ApplicationContext context;
	private MemberDAO dao;
	
	@Before
	public void setUp() {
		System.out.println(this.context);
		System.out.println(this);
		this.dao = this.context.getBean("MemberDAO",MemberDAO.class);
	}
}   
```
     
```JAVA
@DirtiesContext
public class UserDaoTest {
    @Autowired
    UserDao dao;

    @Before
    public void setup() {
        DataSource dataSource = new SingleConnectionDataSource(
            "jdbc:mysql://localhost/testdb", "spring", "book", true);
        dao.setDataSource(dataSource); //수동 DI 
    }
}
```   
   

   
