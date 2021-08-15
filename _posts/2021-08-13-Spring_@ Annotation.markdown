---
layout: post
---

# @RequestParam
    - 1개의 HTTP 파라미터를 얻기 위해 사용됨
    - required=false 설정 필요 (default true)   
   
# @RequestBody
    - Json으로 받은 HTTP Body 데이터를 MessageConverter를 통해 변환
    - 변환시켜주기 때문에(Reflection), Setter 없이 변수 그대로 저장    
   
# @ModelAttribute
    - multipart/form-data 형태의 Body, Param을 Setter를 통해 1:1로 객체에 바인딩
    - 바인딩은 Setter 필요, setter없으면 변수가 저장되지 않음   
   
# @
   

# @
   

# @
    

# @
   

# @

- - -
- - -

## Example

 - Model 객체 
{% highlight Java %}

@Getter
@Setter
@ToString
public class Board {
    private int index; // 게시물 번호
    private String writer; // 작성자 이름
    private String contents;  // 작성 내용
}

{% endhighlight %}


 - Spring 
{% highlight Java %}

@RestController
@RequestMapping("/board")
@RequiredArgsConstructor
@Log4j2
public class BoardController {

    private final BoardService boardService;

    @PostMapping("/requestBody")
    public ResponseEntity<Board> requestBody(@RequestBody final Board board) {
        // @RequestBody는 Json 형태의 HTTP Body를 MessageConverter를 통해 Java 객체로 변환
        log.info(board);
        return ResponseEntity.ok(boardService.add(board));
    }

    @PostMapping("/modelAttribute")
    public ResponseEntity<Board> modelAttribute(@ModelAttribute final Board board) {
        // @ModelAttribute는 multipart/form-data 형태의 HTTP Body, 파라미터들을 1:1로 객체 바인딩
        // 만약 Setter함수가 없다면 매핑을 시키지 못하고, null
        log.info(board);
        return ResponseEntity.ok(boardService.add(board));
    }

    @GetMapping("/list")
    public ResponseEntity<List<Board>> requestParam(
            @RequestParam(value = "searchKeyWord1", required = false) final String searchKeyWord1,
            @RequestParam(value = "writer", defaultValue = "MangKyu") final String searchKeyWord2) {

        // searchKeyWord1는 required가 false이기 때문에 없을 수도 있음
        final List<Board> boardList = searchKeyWord1 != null
                ? boardService.getBoardList(searchKeyWord1)
                : boardService.getBoardList();

        // searchKeyWord2는 required가 true이기 때문에 반드시 요청 파라미터로 존재해야 함.
        log.info(searchKeyWord2);
        return ResponseEntity.ok(boardList);
    }
}
{% endhighlight %}

{% highlight Java %}
Request URL: http://localhost:8080/board/modelAttribute?contents=Contents
Request Body:
  - type: multipart/form-data
  - data: {
      "index": 1,
      "writer": "MangKyu"
    }
{% endhighlight %}
 
- - -


