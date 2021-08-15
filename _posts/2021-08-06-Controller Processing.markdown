---
layout: post
title: "Spring_Controller"
date: 2021-08-06 00:00:00
categories: update

---


## Parameter 전송 받기     
# JAVA - controller   

JAVA servlet source:
{% highlight ruby %}
@RequestMapping(value="/login.me", method=RequestMethod.POST) 
//** servlet 주소 매핑, method=get방식이 기본값이지만 post방식도 생략 가능

//1. 매개변수 : HttpServletRequest ( JSP/Servlet )
public void login(HttpServletRequest request) {
	String id = request.getParameter("id");
	String pwd = request.getParameter("pwd");
}

2. 매개변수 : @RequestParam(value="name") → 변수 자동 파싱이 가능함(String국한x)
		→ param 여러개일때 @param(value="name") / param 한개일때는 @param("name")
																															 - value 생략.
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
			 → views/home.jsp
			 → url 그대로 ( login.me )

	return "redirect:home.do"; 
			// 데이터 유지하지않은채로(session은 유지) 홈으로(home.do) url변경.
}

5. @ModelAttribute 생략
public void login(Member m) {
	System.out.println("member:"+m);
}



## Parameter 출력 (내보내기) 

1. model객체 : 맵형식 (key,value),request scope
public String login(@ModelAttribute Member m, HttpSession session, **Model model**) {

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
public ModelAndView login(@ModelAttribute Member m, HttpSession session, **ModelAndView mv**) {

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

**@SessionAttribute** 사용 : model에 attribute가 추가될떄 자동으로 키를 찾아 세션에 등록
-> 맨위에 @SessionAttributes("loginUser") 추가 --> 로그인유저 추가되면 세션에 자동 등록 명령
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

{% endhighlight %}

```