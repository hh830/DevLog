<p>aws에 올라간 ip로 요청을 보내도 계속 403이 뜨고, 
spring boot에 로그가 뜨지 않아 이상하다고 생각하던 중 ec2 내 프로젝트 코드를 직접 보다가 이상함을 느꼈다.</p>
<p>github actions로 CI/CD 연결을 했고, push 후 성공적으로 build, develop 모두 성공했는데 ec2에 최신 코드 반영이 안되고 있었다.</p>
<p>그래서 docker images와 docker ps를 사용해 실행 중인 컨테이너 정보를 봤다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/5182761d-cc03-493c-8059-db77abae5230/image.png" /></p>
<p>2일 전에 생성 됐던 image가 실행되고 있었던 것.
이름이 달라서 그런가보다. 
Dockerfile과 github actions 설정이 달라서 그런듯.</p>
<p>그래서 github actions에 스크립트가 실행되고 있었는데 해당 이름을 찾지 못해 중지, 삭제, 최신 버전 반영을 못했나보다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/f6fa75bc-b056-4a86-b170-2fae463233cd/image.png" /></p>
<p>그래서 해당 컨테이너를 중지시키고 삭제하여 최신 버전으로 실행하도록 하였다.</p>
<blockquote>
<p>docker stop 컨테이너ID # 현재 실행 중인 컨테이너 중지
docker rm 컨테이너ID # 중지된 컨테이너 삭제
docker run -d --name {새로온 컨테이너 이름} -p 8080:8080 {실행할 도커 이미지}:latest # 최신 이미지로 새로운 컨테이너 실행</p>
</blockquote>
<hr />
<h2 id="에러">에러</h2>
<blockquote>
<p>docker: Error response from daemon: Conflict. The container name &quot;/dcrw&quot; is already in use by container &quot;27fef9514aa6e970504cae117a20a3466619396ced9f203e18c87ab28f6ff335&quot;. You have to remove (or rename) that container to be able to reuse that name.See 'docker run --help'.</p>
</blockquote>
<p>하지만 또 이런 에러가 떴는데 ...
이름 충돌 에러였다.
dcrw라는 이름의 컨테이너가 이미 존재해서 동일한 이름을 가진 새 컨테이너를 만들 수가 없다고.</p>
<p>그래서 docker ps -a를 통해 exit 된 컨테이너도 확인해 보니 exit 되어 있는 dcrw 컨테이너가 있었다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/9a33d5c0-c22e-41b2-acf0-f166de98ea28/image.png" /></p>
<p>이를 삭제하고 새로운 컨테이너를 실행하면 된다.</p>
<blockquote>
<p>docker rm {컨테이너 이름}
docker run -d --name {새로온 컨테이너 이름} -p 8080:8080 {실행할 도커 이미지}:latest # 최신 이미지로 새로운 컨테이너 실행</p>
</blockquote>
<hr />
<h3 id="도커-로그-보기spring-boot-로그-볼-수-있음">도커 로그 보기(spring boot 로그 볼 수 있음)</h3>
<blockquote>
<p>docker logs dcrw</p>
</blockquote>