<p><a href="https://velog.io/@getbrave/Intellij-Tomcat">https://velog.io/@getbrave/Intellij-Tomcat</a></p>
<p>위의 글을 보고 인텔리제이와 톰캣 연결을 진행하였다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/e29b95d5-8cca-4163-92f3-f003f1ab44c2/image.png" />
Jakarta EE로 프로젝트를 만들고 Web application, Tomcat 서버를 설정해준다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/25cd32bd-3bd0-4215-8b7d-c337d4d979db/image.png" />
Run &gt; Edit Configurations를 눌러서 tomcat local을 설정해준다.</p>
<h2 id="문제">문제</h2>
<p>Tomcat을 실행시켰을 때 404 에러가 뜬다.</p>
<p>다른 분들과 비교를 해보니 아래 Deployment 부분에서 자동으로 war 같은게 떠야 하는데 안떴다.
지금 있는건 그냥 + 눌러서 톰캣 폴더 추가한 것(잘못됨)</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/4f07e925-5ada-43a3-b610-a8ec11c8e625/image.png" /></p>
<blockquote>
<h3 id="deployment-화면-비교">Deployment 화면 비교</h3>
</blockquote>
<ul>
<li>현재 (문제있음)
<img alt="" src="https://velog.velcdn.com/images/jhh0830/post/fd3af784-5fe0-4c99-a328-ae27b11298ef/image.png" /><br /></li>
<li>정상
<img alt="" src="https://velog.velcdn.com/images/jhh0830/post/609fe7f1-39b4-4bbc-8e0e-685d38b0628e/image.png" /></li>
</ul>
<h3 id="에러-메세지">에러 메세지</h3>
<blockquote>
<p>Maven resources compiler: Maven project configuration required for module 'demo2' isn't available. Compilation of Maven projects is supported only if external build is started from an IDE.</p>
</blockquote>
<hr />
<h2 id="해결방법">해결방법</h2>
<p>Load maven project를 하지 않았기 때문.......
프로젝트를 생성하고 바로 오른쪽 하단에 나오는 Load maven project를 눌렀어야 됐다. 
skip 버튼도 같이 나오는데 이걸 누르기 전까지는 실행 버튼이 활성화 되지 않기 때문에 그냥 skip 버튼을 눌렀던 것 같다.</p>
<p>maven을 load하면 위와 같은 문제점들이 해결이 되고,
tomcat 서버 실행 &gt; webapp 폴더의 index.jsp를 실행시키면 tomcat 서버가 작동하는 것을 볼 수 있다.</p>
<h3 id="혹시-그래도-안된다면">혹시 그래도 안된다면!</h3>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/603eb339-c33d-4463-8843-d013c842b558/image.png" />
여기서 exploded인가 war이 두개가 뜨면 war를 선택하는게 더 좋다고 한다...!</p>
<h3 id="추가로">추가로</h3>
<p>8080 포트가 사용 중인지 확인하고 포트를 죽이고 해보는 방법도 있다.</p>
<blockquote>
<p>netstat -ano | findstr : 8080
-&gt; 8080 사용 중인 PID가 나온다.
taskkill /PID [PID번호] /F
-&gt; 8080 사용 중인 PID 죽이기</p>
</blockquote>