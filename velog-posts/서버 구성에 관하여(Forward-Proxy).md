<h1 id="시작하며">시작하며,</h1>
<p>내가 경험해 본 다른 프로젝트과는 다르게 이번에는 pytorch를 사용한 딥러닝 모델을 사용하여 모델 서버가 추가로 필요했다. 그래서 딥러닝 모델을 서빙하기 위한 FastAPI 서버와 클라이언트로부터 요청을 받아 처리하는 Spring Boot 서버를 구성하고, Spring Boot에서 FastAPI로 요청을 보내 모델 예측 값을 받아오는 방식으로 구현했다.</p>
<p>개발하면서 문득 포워드 방식이 떠오르며 이게 어떤 방식의 서버 구성인지 궁금해졌다.</p>
<h2 id="forward">Forward</h2>
<p>클라이언트로부터 요청을 받아오면 서블릿이 다른 서블릿/jsp를 호출하여 클라이언트에 응답을 보내는 방식이다.
클라이언트의 요청 정보가 유지된다는 특징이 있다.
스프링에서는 Spring MVC가 이에 해당된다고 할 수 있다.
(Servlet: Controller / Jsp: View / Java에서 DB: Model)</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/f256a81c-047a-4e34-92ed-abbaecb5086b/image.png" /></p>
<p>처음에는 백엔드 쪽에서 클라이언트에 다시 응답을 보내지 않고 다른 url로 요청을 한다는 점에서 포워드 방식이라 생각했었다.</p>
<p>하지만 잘못 알고 있던게 있었다. 
<strong>* 포워드는 같은 서버 내 자원에 대한 개념*</strong> 이다.
스프링 MVC와 같은 같은 서버 내에서 다른 서블릿, jsp로 요청을 해야 하는 것이다.
따라서 위 프로젝트와는 달랐다.</p>
<p>그럼 위 프로젝트는 어떤 방식이라고 할 수 있을까?
서버 사이 중계를 한다는 점에서 Proxy에 관하여 찾아보았다.</p>
<h2 id="proxy">Proxy</h2>
<p>클라이언트가 자신을 통해서 다른 네트워크 서비스에 간접적으로 접속할 수 있게 해주는 시스템
서버 사이의 중계기 역할을 한다.</p>
<p>네트워크 상 어디에 위치하느냐, 어느 방향으로 데이터를 제공하느냐에 따라 Forward Proxy와 Reverse Proxy로 나뉜다.</p>
<h3 id="forward-proxy">Forward Proxy</h3>
<p>클라이언트 바로 뒤에 위치하며, 클라이언트가 서버에 접근하고자 할 때 클라이언트는 타겟 서버의 주소를 Forward Proxy에 전달해 포워드 프록시가 인터넷으로 요청된 내용을 가져오는 방식</p>
<ul>
<li>흔히 Proxy Server는 Forward Proxy Server를 의미</li>
<li>실제 서버와 직접 통신하지 않고 포워드 프록시를 통해 요청을 보냄(제한적인 인터넷 사용 등)</li>
<li>주로 내부 네트워크의 클라이언트가 외부 네트워크에 접근할 때, 캐싱으로 서버 부하 감소, 클라이언트 ip 암호화 목적으로 사용</li>
</ul>
<h3 id="reverse-proxy">Reverse Proxy</h3>
<p>웹 서버/WAS 앞에 놓여 있는 것</p>
<p><a href="https://inpa.tistory.com/entry/NETWORK-%F0%9F%93%A1-Reverse-Proxy-Forward-Proxy-%EC%A0%95%EC%9D%98-%EC%B0%A8%EC%9D%B4-%EC%A0%95%EB%A6%AC">https://inpa.tistory.com/entry/NETWORK-%F0%9F%93%A1-Reverse-Proxy-Forward-Proxy-%EC%A0%95%EC%9D%98-%EC%B0%A8%EC%9D%B4-%EC%A0%95%EB%A6%AC</a></p>
<p>Reverse Proxy인가보다... 생각을 했지만
찾아보면 볼수록 Reverse Proxy는 서버가 정말 중계 역할만 하는 것을 뜻하는 것 같았다.
하지만 우리 프로젝트는 Spring boot가 단순 중계 역할만 하는게 아니라 다른 API 요청도 받아 응답을 보낸다. 즉, 다른 비즈니스 로직이 있는 서버이다.</p>
<p>그래서 그냥 굳이 구조를 말한다면 API Gateway라고 한다고...
이 구조를 뜻하는 게 없는 것 같다.</p>
<p>알게 된다면 다시 추가 할 예정</p>
<hr />
<p>서버... 정말 까도까도 나오는 양파같은 존재 :(
그래도 내가 생각하는 부분들이 계속 문제가 있을 법한 부분에 대해 의문이 드는게 신기했다.
이론만 배우는 것보다는 내가 하는 프로젝트에 적용시키는게 더 이해도 잘 되고, 알아가는 과정도 재밌는 것 같다.😊</p>