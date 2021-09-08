---
layout: post
title:  "SockJS 메신저 그룹웨어"
categories: [Project]
tags: [SocketJS, websocket, Spring, messenger, chatting]
---

# Project - SockJS 선정 이유 (웹소켓 배경)
- 웹소켓은 양방향 소통을 위한 프로토콜로, 적은 데이터를 빠르게 통신. ex. 코인, 주식 홈페이지
	+ 하지만 호환,표준규격 등의 접근성 단점, 이에 대한 솔루션 : Socket.io & SockJS
  	- Socket.io : node.js기반 자체서버를 만들어 실시간 통신이 브라우저 제약x. javascript 개발.
  	-  SockJS : Spring framework에서 자체스펙으로 웹소켓 미지원 브라우저를 관리,   
    -> 서버 개발시 websocket,sockJS 호환 선택 / 클라이언트쪽은 SockJS  client를 통해 통신.   
    -> 웹브라우저가 아닌 Android, ios : STOMP-websocket 메세지 프로토콜 호환 가능.
      
         

### 양방향통신 웹소켓 - SockJS 사용한 메신저 만들기
  ![Image name](https://eunbinoh.github.io//images/Final9.png){: width="100%" height="100%"}
     
- Handler   
```javascript   
@Component
public class HandlerChat extends TextWebSocketHandler{
	@Autowired
	private MsService msService;	
	private Map<String,WebSocketSession> sessionList = new HashMap<String, WebSocketSession>();
	// 웹소켓 서버 접속 메소드 
	@Override
	public void afterConnectionEstablished(WebSocketSession session) throws Exception {
		// 채팅방에 접속한 사용자 세션을 리스트에 저장
		Map<String, Object> map = session.getAttributes();
		String userId = (String)map.get("userId");
		sessionList.put(userId, session);
		// Login 알림 ( 자신의 입장은 제외시킴, 그룹방의 경우 첫입장자만 제외 )
		if(sessionList.size()>1) {
			for (String user : sessionList.keySet()) {
				sessionList.get(user).sendMessage(new TextMessage("<p> [ID]" + userId + "님이  LogIn 하셨습니다. </p>"));
			}
		}
	}
	// 서버로 전송된 메세지 처리 메소드
	@Override
	protected void handleTextMessage(WebSocketSession session, TextMessage message) throws Exception {
		String chat = message.getPayload();
		Gson gson = new Gson();
		ChatVo chatVo = gson.fromJson(chat, ChatVo.class);
		// 모든 세션에 채팅 전달
		if(chatVo.isFirst) {
		}else {
			for (String user: sessionList.keySet()) {
				if(sessionList.get(user).equals(session)) {
					sessionList.get(user).sendMessage(new TextMessage(
							"<div class='media'>" + "<p class='mychatp'>" +
							 chatVo.message +"</p>" +"</div>"));
				}else {
					sessionList.get(user).sendMessage(new TextMessage(
							"<div class='media'>" + "<a class='pull-left'>" +
							"<img class='media-object img-circle img-chat' src='resources/assets/images/dp.png'>" +
							"<div class='media-body'>"+"<h4 class='media-heading'>"+ chatVo.userName +"</h4>" + "</div>"+
							"</a>" + "<p class='otherchatp'>" + chatVo.message + "</p>" +"</div>"));
				}
				//세션에 혼자접속해있을시 상대방에게 안읽은메세지 처리로 저장
				if(sessionList.size()==1) {
					 chatVo.setCount(1);
				} else {
					 chatVo.setCount(0);
				}
			}
		}
		msService.insertMessage(chatVo);
	}
	// 서버 연결 종료 메소드
	@Override
	public void afterConnectionClosed(WebSocketSession session, CloseStatus status) throws Exception {
		 // 채팅방에서 퇴장한 사용자 세션을 리스트에서 제거 
		 Map<String,Object> map = session.getAttributes(); 
		 String userId = (String)map.get("userId");
		 sessionList.remove(userId);
		 
		 //리스트 제거시 유저 제외, map wrapping 
		 List<String> list = ConnectChatUserList.getInstance().userMap.get("userlist");
		 list.remove(userId);
		 ConnectChatUserList.getInstance().userMap.put("userlist", list);
		 
		 if(sessionList.size()>0) { 
			 for (String user: sessionList.keySet()) {
				 sessionList.get(user).sendMessage(new TextMessage("<p> [ID]"+userId + "님이 LogOut 하셨습니다." +"</p>")); 
			}
		 } 
	}
}
```    
- Controller     

~~~javascript   
public class MsController {
	@Autowired
	private MsService msService;
	private Member loginUser;

	@RequestMapping(value = "msStart", method = RequestMethod.GET)
	public String home(HttpSession session, Model model) {
		String mId =""; //유저 아이디
		
		//유저정보(메시지발신인) 받아오기
		loginUser = (Member) session.getAttribute("loginUser");
		mId = loginUser.getmId();
		
		//회원정보(메세지수신인) 받아오기
		ArrayList<Member> tolist = msService.tolist();
		
		//참여멤버 리스트
		List<String> list = ConnectChatUserList.getInstance().userMap.get("userlist");
		list.add(mId); //리스트에 유저 추가
		
		//map에 wrapping
		ConnectChatUserList.getInstance().userMap.put("userlist", list);

		model.addAttribute("tolist",tolist);

		return "ms/msStart";
	}
	@ResponseBody
	@RequestMapping(value = "rno_{roomId}", produces="application/json; charset=UTF-8")
	public List<Messenger> messageList(@PathVariable int roomId, Model model, HttpServletResponse response) throws IOException {
		List<Messenger> mList = new ArrayList<Messenger>();
		mList = msService.messageList(roomId);
		response.setContentType("application/json; charset=utf-8");
		// 메세지 카운트 0으로 바뀌기 (읽음 처리)
		for(int i = 0; i < mList.size(); i++) {
			if(mList.get(i).getReadcount()==1) {
				msService.updateCount(roomId);
			}
		}
		return mList;
	}
~~~
   
   
--- 

#### 대화목록 History(채팅방 형식), 소속부서원만 보이는 그룹톡방 1번으로 정렬
##### 그룹방은 삭제버튼 보이지 않음, 새로운 알람시 new label 생성
  ![Image name](https://eunbinoh.github.io//images/Final5.png){: width="100%" height="100%"}     

- Controller   

``` javascript 
@ResponseBody
	@RequestMapping(value="chatRoomList" , produces="application/json; charset=UTF-8")
	public List<ChatVo> RoomList(Room r, String userName,HttpSession session,HttpServletResponse response)throws IOException {
		loginUser = (Member)session.getAttribute("loginUser");
		int deptNo = loginUser.getDeptNo();
		int readCount = 0;
		List<ChatVo> chatList = new ArrayList<ChatVo>();
		List<Room> msList = msService.chatRoomList(userName,deptNo);

		for (int i = 0; i < msList.size(); i++){
			ChatVo chatVo = new ChatVo();
			String toId = msList.get(i).getTid();
			String fromId = msList.get(i).getFid();
			chatVo.setRoomId(msList.get(i).getRno());
			
			//방 참여자 중 본인이름이 아닌 상대방 이름으로 채팅방 이름 표시
			if( toId.equals(userName)) {
				chatVo.setUserName(toId);
				chatVo.setToId(fromId);
			}else {
				chatVo.setUserName(fromId);
				chatVo.setToId(toId);
			}
			//방마다 안읽은 메세지 수 카운트
			if(chatVo!=null) {
				readCount = msService.readCount(chatVo);
				chatVo.setCount(readCount);
			}
		chatList.add(chatVo);
		}
		return chatList;
	}   

```   
   
- Mapper (쿼리문/Oracle)      

```javascript   

	<select id="RoomList" resultMap="RoomResultMap">
		SELECT *
		  FROM ROOM
		 WHERE (FID = #{userName} OR TID = #{userName} )
		   AND RSTA ='Y' OR RSTA = #{deptno}
		 ORDER BY RNO DESC
	</select>
	
	<select id="selectList" resultMap="MsResultMap">
	  SELECT *
		FROM MS
	   WHERE RNO=#{roomId}
	</select>   

```
--- 
#### 부서별,직급별로 사원목록 조회해와서 선택한 사람과 대화하기   
##### 기존 대화기록 중에 방이 있는지 체크 후, 없으면 생성, 있으면 연결해서 들어가기   
  ![Image name](https://eunbinoh.github.io//images/Final8.png){: width="100%" height="100%"}    
   
   
- Controller      

```javascript   

@ResponseBody
	@RequestMapping("createChat")
	public Room createChat(Room r, String userName, String toId) {
		r.setFid(userName);
		r.setTid(toId);
		//이전기록에서 같은사람과의 대화방이 존재했는지 체크(select)
		Room exist = (Room)msService.existChatRoom(r);
		ChatVo chatVo = new ChatVo();
		chatVo.setToId(toId);
		chatVo.setUserName(userName);
		
		// DB에 방이 없을 때  생성(insert)
		if(exist == null){
			int result = msService.insertRoom(r);
			if(result>0) {
				exist = msService.existChatRoom(r);
				exist.setTid(toId);
				exist.setFid(userName);
				return exist;
			}else {
				return null;
			}
		// DB에 이미 방이 있을 때	
		}else{
			  exist.setTid(toId);
			  exist.setFid(userName);
			  return exist;
		}
	}   

```   

-  Mapper (쿼리문/Oracle)   

```javascript   

	<select id="toList" resultMap="MemberResultMap">
	   SELECT M_CODE, M_NAME, M_ID, DEPT_NO, DEPT_NAME, J_NAME, J_NO
	     FROM MEMBER
	     JOIN DEPARTMENT USING(DEPT_NO)
	     JOIN JOB USING(J_NO)
		  WHERE M_STATUS='Y'
	 ORDER BY DEPT_NO ASC, J_NO DESC
	</select>

<select id="existRoom" resultMap="RoomResultMap">
		SELECT *
		  FROM ROOM
		 WHERE FID IN (#{fid},#{tid}) 
		   AND TID IN (#{tid},#{fid})
		   AND RSTA='Y'
	</select>
	
	<insert id="insertRoom">
		INSERT INTO ROOM
		VALUES (SEQ_RNO.NEXTVAL, #{fid}, #{tid}, DEFAULT)
	</insert>   

```   


#### 채팅방(이전 메세지기록 DB + 소켓 실시간 채팅)
  ![Image name](https://eunbinoh.github.io//images/Final6.png){: width="100%" height="100%"}   

- Controller   

```javascript   

@ResponseBody
	@RequestMapping(value = "rno_{roomId}", produces="application/json; charset=UTF-8")
	public List<Messenger> messageList(@PathVariable int roomId, Model model, HttpServletResponse response) throws IOException {
		List<Messenger> mList = new ArrayList<Messenger>();
		mList = msService.messageList(roomId);
		response.setContentType("application/json; charset=utf-8");	
		// 메세지 카운트 0으로 바뀌기 (읽음 처리)
		for(int i = 0; i < mList.size(); i++) {
			if(mList.get(i).getReadcount()==1) {
				msService.updateCount(roomId);
			}
		}
		return mList;
	}   

```   
   
-  Mapper (쿼리문/Oracle)   

```javascript   

<select id="selectList" resultMap="MsResultMap">
	   SELECT *
		 FROM MS
	    WHERE RNO=#{roomId}
	</select>

	<insert id="insertMs">
		INSERT INTO MS
		VALUES (SEQ_MS.NEXTVAL, #{message}, TO_CHAR(SYSDATE,'YY-MM-DD AMHH:MI:SS'), #{roomId}, #{count}, #{userName}, #{toId})
	</insert>
	
	<select id="readCount" resultType="_int">
		SELECT COUNT(*) 
		  FROM MS
         WHERE READCOUNT=1 
		   AND RNO=#{roomId} 
		   AND M_CODE=#{toId}
	</select>
	
	<update id="updateCount">
		UPDATE MS 
		   SET READCOUNT = 0
		 WHERE RNO=#{roomId}
	</update>   

```
   
#### MS.jsp (socket connect, ajax,.. 중략)
  ![Image name](https://eunbinoh.github.io//images/Final4.png){: width="100%" height="100%"}
- sockJS   

```javascript   

$(document).ready(function() {
	var sock; //웹소켓 전역변수 설정
	
    // 채팅목록 버튼 누르면 열리기 / 채팅창 가리기
	$(".listBtn").on('click', function(){
		//채팅창
		if($('.chatContainer').hasClass("display-none")){
	    }else{             
	        $('.chatContainer').toggleClass("display-none");  
	         onClose();
	         $("#toId").val(""); //선택되어 있는 사람 있으면 null값 세팅
			 window.location.reload();
	    }
		//채팅목록(목록 열려있는 상태에서 목록 버튼)
	    if(!$('.chatListContainer').hasClass('display-none')){  
	    	getRoomList(); 
	    }else{
	    	$('.chatListContainer').toggleClass("display-none");
	    	getRoomList(); 
	    }
	});  
	
	$("#sendBtn").click(function() {
		sendMessage();
	});
	$('#message').keydown(function(key) {
		// 엔터키 입력시 메세지 전송
		if (key.keyCode == 13) {
			sendMessage(); 
		}
	});

	// 5초에 한번씩 채팅 목록 불러오기(실시간 알림 전용)
    setInterval(function(){
	    // 방 목록 불러오기 (업데이트)
	    getRoomList();
	    
    },5000); 
});
//document ready End/
	
	function connect() { 
		sock = new SockJS("<c:url value='/chat'/>");
		sock.onopen = onOpen; //웹 소켓에 이벤트가 발생했을 때 호출될 함수 등록
		sock.onmessage = onMessage; //메세지 수신시 출력 메소드
		sock.onclose = onClose; //웹소켓 연결 종료시  메소드
	}
	function onOpen() {
		console.log("sock open");
	}
	function onClose() {
		console.log("sock close");
	}   

```   
   
- 채팅방 대화 FORM & AJAX   
     
```javascript   

	var roomId;
	var toId;
	//방번호 찾아서 들어간뒤 웹소켓 연결
	function enterRoom(obj){
		//List에서 들어왔을때
		if($('#toId option:selected').val()==''){
		       roomId = obj.getAttribute("rno");
		       toId = obj.getAttribute("tid");
		}else{
		//slectbox로 들어왔을때
		       roomId = obj.rno;
		       toId = obj.tid;
		}
		$("#hiddenRoomId").val(roomId);
		$("#hiddenToId").val(toId);

		// 현재 html에 추가되었던 동적 태그 전부 지우기
		$(".msList").html("");

		// 해당 채팅 방의 메세지 목록 불러오기
		$.ajax({
			url: "rno_"+roomId,
			data:{
			    "userName": "${loginUser.name}",
			    "toId"	  : toId,
			},
			dataType:"json",
			contentType: "application/json; charset=utf-8",
			success:function(data){
		    		$msList = $(".msList");
	                $msList.html("");            
	                if(data.length > 0){
	                  //읽지 않은 메세지 초기화
	                  countAll = 0;
		                for(var i in data){
		                // 태그 동적 추가
		                    if( "${loginUser.name}" == data[i].m_code){ //보낸 사람이 '나'이면 오른쪽에 표시
		                    	  
		                    	$li = $([
		                            '<li class="media right">'
		                            ,'    <div class="msj macro pull-right">'
		                            ,'        <div class="text text-r">'
		                            ,'            <p class="mychatp">fgg</p>'
		                            ,'            <p><small class="msToDate">21-08-27 오후08:59:23</small></p>'
		                            ,'        </div>'
		                            ,'    </div>'
		                            ,'</li>'
		                        ].join(''));   
		                      
		                    	$li.find(".mychatp").text(data[i].ms_content);
		                    	$li.find(".msToDate").text(data[i].ms_time);
		                      
		                    }else{ //보낸사람이 상대방이면 왼쪽에 표시
		                    	$li = $([
	                                '<li class="media">'
	                                ,'  <a class="pull-left" >'
	                                ,'      <img class="media-object img-circle img-chat" src="resources/assets/images/dp.png">'
	                                ,'  	<h4 class="media-heading"></h4>'
	                                ,'  </a>'
	                                ,'  <div class="media-body">'
	                                ,'        <div class="text text-r">'
	                                ,'            <p class="otherchatp">fgg</p>'
	                                ,'            <p><small class="msSendDate">21-08-27 오후08:59:23</small></p>'
	                                ,'        </div>'
	                                ,'  </div>'
	                                ,'</li>'
	                            ].join(''));
		                    
		                        $li.find(".otherchatp").text(data[i].ms_content);
		                        $li.find(".msSendDate").text(data[i].ms_time);
		                        $li.find(".media-heading").text(data[i].m_code);
	                        }
		                    $msList.append($li);
	  	                  }
		              }
		          window.scrollTo(0,document.body.scrollHeight);
				},
		error: function(data){
			console.log("enterRoom error");
		}
	});//ajax end( 채팅 데이터 로딩)
      // 웹소켓 연결 (소켓 서버 연결 시작)
      connect();
	}
      
```   
   
---         
	     	    

# 후기
- 처음에 웹소켓-서버 연결이 너무 어렵고, 계속 접속 끊어지고해서 오기로 이틀 밤을 샜다..
- 솔루션으로 찾아낸 SockJS를 채택하고 나서도, 적용하는데에 난관은 계속됐다.
- 구글링하면 나오는 소스들은 웹소켓, socket-io, STOMP, 부분적으로만 있어서 긁어모으다 보면 오히려 혼란이 더 심해졌고..
- SockJS 서버 통신을 성공시키고 난 뒤에도, 단순 익명채팅방이 아니라 그룹웨어에서 쓰는 메신저로 쓸 목적이였기 때문에,
   사원 선택해서 1:1 메세지도 호환되게 만드려고 하니 필요한 요소들이 뒤늦게 발견되고 수정하고 반복...
- 마침내, 소통이 되고, 조건 지정해서 둘의 대화 폼이 완성 될 때. 셋 이상의 대화가 이루여 졌을 때.
#### 그 기쁨이란 !! 
### 정말 재밌었고 뿌듯했다.
## 이제 진짜 첫걸음 내딛었다 !
   

![Image name](https://eunbinoh.github.io//images/winkS.png){: width="20%" height="20%"}
     
