---
layout: post
title:  "SockJS 메신저 (그룹웨어용)"
categories: [Project]
tags: [SocketJS, websocket, messenger, chatting]
---

# @RequestParam
  ![Image name](https://eunbinoh.github.io//images/Final9.png)

# @RequestBody
    - Json으로 받은 HTTP Body 데이터를 MessageConverter를 통해 변환
    - 변환시켜주기 때문에(Reflection), Setter 없이 변수 그대로 저장    
   
# @ModelAttribute
    - multipart/form-data 형태의 Body, Param을 Setter를 통해 1:1로 객체에 바인딩
    - 바인딩은 Setter 필요, setter없으면 변수가 저장되지 않음   
   
      
 