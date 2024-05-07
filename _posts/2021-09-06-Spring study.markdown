---
layout: post
title:  "토비의 스프링 3.1 - (1)"
post-image: "https://cdn.inflearn.com/public/courses/329974/cover/fe5d2e63-04c3-472e-9bd5-9b26857629a8/329974-eng.png"
tags: [Spring, Toby ]
---

## Vol.1 스프링의 이해와 원리
### 1장. 오브젝트와 의존관계   

#### 1.5) 스프링 IoC 용어
- 
    + Bean
        * 스프링이 IoC방식으로 관리하는 오브젝트.
        * 애플리케이션 중에서 스프링이 직접 생성과 제어를 담당하는 오브젝트
    + Bean Factory
        * 스프링의 IoC를 담당하는 핵심 컨테이너
        * 빈 등록, 생성, 조회, 관리
        * 빈팩토리가 구현하는 기본 인터페이스 이름 : BeanFactory(띄어쓰기붙임)
    + Application Context
        * 빈팩토리를 확장한 IoC 컨테이너
        * 빈 등록, 생성, 조회, 관리는 빈팩토리와 동일
        * 스프링이 제공하는 애플리케이션 지원기능 모두 포함한 관점
        * ApplicationContext는 BeanFactory를 상속
    + Configuration metadata
        * ApplicationContext, BeanFactory가 IoC 적용위해 사용하는 메타정보
        * 설정정보 세팅,조정 + 애플리케이션 오브젝트 생성,구성 (형상정보)
    + Container(IoC container)
        * 스프링 컨테이너 혹은 애플리케이션 컨텍스트를 지칭   
   
#### 1.6) 싱글톤 레지스트리 & 오브젝트 스코프
- 
    * 서버와 싱글톤 패턴
        + 스프링의 주 적용대상은 자바 엔터프라이즈를 사용하는 서버환경(싱글톤 서비스 권장)
        + 서블릿 클래스당 하나의 오브젝트만 만들어서 사용하도록 강제하는것이 싱글톤패턴 원리
        + 한계점 : private - 상속 불가, 테스트 지장(목 오브젝트 대체 불가), 서버환경, 전역상태 활성.   

    * → 싱글톤 레지스트리
        - 스프링이 직접 싱글톤 형태의 오브젝트를 만들고 관리하는 기능 제공 (저장소)
        - 스프링 컨테이너가 싱글톤 생성,관리,공급,관리 역할
        - 장점 : static, private 생성자 클래스 x -> public 자바클래스를 싱글톤으로 활용할수 있게 해줌
        - 애플리케이션 컨텍스트가 오브젝트 생성 관련 모든 권한을 가지고 있기 때문.
        - 객체지향 설계방식, 디자인 패턴에 아무 제약이 없음
        - 주의점 : 멀티스레드 환경에서 서비스형태 오브젝트로 사용시 stateless방식으로 생성되야 함 (공동 작업 주의)
   
    * 스프링 빈 스코프
        - 빈 생성, 존재, 적용되는 범위
        - 스코프: 컨테이너에 1개의 오브젝트로 만들어져 스프링 컨테이너 존재동안 유지(Default 싱글톤) 
        - 세션 스코프, 프로토타입 스코프, 요청 스코프,..

   
#### 1.7) 의존관계 주입 (DI)
- 
    * 객체를 생성하고 관계를 맺어주는 작업을 담당하는 기능을 일반화
    * 스프링 Ioc기능의 대표적인 동작원리 Dependency Injection.
    * 의존관계 검색 getBean() 메소드 (일반화된 이름을 전달해서 Object 호출)
    * 의존관계는 일방향의 방향성이 존재.
    * 의존관계 주입의 3가지 조건
        + 클래스나 코드에는 런타임시점의 의존관계가 드러나지 않음 (인터페이스에만 의존시 가능)
        + 런타임시점의 의존관계는 컨테이너나 팩토리 같은 제3의 존재가 결정 (오브젝트)
        + 사용할 오브젝트에 대한 레퍼런스를 외부에서 제공(주입)
    * 메소드를 이용한 DI
        + setter, setConnectionMaker(),, ..
   

#### 1.8) XML 설정
- 
    * 자바 클래스 외에 XML 등의 방법을 통해 DI 설정정보 메이킹.
    * XML에 담긴 DI 정보는 <beans> 루트 엘리먼트로 사용
        + @Configuration -> <beans> / @Bean -> <bean>
    * 예제] userDao() -> XML 변환
        + userDao.setConnectionMaker(connectionMaker());
        + -> <property name="connectionMaker" ref="connectionMaker"/>
    * DataSource 인터페이스
        + DataSource : DB커넥션을 가져오는 오브젝트 기능을 추상화해서 비슷한 용도로 사용
        + public interface DataSource extends CommonDataSource,Wrapper{
            Connection getConnection() throws SQLException;  }
        + 자바코드 설정방식(@Bean), XML 설정방식(<bean id="dataSource">)
    * 프로퍼티 값 주입 방식



