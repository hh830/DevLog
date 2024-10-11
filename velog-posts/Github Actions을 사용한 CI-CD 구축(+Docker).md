<p>현재 EC2와 RDS만 연결되어 있는 상태이다.
CI/CD가 구축되지 않아 매번 jar 파일로 배포해야 했기 때문에 CI/CD 툴인 github actions을 사용하여 서버 배포 자동화를 하였다.</p>
<h2 id="cicontinuous-integration">CI(Continuous Integration)</h2>
<p>지속적 통합을 의미하며, 코드를 push할 때마다 자동으로 빌드 및 테스트를 실행</p>
<h2 id="cdcontinuous-deployment">CD(Continuous Deployment)</h2>
<p>지속적 배포를 의미하며, 통합된 코드를 자동으로 프로덕션 환경에 배포</p>
<hr />
<h2 id="1-도커-레포지토리를-만든다">1. 도커 레포지토리를 만든다.</h2>
<p>{username}/{repository name}으로 접근하게 됩니다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/53fc5320-c626-4a19-8e0d-214f4b2c80e5/image.png" /></p>
<h2 id="2-루트-디렉토리에-dockerfile과-docker-composeyml을-만든다">2. 루트 디렉토리에 Dockerfile과 docker-compose.yml을 만든다.</h2>
<h3 id="dockerfile">Dockerfile</h3>
<pre><code class="language-yml"># base image
FROM openjdk:17-jdk-alpine

# copy jar file
COPY ./build/libs/{내 jar 파일명}.jar /app.jar

# run the app
ENTRYPOINT [&quot;java&quot;, &quot;-Dspring.profiles.active=prod&quot;, &quot;-jar&quot;, &quot;/app.jar&quot;]</code></pre>
<h3 id="docker-composeyml">docker-compose.yml</h3>
<pre><code class="language-yml">version: '3'

services:
  app:
    build:                           # 이미지를 빌드하기 위한 설정
      context: .                     # Dockerfile이 위치한 디렉토리 (현재 디렉토리)
      dockerfile: Dockerfile          # 사용할 Dockerfile (이름이 Dockerfile일 경우 생략 가능)
    ports:
      - &quot;8080:8080&quot;                  # 호스트:컨테이너 포트 매핑
    environment:                     # 환경 변수 설정 (필요에 따라 추가 가능)
      - SPRING_PROFILES_ACTIVE=prod   # 예: Spring Boot에서 prod 프로파일 활성화</code></pre>
<h2 id="3-github-actions-시-실행시킬-yml-파일을-만든다">3. Github Actions 시 실행시킬 yml 파일을 만든다.</h2>
<p>.github &gt; workflows &gt; yml 파일 생성</p>
<h3 id="yml-파일">yml 파일</h3>
<pre><code class="language-yml">name: Spring Boot Application to EC2

# 워크플로우가 실행될 트리거 조건을 설정
on:
  # main 브랜치로 코드가 푸시될 때 실행
  push:
    branches:
      - main
  # main 브랜치로 PR이 병합될 때 (pull request가 closed 될 때) 실행
  pull_request:
    branches:
      - main
    types:
      - closed

# 워크플로우에 포함된 작업 (build 작업과 deploy 작업)
jobs:
  build:
    # 빌드를 실행할 환경은 최신 Ubuntu로 설정
    runs-on: ubuntu-latest

    steps:
      # 1. 코드 체크아웃 (현재 레포지토리의 코드를 가져옴)
      - name: Checkout code
        uses: actions/checkout@v2

      # 2. Java 17 환경 설정 (JDK 17 설치)
      - name: Set up JDK 17
        uses: actions/setup-java@v2
        with:
          java-version: '17'
          distribution: 'adopt' # 사용할 JDK 배포판

      # 3. gradlew 실행 권한 설정
      - name: Grant execute permission for Gradle wrapper
        run: chmod +x ./gradlew

      # 4. Gradle을 사용하여 프로젝트를 빌드 (테스트는 제외 - `-x test` 옵션 사용)
      # 테스트를 실행하지 않으려면 -x test 사용, 테스트 포함 시 이 옵션 제거 가능
      - name: Build with Gradle
        run: ./gradlew build -x test

      # 5. Docker Hub에 로그인 (GitHub Secrets에 저장된 Docker Hub 사용자명과 비밀번호를 사용)
      - name: Login to Docker Hub
        run: echo &quot;${{ secrets.DOCKER_HUB_PASSWORD }}&quot; | docker login -u &quot;${{ secrets.DOCKER_HUB_USERNAME }}&quot; --password-stdin

      # 6. Docker 이미지 빌드 (Docker Hub 사용자명과 함께 'dcrw:latest' 태그로 이미지 생성)
      - name: Build Docker image
        run: docker build -t ${{ secrets.DOCKER_HUB_USERNAME }}/dcrw:latest .

      # 7. 빌드된 Docker 이미지를 Docker Hub에 푸시 (푸시할 때 'latest' 태그로 지정)
      - name: Push Docker image to Docker Hub
        run: docker push ${{ secrets.DOCKER_HUB_USERNAME }}/dcrw:latest

  deploy:
    # 배포 작업 역시 최신 Ubuntu 환경에서 실행
    runs-on: ubuntu-latest
    # deploy 작업은 build 작업이 완료된 후에만 실행되도록 설정
    needs: build

    steps:
      # 1. EC2 서버에 SSH를 통해 접속 (Secrets에 저장된 IP 주소, 사용자명, SSH 키 사용)
      - name: Deploy to EC2
        uses: appleboy/ssh-action@v0.1.3
        with:
          host: ${{ secrets.EC2_IP }}     # EC2 인스턴스의 IP 주소
          username: ${{ secrets.EC2_USER }} # EC2 인스턴스의 SSH 사용자명 (보통 'ec2-user')
          key: ${{ secrets.EC2_KEY }}     # GitHub Secrets에 저장된 EC2 인스턴스의 SSH 키
          port: 22                       # EC2 SSH 연결 포트 (기본값: 22)
          debug: true
          script: |                      # EC2에 접속 후 실행할 스크립트
            # 2. 최신 Docker 이미지를 Docker Hub에서 EC2 서버로 가져옴
            docker pull ${{ secrets.DOCKER_HUB_USERNAME }}/dcrw:latest
            # 3. 기존에 실행 중인 Docker 컨테이너 중지 (없으면 에러를 피하기 위해 || true 사용)
            docker stop dcrw || true
            # 4. 기존 컨테이너 삭제 (없으면 에러를 피하기 위해 || true 사용)
            docker rm dcrw || true
            # 5. 새로운 Docker 컨테이너 실행 (80 포트에서 외부로 접근 가능, 컨테이너 내부에서는 8080 포트를 사용)
            docker run -d --name dcrw \
              -p 80:8080 \
              -e OPENAI_API_KEY=${{ secrets.OPENAI_API_KEY }} \
              -e DB_URL=${{ secrets.DB_URL }} \
              -e DB_USERNAME=${{ secrets.DB_USERNAME }} \
              -e DB_PASSWORD=${{ secrets.DB_PASSWORD }} \
              -e S3_ACCESS_KEY=${{ secrets.S3_ACCESS_KEY }} \
              -e S3_SECRET_KEY=${{ secrets.S3_SECRET_KEY }} \
              -e S3_BUCKET=${{ secrets.S3_BUCKET }} \
              ${{ secrets.DOCKER_HUB_USERNAME }}/dcrw:latest
</code></pre>
<h3 id="secrets-환경-변수">secrets 환경 변수</h3>
<p>위 yml에서 ${{ secrets.~~ }} 로 적혀있는 부분은 github secrets에서 관리할 수 있게 한다.
아래 사진처럼 Docker, EC2 KEY 등 민감한 정보는 secrets에 등록시킨다.
-e 부분은 properties에서 환경 변수를 쓰기 위해 secrets을 불러오는 것
<img alt="" src="https://velog.velcdn.com/images/jhh0830/post/671266dd-a604-4658-8fbc-34ca2c9b8d32/image.png" /></p>
<h2 id="4-ec2에-docker와-docker-compose를-설치한다">4. EC2에 Docker와 Docker-compose를 설치한다.</h2>
<h3 id="docker">Docker</h3>
<blockquote>
<p>sudo apt-get update
sudo apt-get install docker.io
sudo service docker start</p>
</blockquote>
<h3 id="docker-compose">docker-compose</h3>
<blockquote>
<p>sudo curl -L &quot;<a href="https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$">https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$</a>(uname -s)-$(uname -m)&quot; -o /usr/local/bin/docker-compose
 sudo chmod +x /usr/local/bin/docker-compose</p>
</blockquote>
<h2 id="5-키-기반-인증을-사용하기-위해-키-생성">5. 키 기반 인증을 사용하기 위해 키 생성</h2>
<p>로컬에서 비밀키, ec2에 공개키가 저장되어 있어야 한다.
나는 기존에 pem으로 사용해서 인증을 했기 때문에 따로 공개키, 비밀키를 발급 받았다.</p>
<p>그래서 로컬(윈도우)에서 cmd에 아래를 쳐서 공개키, 비밀키를 만들어 준다.</p>
<blockquote>
<p>ssh-keygen -t rsa -b 4096 -C &quot;<a href="mailto:your_email@example.com">your_email@example.com</a>&quot;</p>
</blockquote>
<p>생성 경로는 보통 &quot;C:\Users\사용자.ssh&quot; 경로의 id_rsa.pub(공개키)과 id_rsa(비밀키)를 확인하면 된다.</p>
<p>로컬의 id_rsa는 github secrets에, id_rsa.pub는 EC2의 authorized_keys 파일에 저장한다.</p>
<p>EC2에 접속한 후 public key를 authorized_keys 파일에 추가한다.</p>
<blockquote>
<p>echo &quot;your-public-key-content&quot; &gt;&gt; ~/.ssh/authorized_keys</p>
</blockquote>
<hr />
<h2 id="에러">에러</h2>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/c82cb53f-c7e6-482c-afb1-b11b12f74eef/image.png" /></p>
<blockquote>
<p>ssh: handshake failed: ssh: unable to authenticate, attempted methods [none publickey], no supported methods remain</p>
</blockquote>
<p>인증이 되지 않아 EC2 Develop에 오류가 생겼다.
키가 맞지 않는 문제인가 해서 하나하나 키를 다시 적고, secrets에도 다시 적어줬는데도 해결되지 않았다.</p>
<h3 id="ec2-서버의-ssh-설정-파일-확인">EC2 서버의 SSH 설정 파일 확인</h3>
<blockquote>
<p>sudo nano /etc/ssh/sshd_config</p>
</blockquote>
<p>위를 실행시키면 SSH 설정 파일이 나오고 수정할 수 있게 된다.
아래 설정이 맞게 되어 있나 확인하고, 아니라면 수정해주면 된다.</p>
<blockquote>
<p>PasswordAuthentication no  # 비밀번호 인증 비활성화
PubkeyAuthentication yes  # 키 기반 인증 활성화
PubkeyAcceptedKeyTypes=+ssh-rsa</p>
</blockquote>
<h3 id="ssh-재시작">SSH 재시작</h3>
<blockquote>
<p>sudo systemctl restart ssh</p>
</blockquote>
<p>이렇게 맞게 설정했으면 성공이다!
SSH 설정 파일에서 주의할 것은...
거의 모든 줄에 #이 붙어있어서 주석일 것이라고 생각하지 못했었다.
그래서 그냥 뒤에 no, yes만 수정했다가 계속 안되서 고생했었다 ㅠ
SSH 설정 파일에서 #은 주석이니 위 설정은 주석 해제까지 해야 한다.</p>
<hr />
<h2 id="참고자료">참고자료</h2>
<p><a href="https://velog.io/@tilsong/%EC%BD%94%EB%93%9C-Push%EB%A1%9C-%EB%B0%B0%ED%8F%AC%EA%B9%8C%EC%A7%80-Github-Actions-Docker">https://velog.io/@tilsong/%EC%BD%94%EB%93%9C-Push%EB%A1%9C-%EB%B0%B0%ED%8F%AC%EA%B9%8C%EC%A7%80-Github-Actions-Docker</a></p>
<p><a href="https://sjh9708.tistory.com/237">https://sjh9708.tistory.com/237</a></p>