# DispatcherServlet

## FrontController

기존 Servlet으로 web을 개발할 때 `web.xml`에 Servlet 등록하고 url을 매핑해주는 설정 작업이 필요했다.  
하지만 서비스가 거대해지면서 mapping해야할 것들이 비대해졌고 `web.xml`을 개발과 별도로 관리를 해주어야 한다는 점에서 번거로움이 발생했다.  
  
여기서 `FrontController` 패턴이 등장한다.

