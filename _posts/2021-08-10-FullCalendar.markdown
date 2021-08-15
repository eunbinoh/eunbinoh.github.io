---
layout: post
---
   

# Google FullCalendar 분석하고 설계 새로해서 변형해보기 !
   
## Calendar.jsp  
```
<div class="col-md-12 col-xl-12">
     <div class="card">                                   
          <div class="container">
          
               <!-- 일자 클릭시 메뉴오픈 -->
               <div id="contextMenu" class="dropdown clearfix">
                    <ul class="dropdown-menu dropNewEvent" role="menu" aria-labelledby="dropdownMenu" id="sccode"
                         style="display:block;position:static;margin-bottom:5px;">
                         <li value="1"><a tabindex="-1">부서일정</a></li>
                         <li value="2"><a tabindex="-1" >개인일정</a></li>
                         <li class="divider"></li>
                         <li><a tabindex="-1" href="#" data-role="close">Close</a></li>
                    </ul>
               </div>
     
               <div id="wrapper">
                    <div id="loading"></div>
                    <div id="calendar"></div>
               </div>
     
               <!-- 일정 추가 MODAL -->
               <div class="modal fade" tabindex="-1" role="dialog" id="eventModal">
                    <div class="modal-dialog" role="document">
                         <div class="modal-content">
                         <div class="modal-header">
                              <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                              <span aria-hidden="true">&times;</span></button>
                              <h4 class="modal-title"></h4>
                         </div>
                         <div class="modal-body">
                              <div class="row">
                                   <div class="col-xs-12">
                                        <label class="col-xs-4" for="edit-title">일정명 : </label>
                                        <input class="inputModal" type="text" name="edit-title" id="edit-title" required="required" />
                                   </div>
                              </div>
                              <div class="row">
                                   <div class="col-xs-12">
                                        <label class="col-xs-4" for="edit-start">시작일 : </label>
                                        <input class="inputModal" type="text" name="edit-start" id="edit-start" />
                                   </div>
                              </div>
                              <div class="row">
                                   <div class="col-xs-12">
                                        <label class="col-xs-4" for="edit-end">종료일 : </label>
                                        <input class="inputModal" type="text" name="edit-end" id="edit-end" />
                                   </div>
                              </div>
                              <div class="row">
                                   <div class="col-xs-12">
                                        <label class="col-xs-4" for="edit-type">구분명 : </label>
                                        <select class="inputModal" name="edit-type" id="edit-type" style="width: 140px; height: 20px;">
                                             <option class="text-c-purple" value="1">부서일정</option>
                                             <option class="text-c-yellow" value="2">개인일정</option>
                                        </select>
                                   </div>
                              </div>
                              <div class="row" style="display:none">
                                   <div class="col-xs-12">
                                   <!-- **********loginUser 생성시 session 값으로 가져가기  **********-->
                                   <input type="text" for="e-id" id="e-id" value="20">
                                   <!-- **********loginUser 생성시 session 값으로 가져가기  **********-->
                                        <label class="col-xs-4" for="edit-color">색상</label>
                                        <select class="inputModal" name="color" id="edit-color">
                                                  <option value="#660099">보라색</option>
                                                  <option value="#FFB64D">주황색</option>
                                        </select>
                                   </div>
                              </div>
                         </div>
                         <div class="modal-footer modalBtnContainer-addEvent">
                              <button type="button" class="btn btn-default" data-dismiss="modal">취소</button>
                              <button type="button" class="btn btn-primary" id="save-event">저장</button>
                         </div>
                         <!--  일정 추가 MODAL -->
                         <!--  일정 수정 MODAL -->
                         <div class="modal-footer modalBtnContainer-modifyEvent">
                              <button type="button" class="btn btn-default" data-dismiss="modal">취소</button>
                         <%-- <c:if test="${ loginUser.id eq c.mCode }"> 부서 일정 등록자만 삭제 수정 가능--%>
                              <button type="button" class="btn btn-danger" id="deleteEvent">삭제</button>
                              <button type="button" class="btn btn-primary" id="updateEvent">저장</button>
                         <%-- </c:if> --%>
                         </div>
                         </div><!-- /.modal-content -->
                    </div><!-- /.modal-dialog -->
               </div><!-- /.modal -->
          </div><!--    /.container     -->
     </div>
</div>

```

- - -



## main.js      
### 구글 풀캘린더 오픈소스 윗부분 생략, custom하여 용도에 맞게 변형함.
```
eventRender: function (event, element, view) {
	  
	 var eventType = event.type;
	 var eNo = event.description;
	 var eId = event._id;
	 
	 if(eventType=='1'){
		 eventType = '부서일정';
	 }else if(eventType=='2'){
		 eventType = '개인일정';
	 }
	  
     //일정에 hover시 요약
     element.popover({
     title: $('<div />', {
        class: 'popoverTitleCalendar',
        text: event.title
     }).css({
        'background': event.backgroundColor,
        'color': event.textColor
     }),
      content: $('<div />', {
          class: 'popoverInfoCalendar'
     }).append('<p><strong>구분 : </strong> ' + eventType + '</p>')
        .append('<p><strong>번호 : </strong> ' + eNo + '</p>')
        .append('<div class="popoverDescCalendar"><strong> 등록자 : </strong> ' + eId + '</div>'),
     delay: {
        show: "800",
        hide: "50"
     },
     trigger: 'hover',
     placement: 'top',
     html: true,
     container: 'body'
     });

    return true;
    // return filtering(event);

 
},

  /* ****************
   *  일정 받아옴 
   * ************** */
events: function (start, end, timezone, callback) {

	var eventId = $('#e-id');
	  //로그인시 세션에서 아이디 가져오기 (임시아이디)  
	  
     $.ajax({
      type: "get",
      url: "listCal.do",
      dataType: 'json',
      data: {
    	  // 화면이 바뀌면 Date 객체인 start, end 가 들어옴
    	  'startDate' : moment(start).format('YYYY-MM-DD'), 
		  'endDate'   : moment(end).format('YYYY-MM-DD'),
		  'eventId'   : eventId.val()
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
    	  debugger;
    	  console.log("select list error");
      }
    });
  },

  eventAfterAllRender: function (view) {
    if (view.name == "month") $(".fc-content").css('height', 'auto');
  },

  //일정 리사이즈
  eventResize: function (event, delta, revertFunc, jsEvent, ui, view) {
    $('.popover.fade.top').remove();

    /** 리사이즈시 수정된 날짜반영
     * 하루를 빼야 정상적으로 반영됨. */
    var newDates = calDateWhenResize(event);

    //리사이즈한 일정 업데이트
    $.ajax({
      type: "get",
      url: "",
      data: {
        //id: event._id,
        //....
      },
      success: function (response) {
        alert('수정: ' + newDates.startDate + ' ~ ' + newDates.endDate);
      }
    });

  },

eventDragStart: function (event, jsEvent, ui, view) {
     draggedEventIsAllDay = event.allDay;
},

     //일정 드래그앤드롭
     eventDrop: function (event, delta, revertFunc, jsEvent, ui, view) {
     $('.popover.fade.top').remove();

    // 드랍시 수정된 날짜반영
    var newDates = calDateWhenDragnDrop(event);

    //드롭한 일정 업데이트
    $.ajax({
      type: "get",
      url: "",
      data: {
        //...
     },
      success: function (response) {
               alert('수정:'+ newDates.startDate +'~'+ newDates.endDate);
      }
    });

},

     select: function (startDate, endDate, jsEvent, view) {
	  
     $(".fc-body").unbind('click');
     $(".fc-body").on('click', 'td', function (e) {
    	
	//클릭한 날짜의 td위치(좌표) 가져오기 
	var $item = $(".fc-highlight").offset();
	
	$("#contextMenu")
		.addClass("contextOpened")
		.css({
		  display: "block",
		      left: $item.left-230, 
		      top: $item.top-610
		    });
		  return false;
	});
    var today = moment();

    //일정 등록 창에서의 날짜
    if (view.name == "month") {
      startDate = moment(startDate).format('YYYY-MM-DD');
      endDate = moment(endDate).subtract(1, 'days').format('YYYY-MM-DD');

    } else {
      startDate = moment(startDate).format('YYYY-MM-DD');
      endDate = moment(endDate).add(1,'days').format('YYYY-MM-DD');
    }

    //날짜 클릭시 카테고리 선택메뉴
    var $contextMenu = $("#contextMenu");
    $contextMenu.on("click", "a", function (e) {
          e.preventDefault();

     //닫기 버튼이 아닐때
     if ($(this).data().role !== 'close') {
        newEvent(startDate, endDate, $(this).html());
     }
      
      $contextMenu.removeClass("contextOpened");
      $contextMenu.hide();
     });

     $('body').on('click', function () {
          $contextMenu.removeClass("contextOpened");
          $contextMenu.hide();
     });

     },

	//이벤트 클릭시 수정이벤트
     eventClick: function (event, jsEvent, view) {
	editEvent(event);
	}	
});
	
	if (event.allDay) {
	    newDates.startDate = moment(event.start._d).format('YYYY-MM-DD');
	    newDates.endDate = moment(event.end._d).add(1,'days').format('YYYY-MM-DD');
	} else {
	    newDates.startDate = moment(event.start._d).format('YYYY-MM-DD');
	    newDates.endDate = moment(event.end._d).format('YYYY-MM-DD');
	}
	
	  return newDates;
	}

     //2일이상 all day
     else if (event.allDay && event.end !== null) {
     newDates.startDate = moment(event.start._d).format('YYYY-MM-DD');
     newDates.endDate = moment(event.end._d).add(1,'days').format('YYYY-MM-DD');
     }
	
	  return newDates;
}
```