---
layout: post
title: "RESTful API"
date: 2021-09-02 00:00:00
categories: [Study&more]
tags: [REST, RESTful API, client-server]
---

### REST 아키텍처의 제약조건을 준수하는 API
#### API (Application Programming Interface)
- 애플리케이션 소프트웨어를 구축하고 통합하는 프로토콜 정의
- 사용자의 컨텐츠 호출, 생산자의 컨텐츠 응답 시스템에서 전달 지원
- 클라이언트(사용자)와 얻고자 하는 리소스 사이의 조정자 역할
- 조직이 보안,제어 유지관리하면서 리소스와 정보 공유

   
#### REST   
- 프로토콜이나 표준이 아닌 아키텍처 원칙 세트
- 개발자는 REST를 다양한 방식으로 구현
- 리소스에 대한 정보를 요청자에게 전송(Http:JSON,HTML,..)
   
      
#### RESTful API 충족 기준
- 클라이언트-서버 아키텍처 + 리소스 구성(HTTP로 요청 관리)
- Stateless 클라이언트-서버 커뮤니케이션 : 요청 분리o, 정보 저장x
- 클라이언트-서버 상호작용을 간소화하는 캐시 가능 데이터
- 정보가 표준형식으로 전송되도록 하기 위한 통합 인터페이스
- 클라이언트가 정보 검색시 관련된 서버를 볼 수 없도록 한 계층구조 시스템
   
      
#### REST API vs SOAP
- SOAP(Simple Object Protocol) : 복잡한 요구사항, 속도 저하
- REST API : 필요에 따라 구현, 빠른 속도, 경량화, 모바일 및 IoT 적합 API
      
         
#### 적용 예시 (Toby's Spring vol.2)
1. 사용할 서비스의 URL 패턴 파악
2. 접근할 HTTP 메소드 방식 결정 (GET,POST,HEAD,PUST,..)
3. 결과 포맷, 컨텐츠 상호작용의 허용 여부 파악
   
```JAVA   
// URL 경로 예시, {}는 파라미터
 http://service.mycompany.com/users/{userid} 

// RestTemplate Object 생성
RestTemplate template = new RestTemplate();

// GET 방식 호출, 결과를 문자열로 받고 싶을 경우 예시
// 파라미터 : URL, type, 바인딩할 파라미터
String result = template.getForObject(
    "http://service.mycompany.com/users/{userid}", String.class, 1234);

// 결과값이 JSON, XML 일 경우 messageConverts를 이용해 오브젝트 변환
// MVC @ResponseBody, @RequestBody 사용 방법 동일

```   
   
      

