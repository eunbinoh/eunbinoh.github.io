---
layout: post
title: "Full_Calendar"
date: 2021-08-30 00:00:00
categories: [Project]
tags: [FullCallendar, main.js, events.]
---
   

# Project - 그룹웨어 일정관리   


### #개발 디자인 (구상하기) 
 - 같은 부서끼리만 부서일정 공유하기 (DB JOIN)
 - 개인일정은 로그인유저 개인것만 보여지기 (DB 서브쿼리 또는 UNION)
 - 장기간 프로젝트(1달 이상) 일정은 보여지는 범위 조건 설정 (기존 제한 체크)
 - 부서 일정 수정,삭제는 등록자만 또는 부장 직급만 권한 부여(ID 보여지게 하기)
 - 하루/이틀 이상의 일정은 계산법이 다르기 때문에 add/subtract.format 신경쓰기   
    

# Final Project에서 만든 그룹웨어 일정관리 - 캘린더     
- Google FullCalendar
    + css : bootstrap-datetimepicker.min.css, fullcalendar.min.css, main.css
    + js : fullcalendar.min.js, addEvent.js, editEvent.js,..
   
-> etc file들이 더 있지만, 선택적으로 사용하고 주로 모달과 폼만 가져옴   
-> 디자인 구도, 색깔, 크기, 효과 변경(내취향대로 커스터마이징)   
-> ajax, back단은 처음부터 끝까지 직접 구상해서 작성함.   
        
   
      
### 부서일정과 개인일정 전체 조회 
  ![Image name](https://eunbinoh.github.io//images/Final2.png){: width="100%" height="100%"}   

- Controller
```java
@SessionAttributes("loginUser")
@Controller
public class CalendarController {
	@Autowired
	private CalendarService cService;
	
	@RequestMapping(value = "addCal", method = RequestMethod.POST)
	@ResponseBody
	public String addCal(@RequestBody Calendar c,HttpSession session, HttpServletResponse response) throws MainException{
		Member loginUser = (Member)session.getAttribute("loginUser");
		String mCode = loginUser.getmCode();
		response.setContentType("application/json; charset=UTF-8");
		c.setmCode(mCode);  
		int result = cService.addCal(c);
		if(result>0) {
			return "success";
		}else {
			throw new MainException("일정 생성에 실패했습니다.");
		}
	}
	
	@RequestMapping(value="listCal", produces="application/json; charset=UTF-8")
	@ResponseBody 
	public List<Calendar> listCal(@RequestParam("eventId") String mcode, 
									@RequestParam("edept") String deptNo, 
									@RequestParam("startDate") String sDate, 
									@RequestParam("endDate") String eDate, 
									Model model) {
		List<Calendar> cList = cService.listCal(mcode,deptNo,sDate,eDate);
		return cList;		
	}
	
	@RequestMapping(value="updateCal", method = RequestMethod.POST)
	@ResponseBody
	public String updateCal(@RequestBody Calendar c, Model model) throws MainException {
		int result = cService.updateCal(c);

		model.addAttribute("Calendar",c);
		
		if(result>0) {
			return "success";
		}else {
			throw new MainException("일정 변경에 실패했습니다.");
		}
	}
```
   
- Mapper   
   
```java     
   
<mapper namespace="calendarMapper">
	<resultMap type="calendar" id="CalResultMap">
		<id column="SC_CODE" property="code"/>
		<result column="DSC_NO" property="no"/>
		<result column="SC_TITLE" property="title"/>
		<result column="SC_SDATE" property="sDate"/>
		<result column="SC_EDATE" property="eDate"/>
		<result column="SC_STATUS" property="status"/>
		<result column="M_CODE" property="mCode"/>
	</resultMap>
	
	<insert id="addCal">
		INSERT INTO SCHEDULE
		VALUES(#{code}, SEQ_DSC.NEXTVAL, #{title}, TO_DATE(#{sDate}), TO_DATE(#{eDate}), DEFAULT, #{mCode} )
	</insert>

	<!-- 부서일정은 같은 부서만, 개인일정은 내일정만 선택해서 가져오기 -->
	<select id="listCal" resultMap="CalResultMap">
  		SELECT SC.SC_CODE, SC.DSC_NO, SC.SC_TITLE, SC.SC_SDATE, SC.SC_EDATE, SC.SC_STATUS, M.M_CODE, M.M_NAME
		  FROM SCHEDULE SC, MEMBER M 
		 WHERE SC.M_CODE= M.M_CODE
           AND SC.SC_STATUS='N'
  		   AND M.DEPT_NO= TO_NUMBER(#{deptNo})
		   AND SC.SC_CODE='1'
      	 UNION ALL        
        SELECT SC.SC_CODE, SC.DSC_NO, SC.SC_TITLE, SC.SC_SDATE, SC.SC_EDATE, SC.SC_STATUS, M.M_CODE, M.M_NAME
    	  FROM SCHEDULE SC, MEMBER M 
		 WHERE SC.M_CODE= M.M_CODE
           AND SC.SC_STATUS='N'
       	   AND SC.M_CODE=#{mcode}  
           AND SC.SC_CODE='2' 
	</select>
	
	<update id="updateCal">
	   UPDATE SCHEDULE
		  SET SC_CODE=#{code}, SC_TITLE=#{title}, SC_SDATE=TO_DATE(#{sDate}), SC_EDATE=TO_DATE(#{eDate})
		WHERE DSC_NO=#{no} AND M_CODE= #{mCode} 
	</update>   
          
```
   
   

 + 일정 등록.js   

```javascript   

 var newEvent = function (start, end, eventType) {
    if(eventType=='부서일정'){
    	eventType = '1';
    }else if(eventType=='개인일정'){
    	eventType = '2';
    }
    $("#contextMenu").hide(); //메뉴 숨김
    modalTitle.html('새로운 일정');
    editType.val(eventType).prop('selected', true);
    editTitle.val('');
    editStart.val(start);
    editEnd.val(end);
    
    //카테고리 선택에 따라 selected 자동 선택(선택된 값 가져오기)
    var eType= $('#edit-type option:selected');
    var sccode= editType.val();
    	console.log("etypeval:",eType.val());
    if(eType.val()==1){
    	$('#edit-color option:eq(0)').attr('selected',true);
    }else if(eType.val()==2){
    	$('#edit-color option:eq(1)').attr('selected',true);
    }
    addBtnContainer.show();
    modifyBtnContainer.hide();
    eventModal.modal('show');

    //새로운 일정 저장버튼 클릭
    $('#save-event').unbind();
    $('#save-event').on('click', function () {
        var eventData = {
            _id: eventId.val(),
            title: editTitle.val(),
            start: editStart.val(),
            end: editEnd.val(),
            type: editType.val(),
            username: '테스트1',
            description: editDesc.val(),
            backgroundColor: editColor.val(),
            textColor: '#ffffff',
            allDay: true
        };
        var eventParam = {
        		"code"	: editType.val(),
        		"title"	: editTitle.val(),
        		"sDate"	: editStart.val(),
        		"eDate"	: editEnd.val(),
        		"mCode" : eventId.val()
        }

        if (eventData.start > eventData.end) {
        	 alert('시작일과 종료일을 확인하세요.');
            return false;
        }
        if (eventData.title === '') {
        	alert('일정명을 작성해 주세요.');
            return false;
        }

        var realEndDay;
        if (event.end !== null) {
            eventData.start = moment(eventData.start).format('YYYY-MM-DD');
            //render시 날짜표기수정
            eventData.end = moment(eventData.end).format('YYYY-MM-DD');
            //DB에 넣을때(선택)
            realEndDay = moment(eventData.end).subtract(1, 'days').format('YYYY-MM-DD');
            eventData.allDay = true;
        }

        $("#calendar").fullCalendar('renderEvent', eventData, true);
        eventModal.find('input, textarea').val('');
        editAllDay.prop('checked', true);
        eventModal.modal('hide');
        console.log("eData:",eventParam);

        //새로운 일정 저장 ajax
        $.ajax({
        	type : 'POST',
        	url: 'addCal',
        	data: JSON.stringify(eventParam),
            contentType: "application/json",
            success: function (data) {
            	alert('일정이 등록 되었습니다.');
            	console.log(data); 
            	location.reload();
            },error: function(data){
            	alert('일정 등록에 실패했습니다.');
				console.log(data);
				console.log("add error");
			}
        });
    });    

```    
   

 + 일정 조회.js   

```javascript
events: function (start, end, timezone, callback) {
	var eventId = $('#e-id');
	var edept = $("#e-dept");
    $.ajax({
      type: "get",
      url: "listCal",
      dataType: 'json',
      data: {
    	  // 화면이 바뀌면 Date 객체인 start, end 가 들어옴
    	  'startDate' : moment(start).format('YYYY-MM-DD'), 
		  'endDate'   : moment(end).format('YYYY-MM-DD'),
		  'eventId'   : eventId.val(),
		  'edept'	  : edept.val()
      },
      success: function (cList) {
    	  var jsonArray = new Array();
    	  var count = 0;
	      for (var i=0; i<cList.length; i++){
	    	var obj = new Object();
			obj._id 	= cList[i].mCode;
			obj.description	= cList[i].no;
			obj.title 	= cList[i].title;
			obj.start	= moment(cList[i].sDate).format('YYYY-MM-DD');
			obj.end		= moment(cList[i].eDate).add(1,'days').format('YYYY-MM-DD');
			obj.type	= cList[i].code;
			obj.textColor = '#ffffff'
			if(obj.type=='1'){
				obj.backgroundColor = '#660099';
			}else if(obj.type=='2'){
				obj.backgroundColor = '#FFB64D';
			}
			jsonArray.push( obj );
	      }
	      callback(jsonArray);
	  },
      error: function(response){
    	  console.log("select list error");
      }
    });
}   

``` 
   
       
 + 일정 수정.js   
    
```javascript
var editEvent = function (event, element, view) {
    $('#updateEvent').data('id', event.description); //클릭한 이벤트ID
    $('.popover.fade.top').remove();
    $(element).popover("hide");
    if (event.end === null) {
        event.end = event.start;
    }
    if (event.allDay === true && event.end !== event.start) {
        editEnd.val(moment(event.end).subtract(1, 'days').format('YYYY-MM-DD'))
    } else {
        editEnd.val(event.end.format('YYYY-MM-DD'));
    }
    //업데이트 버튼 클릭시
    $('#updateEvent').unbind();
    $('#updateEvent').on('click', function () {
        if (editStart.val() > editEnd.val()) {
            alert('종료일이 시작일 보다 빠를 수 없습니다.');
            return false;
        }
        if (editTitle.val() === '') {
            alert('일정명은 필수입니다.')
            return false;
        }

        var statusAllDay;
        var startDate;
        var endDate;
        eventModal.modal('hide');
        event.title = editTitle.val();
        event.start = startDate;
        event.end = endDate;
        event.type = editType.val();
        event.description;
        var eventParam = {
        		"code"	: event.type,
        		"no"	: event.description,
        		"title"	: event.title,
        		"sDate"	: event.start,
        		"eDate"	: event.end,
        		"mCode" : event._id
        }
        
        //일정 업데이트
        $.ajax({
        	type : 'POST',
            url: "updateCal",
            data: JSON.stringify(eventParam),
            contentType: "application/json",
            success: function (data) {
	            	if(data=='success'){
		            	alert('일정이 수정 되었습니다.');
		            	location.reload();
	            	}
            },error: function(data){
	            	alert('일정 수정에 실패했습니다.');
					console.log("modify error");
            }
        });
    });
};   

``` 





# CSS, min.js, bootstrap 때문에 너무 힘들었던 나는 ..  
![Image name](https://eunbinoh.github.io//images/sadS.png){: width="20%" height="20%"}   

.      
.   
. col-sm,.. col-md,.. col-lg, ..        
.      
. modal,...  
.             
. event     
.    
. beautifier min.js...
.   
# 이번 풀캘린더 정면 돌파를 통해 도장 격파 Success!!  
![Image name](https://eunbinoh.github.io//images/punchS.png){: width="20%" height="20%"}   

