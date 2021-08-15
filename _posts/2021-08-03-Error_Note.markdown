---
layout: post
title: "Error_Note"
date: 2021-08-03 00:00:00
categories: update
---

# Error : 
    # javax.servlet.ServletException: 
      java.lang.NoClassDefFoundError: 
      javax/servlet/ServletInputStream   

      
# → Problem Solving !
 - 원인  : library에 servlet-api.jar 존재하지 않음.
 + 해결  : 톰캣에서 lib 폴더에 있는 servlet-api.jar를 프로젝트 library dependency에 추가

- - -      

# Error : 
    # Request processing failed; 
      nested exception is org.mybatis.spring.MyBatisSystemException:
      nested exception is org.apache.ibatis.binding.BindingException: 
      Parameter 'A' not found. Available parameters area[B]

# → Problem Solving !
    - 원인  : DAO에서 넘겨준 매개변수 객체 B에 멤버 변수 A가 존재하지 않음.
    + 해결  : 객체 B의 멤버 변수 A 추가
    + 해결2 : 있는데도 에러날 경우  
        * @Param("B") → Mapper : #{B.A}
        * @Param("B") x → Mapper : #{A}
 
- - -        
 
# Error : 
    # Scope 'session' is not active for the current thread; 
      consider defining a scoped proxy for this bean 
      if you intend to refer to it from a singleton; 
      nested exception is java.lang.IllegalStateException   

# → Problem Solving !
 - 원인  : DAO에서는 request scope가 불필요할 수 있음. 
 + 해결  : 불필요한 요소 삭제 또는 필요시 web.xmllistener 등록
            <listener>
                <listener-class>
                org.springframework.web.context.request.RequestContextListener
                </listener-class>
            </listener>   

- - -      

# Error :  
    # java.lang.IllegalArgumentException: Mapped Statements collection does not contain value   

# → Problem Solving !

    1. mapper id Check :  mapper 파일 - DAO, VO 비교

    2. Parameter와 bean의 필드명이 다른 경우

    3. mapper 파일 namespace 가 다를 경우

    4. mapper에 정의된 namespace 중복될 경우

    5. MyBatis config 파일에 정의가 되어 있지 않거나 Spelling이 틀린 경우

- - -      

# Error :
    # @Requestbody 415 에러

# → Problem Solving !

    1. $.ajax 블록{ dataType:'json', } 추가

    2. consumes = MediaType.APPLICATION_JSON_UTF8_VALUE

    3. Request Header 

    4. mapper에 정의된 namespace 중복될 경우

    5. MyBatis config 파일에 정의가 되어 있지 않거나 Spelling이 틀린 경우

- - -      

# Error :
    # 

# → Problem Solving !
 - 원인  : 
 + 해결  :

- - -      

# Error :
    # 

# → Problem Solving !
 - 원인  : 
 + 해결  :

- - -      

# Error :
    # 

# → Problem Solving !
 - 원인  : 
 + 해결  :

- - -      

# Error :
    # 

# → Problem Solving !
 - 원인  : 
 + 해결  :

- - -      

# Error :
    # 

# → Problem Solving !
 - 원인  : 
 + 해결  :

- - -