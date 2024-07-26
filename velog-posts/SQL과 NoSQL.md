<h2 id="1-scallability확장성">1. Scallability(확장성)</h2>
<h3 id="scale-up">Scale Up</h3>
<p>: 인프라 장비의 개수를 늘리지 않고 내부 부품을 더 좋은 부품으로 업그레이드</p>
<ul>
<li><p>RDBMS에 더 유리</p>
</li>
<li><p>JOIN : 분리된 데이터 집합(table)을 다시 유의미한 하나로 만드는 작업
  -&gt; 만약 2 table을 join하는데 A table은 1번 서버, B table은 2번 서버에 있다면 Network 통신이 필요함 (비효율적)</p>
</li>
</ul>
<h3 id="scale-out">Scale Out</h3>
<p>: 인프라 장비의 내부 부품보다는 장비의 개수를 늘리는 작업</p>
<ul>
<li><p>NoSQL에 더 유리</p>
</li>
<li><p>RDBMS의 JOIN은 없다</p>
</li>
<li><p>최대한 자기완결적인 데이터로 저장, 관리</p>
</li>
<li><blockquote>
<p>위 두가지를 혼합하여 일반적으로 대응</p>
</blockquote>
</li>
</ul>
<h2 id="2-데이터-철학">2. 데이터 철학</h2>
<h3 id="rdbms">RDBMS</h3>
<p>커다란 덩어리의 데이터를 분할하고 정제해서 데이터 관리의 목표를 획득한다.</p>
<ul>
<li>사용 년한(오랜 기간), 변경 주기(빠르다), 처리 속도(빠르면 좋지만 정확한 것이 더 우선)</li>
<li>은행, 공공기관, 대부분의 데이터 관리가 필요한 곳</li>
</ul>
<h3 id="nosql">NoSQL</h3>
<p>커다란 덩어리의 데이터를 최대한 자기 완결적인 데이터로 구성해서 데이터 관리의 목표를 획득한다.</p>
<ul>
<li>사용 년한(짧다), 변경 주기(갱신이 적다), 처리 속도(아주 중요하다 &lt;= 한 곳에 모아 놓았다. 정확한 것은 두번째)</li>
<li>마케팅, 과학 연구, &lt;&lt; function programming &gt;&gt; 단기적 목표를 위해 데이터 취합, 소화 후 버린다.</li>
<li>통신사의 고객 행동 분석 -&gt; 차기 요금제 구성에 사용</li>
<li>페이스북 메타(개인정보/행동 파악 페이스북으로 전송)을 쇼핑몰 사이트에 설치</li>
<li>빅데이터</li>
</ul>
<hr />
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/49ea5ae3-aa47-4f0b-a390-112a642dad2c/image.png" /></p>
<h2 id="mongodb-사용에-관하여">MongoDB 사용에 관하여</h2>
<p>MongDB에는 조인이 없어서 그래서 사원에 모든 관련 정보(휴가 등)를 넣어야 됨.</p>
<p>휴가라는 것에 접근을 할 때 사원을 먼저 찾고 휴가를 찾아야 함. 
(RDBMS는 휴가를 먼저 접근할 수 있음)
이러면 비대해지게 됨.</p>
<blockquote>
<h3 id="닉네임을-바꾼다면">닉네임을 바꾼다면?</h3>
<p>$lookup으로 조인 같이 흉내는 낼 수 있음
<br />
만약 닉네임을 바꾼다면 이전 닉네임은 바뀌지 않아서 서로 다른 사람처럼 보이는 현상이 발생함.
<br />
이런 경우 하나하나 바꿔주는 수 밖에 없음</p>
</blockquote>
<p>** 조인이 자주 필요하면 MongoDB와 어울리지 않음. **</p>