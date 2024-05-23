---
layout: post
title:  "토비의 스프링 3.1 - (3) "
post-image: "https://cdn.inflearn.com/public/courses/329974/cover/fe5d2e63-04c3-472e-9bd5-9b26857629a8/329974-eng.png"
tags: [Spring, Toby ]
---

## Vol.1 스프링의 이해와 원리
### 3장. 템플릿 

   
#### 3.2) 변하는 것과 변하지 않는 것

**JDBC try/catch/finally 코드의 문제점**
- 메소드 반복 중첩, 코드 수정의 불편함과 close 요소 누락 가능성
- 커넥션풀 과부하

**분리와 재사용을 위한 디자인패턴**
- 수정이 필요없는 고정적인 부분 / 수정이 필요한 부분을 분리
- 메소드 추출 : 수정이 빈번한 부분을 메소드 독립, but 재사용 어려움.
- 템플릿 메소드 패턴 적용 : 추상클래스/서브클래스, but 클래스 상속의 단점 발생.
- 전략 패턴의 적용

**DB커넥션 가져오기**
+ PreparedStatement 만들어줄 외부기능 호출
+ 전달 받은 PreparedStatement 실행
+ 예외 발생시 다시 메소드 밖으로 던지기
+ 모든 경우에 만들어진 PreparedStatemen, Connection 닫아주기
+ 인터페이스, 특정 클래스가 고정되어 컨텍스트에 노출되므로 전략패턴에 잘 맞지 않음   

**DI 적용을 위한 클라이언트/컨텍스트 분리**
- 컨텍스트에 해당하는 JDBC try/catch/finally 코드를 
- 클라이언트 코드인 StatementStrategy를 만드는 부분에서 독립시키기      

```java
// 메소드로 분리한 컨텍스트 코드
public void jdbcContextWithStatementStrategy(StatementStrategy stmt) throws SQLException {
    Connection c = null;                
    PreparedStatement ps = null;
    try{
        c = dataSource.getConnection();
        ps = stmt.makePreparedStatement(c);
        ps.executeUpdate();

    }catch (SQLException e){
        throw e;
    } finally{
        if(ps !=null){
            try {
                ps.close();
            }catch(SQLException e) {}
        }
        if(c !=null){
            try {
                c.close();
            }catch(SQLException e) {} 
        }
    }
}   
// 클라이언트 담당 메소드
public void deleteAll() throws SQLException {
    StatementStrategy st = new DeleteAllStatement(); // 전략 클래스의 Obj 생성
    jdbcContextWithStatementStrategy(st); // 컨텍스트 호출. StatementStrategy 전달
}
```    
  
- - -

#### 3.3) JDBC 전략 패턴의 최적화

* 전략클래스의 추가정보 (AddStatement 생성자)
- user insert와 같은 추가정보 컴파일 -> add 생성자 전달(this.user)
* 중첩 클래스
- 클래스 파일이 많아지는 문제 해결 -> 클래스 안에 내부 클래스로 정의
- 중첩 클래스(nested) : static class(독립적 obj 생성) , inner class(정의된 클래스 obj 에서만 생성)
- 내부 클래스(inner) : scope에 따라 분류 - local /member inner/ annonymous inner class
   
- - -

#### 3.4) Context & DI

* 클래스 분리
* 빈 의존관계 변경
* JDBCContext의 특별한 DI

- - -

#### 3.5) 템플릿과 콜백


   
