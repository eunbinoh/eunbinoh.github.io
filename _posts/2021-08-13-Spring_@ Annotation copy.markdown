---
layout: post
title:  "Spring_@Annotaion"
date:   2021-08-13 00:00:00
categories: [Spring & JAVA]
tags: [Annotation, RequestParam, RequestBody, ModelAttribute]
---

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
                 
# @        
   
      
---   
- - -
- - -
   

## Example

- Model 객체 
```Java 

@Getter   
@Setter   
@ToString   
public class Board {   
    private int index; // 게시물 번호   
    private String writer; // 작성자 이름   
    private String contents;  // 작성 내용   
}
```   
      
- Spring 
~~~ Java

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
~~~

```Java
Request URL: http://localhost:8080/board/modelAttribute?contents=Contents
Request Body:
    -type: multipart/form-data
    -data: {
            "index"  : 1,
            "writer" : "MangKyu"
    }
```
 
- - -


