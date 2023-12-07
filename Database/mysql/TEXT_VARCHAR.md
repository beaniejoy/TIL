# TEXT, VARCHAR 차이

## 지금까지 정리한 내용  

> InnoDB 기준

페이지 크기에 따라 maximum row length가 달라지긴 하지만 보통 페이지 크기의 절반 미만 정도 되는 듯  
(**기본 페이지 크기 16KB > 최대 row length 8,117 bytes 정도**)  

특정 칼럼이 **max row length**를 넘어가면 기존 Inline 방식으로 저장되던 것이 Off-page 방식으로 변경

## 궁금증
길이가 긴 게시글을 작성하는 게시판 개발할 때 게시글의 길이 제한을 4000자로 할 거라 TEXT 타입으로 지정하고자 함  
하지만 표면적으로 보이는 텍스트는 예를 들어 200자만 보이고 싶을 때는 어떻게 조회??
  
## References
- [innodb 행 저장 방식(1) 저장 파일 형식](https://yhjin.tistory.com/87)
- [TEXT & VARCHAR 차이에 대한 좋은 글](https://medium.com/daangn/varchar-vs-text-230a718a22a1)