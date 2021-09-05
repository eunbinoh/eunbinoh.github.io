---
layout: post
title:  "Spring_@Annotaion"
date:   2021-08-13 00:00:00
categories: [Spring & JAVA]
tags: [Annotation, RequestParam, RequestBody, ModelAttribute]
---
   

### My Notion Link   
→  [Spring](https://www.notion.so/Spring-41db81b95ae24674ae6bc048dfee5458)   

# @RequestParam
    - 1개의 HTTP 파라미터를 얻기 위해 사용됨
    - required=false 설정 필요 (default true)   


# @ResponseBody   
    - HTTP요청 Body의 내용을 자바 객체로 전달
    - Param type이 확인되면 통째로 변환해서 지정 메소드 Param으로 전송


# @RequestBody
    - Json으로 받은 HTTP Body 데이터를 MessageConverter를 통해 변환
    - 변환시켜주기 때문에(Reflection), Setter 없이 변수 그대로 저장     


# @ModelAttribute
    - multipart/form-data 형태의 Body, Param을 Setter를 통해 1:1로 객체에 바인딩
    - 바인딩은 Setter 필요, setter없으면 변수가 저장되지 않음            
      

# @RestController   
    - 리턴값에 자동으로 @ResponseBody 붙여줌, HttpResponse로 바로 응답 가능
    - 메소드 결과를 JSON 형태로 리턴
    - view가 필요없는 API만 지원하는 서비스에서 사용
    - @Controller + @ResponseBody
   

# @Component
    - 개발자가 직접 작성한 Class를 Bean으로 등록하기 위한 어노테이션
    - name이 아닌 value로 Bean의 이름을 지정함


# @Bean
    - 개발자가 직접 제어 불가능한 외부 라이브러리를 Bean으로 만들기 위한 어노테이션
    - name으로 Bean 이름을 지정함
      

# @SessionAttributes
    - Model에 key값이 "name"으로 있는 값을 자동 세션에 저장
    - ex. @SessionAttributes("loginUser")
      

# @Autowired // @Inject
    - 스프링에서 무조건적인 객체에 대한 의존성을 자동 주입
    - 주로 Controller 에서 DAO, Service 객체 주입시 많이 사용
      

# @RequestMapping
    - 요청 URL을 어떤 method가 처리할지 mapping 해주는 역할
    - default : GET  
   

# @PathVariable
    - Http요청에 대해 매핑되는 Param값이 자동으로 Binding
    - Uri 에서 각 구분자에 들어오는 값을 처리해야 할 때/ REST API 값 호출시 사용
    - @GetMapping(value="/path/{id}") {}을 URL에 변수로 받을수 있음


     
   
      
---   
- - -
- - -
   

## Param 전송
```java   

@RequestMapping(value="/login.me", method=RequestMethod.POST) 
//** servlet 주소 매핑, method=get방식이 기본값이지만 post방식도 생략 가능

//1. 매개변수 : HttpServletRequest ( JSP/Servlet )
public void login(HttpServletRequest request) {
	String id = request.getParameter("id");
	String pwd = request.getParameter("pwd");
}

2. 매개변수 : @RequestParam(value="name") -> 변수 자동 파싱이 가능함(String국한x)
		-> param 여러개일때 @param(value="name") / param 한개일때는 @param("name")
																								
public void login(@RequestParam(value="id") String idee,
		@RequestParam(value="pwd" ,defaultValue="what", required=false) String pwddd) {
				//+ defaultValue : 값이 null이거나 공란일때 들어갈 데이터 기본값 지정 
	      (값이 있으면 나오지않음)
				//+ required=false: 빈칸 가능(default=true :생략시 빈칸이면 에러)
}

3. @RequestParam 생략 : 파라미터명 = 매개변수명 일때 자동매핑되어 생략가능
public void login2(String id, String pwd) {

}

4. @ModelAttribute 어노테이션 : 요청 파라미터가 많을경우 객체 타입으로 넘겨 받기
		기본생성자, setter 주입방식이기때문에, 둘 중 하나라도 없으면 에러
public String login(@ModelAttribute Member m, HttpSession session) {

	Member loginUser =	service.login(m);

	if(loginUser !=null) {
		session.setAttribute("loginUser", loginUser);
	}

		return "../home";
			 views/member/home 으로 member-context에서 경로 설정 되어있기 때문에,
			 그 상위 경로 이전으로 타야할때 : " ../ "
			 -> views/home.jsp
			 -> url 그대로 ( login.me )

	return "redirect:home.do"; 
			// 데이터 유지하지않은채로(session은 유지) 홈으로(home.do) url변경.
}

5. @ModelAttribute 생략
public void login(Member m) {
	System.out.println("member:"+m);
}     

```   
   
## 요청 후 데이터 전달
```java   

1. model객체 : 맵형식 (key,value),request scope
public String login(@ModelAttribute Member m, HttpSession session, Model model) {

	Member loginUser =	service.login(m);
	System.out.println("member:"+loginUser);

	if(loginUser !=null) {
		session.setAttribute("loginUser", loginUser);
		return "redirect:home.do";

	}else {
		model.addAttribute("message","로그인 실패");
		return "../common/errorPage";
	}

 2. ModelAndView 객체 : Model(맵형식) + View(뷰페이지 경로)
@RequestMapping(value="/login.me", method=RequestMethod.POST)
public ModelAndView login(@ModelAttribute Member m, HttpSession session, ModelAndView mv) {

	Member loginUser =	service.login(m);
	System.out.println("member:"+loginUser);

	if(loginUser !=null) {
		session.setAttribute("loginUser", loginUser);
		 mv.setViewName("redirect:home.do");

	}else {
		//Model
		mv.addObject("message","로그인 실패");

		//View
		mv.setViewName("../common/errorPage");
	}

	return mv;

}

@SessionAttribute 사용 : model에 attribute가 추가될때 자동으로 키를 찾아 세션에 등록
@SessionAttributes("loginUser") 추가 --> 로그인유저 추가되면 세션에 자동 등록 명령
@RequestMapping(value="/login.me", method=RequestMethod.POST)
public String login(Member m, Model model) {

			Member loginUser =	service.login(m);

			if(loginUser !=null) {
				model.addAttribute("loginUser", loginUser);
				return "redirect:home.do";

			}else {
				model.addAttribute("message","로그인 실패");
				return "../common/errorPage";
			}
}   

```   
   


