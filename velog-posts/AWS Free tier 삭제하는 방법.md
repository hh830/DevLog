<p><a href="https://dev-jwblog.tistory.com/91">https://dev-jwblog.tistory.com/91</a></p>
<p>AWS free tier 종료가 다가와 위 링크를 보고 따라하며 인스턴스를 삭제하였다.</p>
<h2 id="1-ec2-인스턴스-중지-종료삭제">1. EC2 인스턴스 중지, 종료/삭제</h2>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/9dfa9e36-1de9-4567-b284-6f645c99e79e/image.png" />
EC2에 접속해 삭제하고자 하는 인스턴스를 체크 후 [인스턴스 상태] -&gt; [인스턴스 중지]를 누른다.
중지되었으면 다시 [인스턴스 상태] -&gt; [인스턴스 종료]를 누른다.</p>
<p>종료 후 몇 분이 지나야 삭제가 된다.</p>
<h2 id="2-탄력적-ip-삭제">2. 탄력적 IP 삭제</h2>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/b2ca2f79-0bd5-4b88-82ec-58a68d804067/image.png" />
왼쪽 탭에서 탄력적 IP 탭을 찾아 들어간다.
삭제하고 싶은 탄력적 IP 자체를 들어온 다음 [작업] -&gt; [탄력적 IP 주소 릴리스]를 누른다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/c9524bca-9ed5-43ae-98eb-3f1342394c26/image.png" />
<strong>체크 말고 IP 자체를 한번 클릭</strong>하고 들어와 릴리스를 하면 된다.</p>
<p><a href="https://velog.io/@jch9537/AWS-Elastic-IPEIP">https://velog.io/@jch9537/AWS-Elastic-IPEIP</a></p>
<p>만약 체크를 하면 아래와 같이 뜬다.
<img alt="" src="https://velog.velcdn.com/images/jhh0830/post/74529b16-e746-4659-bae6-38b8f0f9e7dd/image.png" />
릴리스 할 수 없다고 뜨고</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/8f1a0aae-47c2-40f1-8994-f01bb992624a/image.png" />
연결 해제도 할 수 없다고 떴다.</p>
<h2 id="3-보안-그룹-삭제">3. 보안 그룹 삭제</h2>
<p>왼쪽 탭에서 탄력적 IP 바로 위에 있는 보안 그룹을 클릭하여 들어온다.</p>
<p>원하는 보안 그룹을 체크 후 [작업] -&gt; [보안 그룹 삭제]를 누르면 된다.
RDS를 사용했다면 지금 단계에서는 할 수 없다고 뜬다.
** RDS 삭제 후 보안 그룹을 삭제해야 한다.**</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/274333a4-f0a6-499c-8a3b-fa4abe8956cf/image.png" /></p>
<p>나 역시 RDS를 사용중이라 보안 그룹을 지금 단계에서 삭제할 수 없었다.
보안 그룹과 네트워크 인터페이스와 연결되어 있어 삭제할 수 없다고 떠서 네트워크 인터페이스를 삭제해야 하는 것 같아 보이지만 RDS를 삭제하면 해결이 된다.</p>
<p>아래는 나의 시행착오들
<img alt="" src="https://velog.velcdn.com/images/jhh0830/post/2ea3a52b-7bba-4eb6-a4d1-f2ab1fec44fc/image.png" />
이것은 네트워크 인터페이스를 삭제하는 방법을 AWS 공식 홈페이지에서 캡쳐해온 것이다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/86cc38ec-28c8-4717-972a-1ac268b848f1/image.png" /></p>
<p>네트워크 인터페이스에 들어간 다음
위 캡쳐본 순서대로 실행하다가 3번을 실행하면 아래와 같이 뜬다.
<img alt="" src="https://velog.velcdn.com/images/jhh0830/post/7f01c36a-904e-4530-ae4e-b9bfb8ea76eb/image.png" /></p>
<p>대충 삭제할 수 없다는 뜻</p>
<h2 id="4-rds-삭제">4. RDS 삭제</h2>
<p>이후에는 그냥 RDS부터 삭제하였다.
RDS로 들어가 왼쪽 탭에서 [RDS] &gt; [데이터베이스]에 들어간다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/2fcbb1d6-eb77-4e2b-b785-f6f840702a28/image.png" /></p>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/fc14e6d5-e4a8-4f7f-a44a-85ad3278f237/image.png" />
그러면 위와 같이 실행 중인 데이터베이스가 뜬다.
삭제하고 싶은 데이터베이스를 선택하고 
[작업] &gt; [중지] &gt; [삭제]를 하면 된다고 한다.
하지만 나는 [일시적으로 중지] 밖에 없어서 그냥 삭제하였다.</p>
<p>이렇게 해도 성공적으로 삭제되는 것 같다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/e3f3c360-b890-43f6-96a5-d90d2c78707b/image.png" />
DB 인스턴스를 삭제할 때 위와 같이 뜬다.
나는 확실하게 더이상 사용하지 않을 것이기 때문에 스냅샷이나 자동 보존 등은 체크 해제를 했다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/d44d240f-78fb-4ddb-926d-62c08fabcda9/image.png" />
이렇게 삭제되는 중
조금 시간이 걸림</p>
<h2 id="5-rds-삭제-후-보안-그룹-삭제">5. RDS 삭제 후 보안 그룹 삭제</h2>
<p>이때 보안 탭으로 다시 온다.
<img alt="" src="https://velog.velcdn.com/images/jhh0830/post/6043d09b-3444-474d-afd6-817680197173/image.png" /></p>
<p>아까 했던 것처럼 삭제하면 이렇게 삭제가 가능하다.
바로 삭제.</p>
<h2 id="6-키페어-삭제">6. 키페어 삭제</h2>
<p>다음으로 키페어 탭으로 온다.
<img alt="" src="https://velog.velcdn.com/images/jhh0830/post/dec37328-6e6d-4cb6-8fc5-ae8b0490e72e/image.png" />
키페어도 마찬가지로 삭제하면 된다.
<img alt="" src="https://velog.velcdn.com/images/jhh0830/post/83ddc3ea-8177-4d05-b096-c2db11c0c201/image.png" />
삭제.</p>
<h2 id="7-s3-삭제">7. S3 삭제</h2>
<p>링크에는 S3도 사용하였다 해서 혹시나 봤는데
나도 버킷이 만들어져 있었다.(왜지?)
처음에 아무것도 몰라서 이것저것 하다가 만들었나부다..
<img alt="" src="https://velog.velcdn.com/images/jhh0830/post/77593bbf-3e82-4b96-af20-8fc4e9915233/image.png" /></p>
<p>버킷의 모든 객체를 비우고 삭제해야 한다고 한다.
나는 아무것도 없었기 때문에 바로 삭제가 가능했다.
<img alt="" src="https://velog.velcdn.com/images/jhh0830/post/d8f3ae19-748b-4f88-8de0-4a2b10141730/image.png" /></p>
<h2 id="8-route-53-삭제">8. Route 53 삭제</h2>
<p>Route 53은 도메인을 등록하는 메뉴인 것 같다.
나는 도메인을 구매하지 않았으므로 패스했다.
나중에 다시 AWS를 사용한다면 도메인도 구매해봐야지 :)</p>
<h2 id="9-확인">9. 확인</h2>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/a0b0d43b-a5d8-4cb6-85d2-adfdc519b621/image.png" />
보안 그룹이 1인 이유는 default인 보안 그룹이 있는 것 같다.
삭제할 수 없다고 뜬다.
인스턴스 1은 종료된 것이 저렇게 뜨는 것 같다. 들어가면 종료됨으로 뜸.</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/2fea1b7c-6ced-4e13-84c7-0a1e7b061933/image.png" />
시간이 더 지나니 인스턴스는 0이 된 것을 확인하였다 :)</p>