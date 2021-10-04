# Servlet Application

## Servlet
- `server` + `applet`
- 요청 당 쓰레드를 사용 (확장 CGI)
- 중요한 클래스 중 하나가 `HttpServlet`
- CGI보다 좋아진 점
  - 속도가 빠르다.
  - 플랫폼 독립적
  - 보안
  - 이식성

## Servlet 생명주기
- `init()`
  - 최초 요청을 받았을 때 실행되는 메소드
  - 최초 한 번 초기화 이후에 init 과정을 생략하게 된다.
- `service()`
  - 초기화 된 이후에 클라이언트 요청에 대한 처리를 위해 service 메소드 호출
  - 각 요청은 별도 쓰레드로 처리
  - HTTP Method에 따라 `doGet()`, `doPost()` 메소드로 위임
  - 보통은 `service()`보다 `doGet()`, `doPost()`를 구현해서 사용
- `destroy()`
  - Servlet Container의 판단에 따라 서블릿을 메모리에서 내려야할 시점에 `destroy()` 호출

## Servlet class 코드

### Servlet java 코드
```java
import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

public class HelloServlet extends HttpServlet {

  @Override
  public void init() throws ServletException {
    System.out.println("Servlet init");
  }

  @Override
  public void doGet(HttpServletRequest req, HttpServletResponse resp) throws IOException {
    System.out.println("Servlet doGet");
    resp.getWriter().write("Hello Servlet");
  }

  @Override
  public void destroy() {
    System.out.println("Servlet destroy");
  }
}
```
### web.xml 코드
- `main/webapp/WEB-INF/web.xml` 경로에 존재

```xml
<web-app>
  ...

  <servlet>
    <servlet-name>hello</servlet-name>
    <servlet-class>io.beaniejoy.HelloServlet</servlet-class>
  </servlet>
  <servlet-mapping>
    <serlvet-name>hello</servlet-name>
    <url-pattern>/hello</url-pattern>
  </servlet-mapping>
  
</web-app>
```