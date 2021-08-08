---
layout: post
---
<img src="{{ site.baseurl }}/images/dietBoard.jpg">

Sed lobortis urna ut mi volutpat, sit amet euismod sapien tincidunt. Nulla facilisi. Pellentesque quis tempus neque. Mauris dictum ac sapien nec congue. In hac habitasse platea dictumst. Duis id pellentesque nisl. Morbi eget massa magna.


{% highlight Java %}
<script>
	$(function(){
		$('#diet').addClass('active');
		$('.menus').not('#diet').removeClass('active');
	});
	
      $('#selectDiet ul>li>label').on('click', function(){
         $(this).next().slideToggle();
         $('#selectDiet ul li ul').not($(this).next()).slideUp();
      });
      
      $('#selectDiet ul li ul li').on('click', function(){
         $('#tBox').val($(this).attr('value'));
      });
     
      $('#tBox').keyup(function(e){
          var memo = $(this).val();
          $('#tcount').html(memo.length);
          if(memo.length>100){
              alert("100자 이내로 입력해주세요!");
              $(this).val(memo.substring(0,100));
              $('#tcount').html('100');
              $('#tcount').css('color','orangered');            
          }else{
       	   $('#tcount').css('color',' #c1dff0');  
          }
      });
    
      $('#saveBtn').on('click',function(){
		var dateNo = $('#date').val();			
		var dtext = $('#tBox').val();			
		var userId = '<%=loginUser.getUserId()%>';
			$.ajax({
				url : 'dForm.save',
				data : {
					dateNo : dDate,
					dtext : memo,
					userId : userId
				},
				success : function(data) {
					$('#tBox').text('');
					var $tB = $('#dTable');
					$tB.html('');
					for ( var i in data) {
						var d = data[i];
						var $tr = $('<tr>');
						var $dateTd = $('<th>').html(d.dDate);
						var $memoTd = $('<td>').html(d.memo);
						$tr.append($dateTd);
						$tr.append($memoTd);
						$tB.append($tr);
					}
				}
			});
		});
		$('#chall').click(function() {
			if ($("#chall").prop("checked")) {
				$("input[type=checkbox]").prop("checked", true);
			} else {
				$("input[type=checkbox]").prop("checked", false);
			}
		});
		$('#deleteBtn').on('click', function() {
			var charr = new Array();
			$("input[name=checkRow]:checked").each(function(i) {
				charr[i] = $(this).attr("value");
				console.log("charr[]:" + charr[i]);
			});
			if (charr.length == 0) {
				alert("삭제하려는 글을 체크하세요");
			} else {
				$.ajax({
					url : 'dForm.delete',
					data : {
						'charr' : charr
					},
					success : function(result) {
						console.log(result);
						if (result == 'd.success') {
							console.log("선택된 식단기록 삭제 성공");
							location.reload();
						} else {
							console.log("선택된 식단기록 삭제 실패");
						}
					},
					error : function(result) {
						console.lof("res:" + result)
						console.log("ajax 삭제 실패");
					}
				});
				charr = new Array(); //초기화
			}
		});
		function validate() {
			var date = $('#date');
			var memo = $('#tBox');
			if (date.val().trim().length == 0) {
				alert("날짜를 선택하세요");
				date.focus();
				return false;
			}
			if (memo.val().trim().length == 0) {
				alert("추천 식단을 선택하거나 식단 내용을 작성해주세요");
				memo.focus();
				return false;
			}
			return true;
		}
	</script>
{% endhighlight %}

Sed lobortis urna ut mi volutpat, sit amet euismod sapien tincidunt. Nulla facilisi. Pellentesque quis tempus neque. Mauris dictum ac sapien nec congue. In hac habitasse platea dictumst. Duis id pellentesque nisl. Morbi eget massa magna.