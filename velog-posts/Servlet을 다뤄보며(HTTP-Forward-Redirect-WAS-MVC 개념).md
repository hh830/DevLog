<h2 id="1-stateless">1. stateless</h2>
<ul>
<li>server는 client를 기억하지 못한다.</li>
<li>client를 기억하기 위한 다양한 기술</li>
<li>cookie, session, jwt</li>
</ul>
<hr />
<h2 id="2-request">2. Request</h2>
<ul>
<li>GET, POST, PUT, DELETE</li>
</ul>
<hr />
<h2 id="3-response">3. Response</h2>
<ul>
<li>정상적인 request<ul>
<li>200</li>
</ul>
</li>
<li>비정상적인 request<ul>
<li>404: 존재하지 않은 URL 요청</li>
<li>401, 403: 허가되지 않은 요청</li>
</ul>
</li>
</ul>
<hr />
<h2 id="4-forward--redirect">4. forward / redirect</h2>
<p>서버에서 처리하는 방식</p>
<h3 id="forward">forward</h3>
<p>브라우저로 요청을 전달하는 것이 아니라 서버 내의 다른 리소스를 호출하는 방식</p>
<ul>
<li>요청 정보를 WAS 내 다른 리소스로 전달함</li>
<li>요청 정보를 함께 전달</li>
<li>페이지 이동 없이 WAS 내부에서 다른 리소스를 호출하여 Redirect보다 빠름</li>
<li>최초 요청한 URL이 변경되지 않음
<img alt="" src="https://velog.velcdn.com/images/jhh0830/post/88f661b3-a056-42ca-9e16-536f60a021bd/image.png" /></li>
</ul>
<h3 id="redirect">redirect</h3>
<p>요청을 브라우저로 전달하여 새로 요청하게 만들어 두번씩 통신이 일어남</p>
<ul>
<li>요청을 Web Browser에게 전달하여 새로 요청하게 만듬</li>
<li>WAS 서버와 Web Browser 간 두번 통신이 일어나 forward보다 속도가 느림</li>
<li>새로운 URL로 요청하여 request 정보가 기본적으로 전달되지 않음</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/82fd6306-72d8-47d9-bf92-542313731d2b/image.png" /></p>
<p><a href="https://dev-handbook.tistory.com/35">https://dev-handbook.tistory.com/35</a></p>
<hr />
<h2 id="5-tcpip">5. TCP/IP</h2>
<p>1.0 -&gt; 1.1
TCP/IP 위에 Application Protocol이 HTTP인데 1.0(TCP/IP까지 disconnect)</p>
<p>1.1은 HTTP만 disconnect 되고 TCP/IP 연결 유지</p>
<hr />
<h2 id="6-static-vs-dynamic-content">6. static vs dynamic content</h2>
<h3 id="static-content">static content</h3>
<p>image, html, css, js 파일 등 파일로 존재</p>
<h3 id="dynamic-content">dynamic content</h3>
<p>동적으로 만들어지는 content
ooo님 안녕하세요~ -&gt; 사용자에 따라 동적으로 만들어야 함</p>
<hr />
<h2 id="web-server-vs-web-application-serverwas">Web Server vs Web Application Server(WAS)</h2>
<h3 id="web-server">Web Server</h3>
<ul>
<li>Apache</li>
<li>static content를 관리
  (ex. 이미지 등 정적 컨텐츠를 가지고 있다가 관련 클라이언트로부터 관련 요청이 들어오면 파일 I/O를 해서 바로 보냄)</li>
<li>dynamic content가 필요하다면 WAS에 요청해 응답 받고 클라이언트로 전달</li>
</ul>
<h3 id="web-application-server">Web Application Server</h3>
<ul>
<li>Tomcat, Resin, Web Logic, Jeus </li>
<li>dynamic content를 관리</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/4c4b0ccc-5325-4b52-928e-bba9835e4ae4/image.png" /></p>
<hr />
<h2 id="post-기본-request-body">post 기본 request body</h2>
<p>x-www-form-urlencoded
-&gt; form-data는 따로 설정해줘야함</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/82a81940-71d2-49fa-b4e9-b9bbde2596a9/image.png" /></p>
<hr />
<h2 id="mvc">MVC</h2>
<p>servlet이 controller 역할(request 정리)
java - database가 model 역할
jsp가 view 역할
-&gt; forward 방식 사용됨</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/6e1707d0-af27-4e27-89f7-9459e73b0c41/image.png" /></p>