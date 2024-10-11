<h2 id="ec2-인스턴스-생성">EC2 인스턴스 생성</h2>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/dcd2b275-fcc5-4a12-90b9-eeaa3079db6d/image.png" />
인스턴스 이름, OS는 Ubuntu를 선택했다.
프리티어를 사용할 것이기 때문에 프리티어 사용 가능 위주로 선택해준다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/d69969af-d989-4b83-b2ff-1a8bd329fdd5/image.png" />
인스턴스 유형 역시 프리티어에서 사용 가능한 t2.micro를 선택했다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/ef69b1f9-d8ad-4164-8498-83a831e52b0f/image.png" />
새 키 페어 생성을 누른다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/8693c8a2-ac82-472a-9fcc-a112b56706bb/image.png" />
그럼 이 창이 뜨는데, 키 페어 이름과 유형, 파일 형식을 지정해주고 키 페어 생성을 누른다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/754d159c-acdc-4470-a84c-d206f4eddab0/image.png" />
그러면 이렇게 키 페어가 다운로드 된다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/39526b54-dec8-4c0a-8ef5-bcd0820797fc/image.png" />
이 키 페어 파일로 서버에 접속하므로 기억할 만한 경로로 옮겨놓는 것을 추천한다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/a0fa8dd0-25e3-4fd9-ba12-84988fdc6bd2/image.png" />
보안 그룹은 해당 포트에 접속할 수 있는 IP를 지정해 줄 수 있다.
이 당시 카페에 있었어서 SSH 트래픽 허용 부분은 위치 무관으로 설정했다.
이후 데스크탑으로 작업하는 경우가 많아 내 IP로 설정하였다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/0006030a-f30d-40fc-b007-81e0723a5520/image.png" />
스토리지는 free tier가 사용할 수 있는 최대 30GB gp3를 선택하였다.
<img alt="" src="https://velog.velcdn.com/images/jhh0830/post/7d53ad77-e0a8-4cda-b426-012203899c2b/image.png" />
우측에 이렇게 뜨는데 확인하고 인스턴스 시작을 누르면 된다.</p>
<hr />
<h2 id="보안-그룹">보안 그룹</h2>
<p>각 포트에 접근할 수 있는 IP를 지정해주기 위해 보안 그룹을 생성해야 한다.
참고로 이 페이지에서 한글 쓰면 안된다.(설명도 영어로)</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/4da67959-5d62-4e5f-8465-af76eea60ed7/image.png" />
마찬가지로 이 당시 카페에 있었으므로 모두 위치 무관인 0.0.0.0/0으로 설정했다.
이 역시 집에 와서 바꿈</p>
<p>보안 그룹을 생성한 후 이 보안 그룹을 인스턴스에 연결을 해줘야 한다.
<img alt="" src="https://velog.velcdn.com/images/jhh0830/post/23118ce4-2f73-49ac-b019-6b2a8be55f3e/image.png" /></p>
<p>작업 &gt; 보안 &gt; 보안그룹 변경</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/fe08e93a-f983-4974-b988-1b361297ec91/image.png" />
위에서 보안 그룹 변경하기</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/9c365f03-9852-40fb-ba6e-4e25aaa1bb25/image.png" />
연결 누르면 보안 그룹이 변경된다.</p>
<hr />
<h2 id="탄력적-ip">탄력적 IP</h2>
<p>서버가 다운될 때 public ip는 ip가 변하게 되는데 이를 해결하는 게 탄력적 IP이다.
그래서 탄력적 IP를 발급받으려고 했는데...
올해 초부터인가 free tier 상관 없이 과금이 된다고 한다...</p>
<p>그래서 생성하고 바로 삭제...</p>
<h3 id="생성">생성</h3>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/b2becb7e-bbda-4957-af1e-812849a8bc35/image.png" />
EC2 왼쪽 탭에 탄력적 IP 탭이 있다. 
들어가면 위 화면이 뜬다.
할당 버튼을 누르면 된다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/58b4e8bf-f295-4ceb-aa3f-aec04d0aee1d/image.png" />
이후 인스턴스와 탄력적 IP를 연결시키면 되는데
여기서 인스턴스를 선택하고 연결을 누르면 된다.
(간단)</p>
<h3 id="삭제하는-법">삭제하는 법</h3>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/921ff479-60a0-46ad-923c-49d067b01e35/image.png" />
만들자마자 삭제하게 됐다.
탄력적 IP 주소에 들어가서 작업 &gt; 탄력적 IP 릴리즈 누르면 삭제 된다.</p>
<p>원래 free tier라도 탄력적 IP를 인스턴스에 연결하기만 하면 과금이 안됐는데 슬프다 ㅠ</p>
<hr />
<h2 id="window에서-인스턴스-연결">Window에서 인스턴스 연결</h2>
<p>window에서는 chmod라는 명령어가 없다.
그래서 key 파일의 권한을 설정해 주어야 하는데
직접 파일을 찾아 속성 &gt; 보안 &gt; 고급을 눌러 설정해주면 된다고 한다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/ddeb5f99-418d-43ce-8cb0-cde71029a8ff/image.png" />
sshKey.pem 우클릭 &gt; 속성 &gt; 보안 &gt; 고급</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/9893cb7e-bf10-4ef2-a88a-abb997dcebf9/image.png" />
상속 사용 안함으로 권한을 모두 해제시켜준다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/c1f84b5e-e4b0-4ae0-ae20-df0c1b8925fb/image.png" />
그 후 뜨는 창에 윈도우 마이크로소프트 계정이라 메일 주소 적고 읽기 및 실행과 읽기 권한 부여</p>
<p>모두 확인 누르면 끝</p>
<p>그 후 cmd에 ssh -i 키 파일 사용자이름@publicIP 실행시켜서 우분투에 접속 하면 성공이다.</p>
<hr />
<h2 id="github-코드-clone으로-가져오기">Github 코드 Clone으로 가져오기</h2>
<ol>
<li><p>EC2에 git 설치</p>
<blockquote>
<p>sudo apt-get install git
git --version # 버전 확인</p>
</blockquote>
</li>
<li><p>github에서 ssh key 생성</p>
<blockquote>
<p>cd ~/.ssh
ssh-keygen -t rsa -C github계정 메일</p>
</blockquote>
</li>
</ol>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/9b0f146e-227a-4319-848b-147ebf4a82ad/image.png" /></p>
<p>이러면 키 페어를 생성하게 된다.
그 후 id_rsa.pub 파일 내용을 github에 등록해야 한다.</p>
<ol start="3">
<li><p>id_rsa.pub 내용 보기</p>
<blockquote>
<p>cat id_rsa.pub</p>
</blockquote>
</li>
<li><p>github &gt; setting &gt; SSH and GPG keys &gt; new SSH key 버튼을 클릭
<img alt="" src="https://velog.velcdn.com/images/jhh0830/post/73dce89c-07e1-4642-91d8-eb6b86ff2ac3/image.png" /></p>
</li>
</ol>
<ol start="5">
<li>그 후 git clone을 하면 된다.<blockquote>
<p>git clone 레포 주소</p>
</blockquote>
</li>
</ol>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/87ee8004-42cd-415e-be77-3d74c4b1aef5/image.png" /></p>
<p>하지만 properties가 없음
(민감한 정보를 다루기 때문에 git에서 관리하지 않게 하였는데, github actions을 사용하면서 github secrets으로 민감한 정보를 빼내고 git에 포함시켰다.)</p>
<p>그래서 지금은 수동으로 추가해줘야 함.
resources 폴더로 이동한 다음 properties 추가하는 방식.
하지만 나는 resources 폴더도 없어서 mkdir로 만들어줌.</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/f43b6c17-2463-4e57-abf3-b7e13a3e53ba/image.png" /></p>
<ol start="6">
<li>만든 후 다시 레포지토리 부모 폴더로 이동해서 자바를 설치한다.
spring boot에서 자바 17을 사용했기 때문에 17을 설치해줬다.</li>
</ol>
<blockquote>
<p>sudo apt install openjdk-17-jdk</p>
</blockquote>
<ol start="7">
<li>그 다음 ./gradlew build를 하면 되는데...<blockquote>
<p>./gradlew build</p>
</blockquote>
</li>
</ol>
<p>gradlew 파일이 git에 없었다... ignore로 설정했나보다.</p>
<p>ignore에 gradle-wrapper 관련 파일들과 gradlew 관련 파일들이 설정되어 있었다.
properties ignore 시키면서 개인 정보 같은 것들 넣어놨더니 배포할 때 없어서 문제가 생겼다.</p>
<p><strong>* 꼭 gradle 관련된 것은 git에 올리도록 하자....... *</strong></p>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/7c7b76df-2c98-4b7a-954b-6b43d3f6f231/image.png" />
왜... 0%인거야...</p>
<p>너무 진행이 안되서 그냥 cmd 닫고 다시 하려고 했지만...
cmd ssh 연결 응답 자체가 안와서 인스턴스를 확인해보니 cpu 100%를 찍었다.
그래서 인스턴스 재부팅 하고, settings.gradle이 없길래 settings.gradle을 추가해줬다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/771a0049-6d3b-4013-bd37-e078871d10f5/image.png" /></p>
<blockquote>
<h3 id="settingsgradle">settings.gradle</h3>
</blockquote>
<pre><code class="language-java">pluginManagement {
    repositories {
        gradlePluginPortal()
        mavenCentral()
    }
}</code></pre>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/d80b0383-6bca-4a8b-ac59-292bfbabe1ae/image.png" /></p>
<p>하지만 또 test에서 멈춰버린...
어차피 test 코드는 git에 안올리기도 했고 test는 제외시켰다.</p>
<blockquote>
<p>./gradlew build -x test</p>
</blockquote>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/18886b07-c3c6-49ec-a551-f5c8dbabb665/image.png" />
휴 드디어 성공</p>
<p>그 다음 cd build &gt; cd libs로 폴더 이동하고 ls로 jar 파일이 들어와 있나 확인해준다.
해당 jar 파일의 이름을 통해 nohup으로 백그라운드에서 실행하도록 하여 무중단 서비스를 할 수 있게 만들어준다.</p>
<p>띄어쓰기 주의</p>
<blockquote>
<p>nohup java -jar jar파일명 &amp;</p>
</blockquote>
<p>무중단 배포를 하면 EC2 배포는 끝이다~
(아래는 spring boot 문제)</p>
<hr />
<h2 id="csrf">CSRF</h2>
<p>하지만 CSRF 문제가 나타났다.
CSRF 활성화(기본값)를 했지만 관련 토큰을 보내고 있지 않은 상황.
테스트 할 때는 비활성화 해놨었지만 배포할 때 활성화로 바꿔놓고 토큰 보내주는 코드를 빠뜨렸다.</p>
<h3 id="csrf-공격">CSRF 공격</h3>
<p>인증된 사용자의 세션을 악용하여 악의적인 요청을 보낼 수 있는 공격으로, 웹 브라우저에서 사용자가 로그인 된 상태에서 다른 사이트에서 해당 사용자의 세션을 이용해 요청을 보내는 것이다. </p>
<p>우리는 세션 기반 인증을 사용하기 때문에 CSRF 보안을 활성화 해야 한다.
CSRF 토큰을 XSRF-TOKEN 쿠키에 담아 클라이언트에 보내고, 클라이언트에서는 X-CSRF-TOKEN 헤더에 포함시키면 된다.</p>
<p>하지만 로그인, 회원가입에서는 인증이 되지 않은 상태이니 비활성화 설정을 했다.(웬만하면 활성화 하는 게 좋은 듯)</p>
<pre><code class="language-java">http
    .csrf(csrf -&gt; csrf
        .csrfTokenRepository(CookieCsrfTokenRepository.withHttpOnlyFalse()) // CSRF 토큰을 쿠키로 전달
        .ignoringRequestMatchers(&quot;/login&quot;, &quot;/signup&quot;)  // CSRF 보호 비활성화
    )
    .authorizeHttpRequests(auth -&gt; auth
        .requestMatchers(&quot;/login&quot;, &quot;/signup&quot;).permitAll() //  인증 없이 접근 허용
        .anyRequest().authenticated()              // 그 외의 요청은 인증 필요
    );
</code></pre>
<hr />
<h3 id="참고자료">참고자료</h3>
<p><a href="https://velog.io/@jonghyun3668/SpringBoot-%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8-EC2-%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0">https://velog.io/@jonghyun3668/SpringBoot-%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8-EC2-%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0</a>
<a href="https://wordbe.tistory.com/318">https://wordbe.tistory.com/318</a>
<a href="https://benfatto.tistory.com/54">https://benfatto.tistory.com/54</a></p>