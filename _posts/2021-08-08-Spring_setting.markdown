---
layout: post
title: "Spring_setting"
date: 2021-08-08 00:00:00
post-image: "https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcSCOGJsxz9iINmjXWOsA6qRfA7Hc8GASDYYM181fsSevg&s"
tags: [스프링 세팅, Spring Legacy Project, pom.xml ]
---

1. spring 설치 - STS / eclipse-marketplace   

2. maven, apache tomcat, jQuery, ojdbc6, taglibs(compat,impl,jstlel,spec) : setting check   

3. 인코딩 - preferences encoding UTF-8, spelling UTF-8;   

     *tip) java 자동완성 툴 : preference - editor - content assist - autoActivation java    
      →   .qwertyuioplkjhgfdsazxcvbnm_QWERTYUIOPLKJHGFDSAZXCVBNM   

4. Spring Legacy Project 생성   

5. pom.xml :  DB연결 Lib, Security Lib, 파일업로드 Lib,..→ mvnrepository.com   

     → [pom.xml](https://www.notion.so/pom-xml-a191b4a728d34f5fa7e5a24cd2df79c9)   

6. web.xml : Servlet Mapping,  인코딩필터,  공용에러페이지 (exception→error) 등록   

    context-param(root-context, servlet-context, security.xml)   

     → [web.xml](https://www.notion.so/web-xml-59c9333ac9eb4cacba6ee4a0affd63a8)   

     → [servlet-context.xml](https://www.notion.so/servlet-context-xml-508046453883402b9416f616c74f23e1)    

     → [member-context.xml](https://www.notion.so/member-context-xml-4f95d1193dbc4b61b1641f3e119823c0)    

     → [security.xml](https://www.notion.so/security-xml-0ecd6514dc74436180900ea001f33c75)       



7. root-context : bean객체 생성   

    (driver, sqlSession, Template, transaction 등록), 파일업로드 bean, size 규정   

     → [root-context .xml](https://www.notion.so/root-context-xml-e2ab691eeb0a43afb45050db1c12f654)   

8. mybatisconfig :  **window- preferences - xml Catalog Entries -> DTD 등록**   

     → [mybatis-config.xml](https://www.notion.so/mybatis-config-xml-0b9cc33cc77943ba89fc18b8a9933e86)   

9. mappers - 매퍼 등록 (쿼리 연결)  **window- preferences - xml Catalog Entries - DTD 등록**      
   

     → [member-mapper .xml](https://www.notion.so/member-mapper-xml-e072c396108844159cb84dd071e40289)    

     → [board-mapper .xml](https://www.notion.so/board-mapper-xml-48967d2e4dcb46e3a26b221e2cf6f977)   


