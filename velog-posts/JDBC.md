<h2 id="db-연결-방법">DB 연결 방법</h2>
<p>: DriverManager 클래스의 getConnection()</p>
<h2 id="db-연결-객체">DB 연결 객체</h2>
<p>: Connection 객체</p>
<pre><code>    Connection con = DriverManager.getConnection(url, user, pwd);</code></pre><h2 id="sql-전달--결과-관리-객체">SQL 전달 , 결과 관리 객체</h2>
<p>Statement 객체, PreparedStatement 객체, CallableStatement 객체 ( stored procedure )</p>
<ul>
<li><p>Statement 객체 : SQL 전달할 때 SQL 문에 대한 사전 처리 등이 진행됨, 객체 생성 시에 SQL과 상관없이 생성.</p>
<pre><code class="language-sql">update customer set address = '한국 서울' where custid = 6; 

con = DriverManager.getConnection(url, user, pwd);
stmt = con.createStatement();
int ret = stmt.executeUpdate(updateSql);
</code></pre>
</li>
<li><p>PreparedStatement 객체 : SQL 전달 전에 객체가 만들어 질 때 SQL 문에 대한 사전 처리 등이 진행됨, 파라미터 자리에 ?를 쓸 수 있어서 편함(권장됨)</p>
<pre><code class="language-sql">update customer set address = ? where custid = ?;

con = DriverManager.getConnection(url, user, pwd);

pstmt = con.prepareStatement(updateSql);
       pstmt.setString(1, address);
       pstmt.setInt(2, custId);

ret = pstmt.executeUpdate();
</code></pre>
<ul>
<li>CallableStatement 객체 :  stored procedure를 실행시키기 위해 사용되는 인터페이스
: query문을 하나의 파일 형태로 만들거나 데이터베이스에 저장해놓고 함수처럼 호출해서 사용하는 것 - 연속되는 쿼리문에서 빠른 성능을 보임</li>
</ul>
<br />



</li>
</ul>
<h2 id="sql-전달--결과-수신">SQL 전달 / 결과 수신</h2>
<h3 id="c-u-d--insert-update-delete-">C, U, D ( insert, update, delete )</h3>
<blockquote>
<h3 id="sql-전달">SQL 전달</h3>
<pre><code>ret = pstmt.executeUpdate();</code></pre></blockquote>
<h3 id="return-값">return 값</h3>
<pre><code>return 정수
(insert, udate, delete 된 row 리턴 )</code></pre><h3 id="r--read--select-">R ( read = select )</h3>
<blockquote>
<h3 id="sql-전달-1">SQL 전달</h3>
<pre><code>resultSet = pstmt.executeQuery();</code></pre></blockquote>
<h3 id="return-값-1">return 값</h3>
<pre><code>return resultSet;
읽은 ResultSet 객체가 반환됨</code></pre><p>위의 jdbc interface 메소드의 처리는 모두 jdbc driver( implement 객체) 가 대행함</p>
<h2 id="connection-pool">Connection pool</h2>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/b4fcf041-86ca-4da7-8ca8-fd88df2dfc4f/image.png" />
Connection 객체를 도로라 하고, PreparedStatement 객체를 자동차라고 하자.
도로가 있는데 자동차가 안다니면 안되고, 자동차가 있는데 도로가 있으면 안됨
-&gt; connection 객체를 효율적으로 관리하기 위해 connection pool 사용</p>
<h3 id="connection-pool-1">connection pool</h3>
<p>웹 컨테이너(WAS)가 실행되면서 일정량의 Connection 객체를 미리 만들어서 Pool에 저장.
APP이 반복적으로 무언가를 하려고 할 때 DB쪽에 미리 요청을 한다. 
이때 DB가 (connection객체)n개를 주면, app이 사용하고 반납하는 형태</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/19cda23a-77cd-4b41-b12b-099885b6e0a4/image.png" /></p>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/ff35ff2b-e44d-4125-b1e0-fd98b0212af2/image.png" /></p>
<h3 id="connection-pool의-종류">connection pool의 종류</h3>
<p>: common-dbcp2, tomcat-jdbc Pool, DriverManager DataSource, HikariCP</p>
<p><a href="https://f-lab.kr/insight/understanding-database-connection-pool">https://f-lab.kr/insight/understanding-database-connection-pool</a></p>
<hr />
<h2 id="jdbc-불편한점">JDBC 불편한점</h2>
<ul>
<li>많은 양의 코드 사용</li>
<li>개발자들 코드 일관성 확보 어려움</li>
<li>커넥션 풀 관리 이슈 어렵</li>
<li>자바 코드 내에 sql문이 있음(가독성 안 좋음 - stringbuilder 써야된다던지)</li>
</ul>
<hr />
<p>CallableStatement 참고글: <a href="https://blog.naver.com/naomi007/10017208097">https://blog.naver.com/naomi007/10017208097</a></p>
<p>좋은 비교 글: <a href="https://blog.naver.com/naomi007/10017208097">https://blog.naver.com/naomi007/10017208097</a></p>