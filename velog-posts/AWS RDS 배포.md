<h2 id="rds-인스턴스-생성">RDS 인스턴스 생성</h2>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/fc9c8062-da34-4139-a03c-28e7f735ba20/image.png" />
표준 생성, MySQL, 프리티어 등을 설정한다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/6d3786a2-35b1-4c18-8ce7-8a479f79a417/image.png" /></p>
<p>DB 인스턴스 식별자, 사용자 이름, 암호도 설정한다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/708f8873-22c0-436c-9e97-8c745fbe0279/image.png" />
자동으로 된거 선택
free tier에서 가능한 것을 선택했다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/8c698897-b4b5-4bbd-8671-c525228aba91/image.png" />
마찬가지로 free tier는 최대 20GiB 가능하다 해서 free tier 가능한 것을 선택했다.
과금 할 생각 없으니 <code>&lt;스토리지 자동 조정 활성화&gt;</code> 체크 해제</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/84c31692-896e-4063-ae1e-a6f393806287/image.png" />
이때 RDS는 free tier 관계 없이 Public IP를 발급 받으면 과금이 된다고 한다.
그래서 EC2로 터널링 해서 사용할 생각으로 EC2 컴퓨팅 리소스에 연결을 선택했다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/8a4b65bc-b5da-4de7-b973-d69c596bda49/image.png" />
<img alt="" src="https://velog.velcdn.com/images/jhh0830/post/132b7e69-f3e3-4fc1-878f-e4d0a77f36a3/image.png" />
나머지 모니터링, 백업, 암호화, 마이너 버전 자동 업그레이드 사용 등은 혹시 모를 과금을 대비해 체크 해제 했다.</p>
<hr />
<h2 id="intellij에서-연결">IntelliJ에서 연결</h2>
<p>인텔리제이에서 데이터베이스를 연결하는 방법
<img alt="" src="https://velog.velcdn.com/images/jhh0830/post/dfcfecde-9048-4456-a42a-58f8816c643b/image.png" />
위 사진 순서대로 SSH/SSL에 들어가서 1. Use SSH tunnel을 클릭하고, 2. ...을 클릭한다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/b9149f22-ba3b-4a00-b93f-ed8383eac930/image.png" />
그러면 위 화면이 나오는데 왼쪽 상단의 + 버튼을 누르고 체크 표시들을 적는다.</p>
<p>EC2에 대한 정보들을 적으면 된다.</p>
<p>Host에는 ec2 publicIP를 적으면 된다. 
예를 들면 <a href="mailto:ubuntu@0.0.0.0">ubuntu@0.0.0.0</a>:22 이런 식.
username에는 사용자 이름(ubuntu)
Authentication Type에는 원래 password로 되어있지만 Key Pair 방식으로 사용할 것이기 때문에 바꿨다.
private key file을 선택해준다.
Passphrase는 설정 안해줘도 된다.(잘못체크함)</p>
<p>그 후 Test Connection 버튼을 눌러 연결 테스트를 했을 때 성공이 나오면 성공!
성공했다면 OK 버튼을 누른다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/28d6d5a2-8811-4658-8147-caee3b7633f5/image.png" />
General에 와서 상단 Name, Host, User, Password를 적는다.
RDS에 관한 정보들을 적으면 된다.</p>
<p>Host는 RDS 엔드포인트를 적고, User에는 RDS username, password에는 RDS password를 적으면 된다.
하단 URL에 잘 반영되나 확인하고 OK 누르면 된다.
Database 탭에 잘 뜨면 성공~</p>
<hr />
<h2 id="applicationproperties">Application.properties</h2>
<pre><code class="language-properties">spring.datasource.url=jdbc:mysql://&lt;RDS_ENDPOINT&gt;:3306/&lt;DB_NAME&gt;
spring.datasource.username=${DB_USERNAME}
spring.datasource.password=${DB_PASSWORD}</code></pre>
<p>Spring boot에서는 properties에 이런식으로 적어서 DB를 연결하면 된다.</p>
<hr />
<h3 id="참고자료">참고자료</h3>
<p><a href="https://jih3508.tistory.com/167">https://jih3508.tistory.com/167</a>
<a href="https://dgjinsu.tistory.com/66">https://dgjinsu.tistory.com/66</a>
<a href="https://labs.inchan.dev/posts/202403051335/">https://labs.inchan.dev/posts/202403051335/</a></p>