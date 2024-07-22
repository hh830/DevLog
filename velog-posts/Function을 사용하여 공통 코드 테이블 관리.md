<p>상태를 관리하는 방법으로는 그냥 테이블에 열을 만들어 문자열로 관리해도 되겠지만, 공통 코드 테이블을 만들어 관리하는 방법도 있다.</p>
<p>아래와 같이 공통 코드를 담는 테이블을 만들어 여러가지 상태를 관리할 수 있다.
<img alt="" src="https://velog.velcdn.com/images/jhh0830/post/7d6d1aec-44db-47bc-abd0-fd8a7f50a1be/image.png" />
이렇게 공통 코드 테이블을 만들어 관리를 하면 추가, 삭제 등 확장성 면에서 이점이 된다.
또한 프론트 개발자와의 협업에서도 프론트가 하드 코딩으로 개발하는 것이 아닌 코드 테이블을 요청하여 변화가 생긴다면 그에 대한 반응이 가능하게 된다.</p>
<h2 id="공통-코드를-사용한-sql">공통 코드를 사용한 SQL</h2>
<pre><code class="language-sql">select user_id, user_name, user_clsf, 
       (select code_name from code where group_code = '001' and code = user_clsf) user_clsf_nm
  from user
 where user_id = 1; -- 홍길동 로그인한 경우</code></pre>
<h2 id="문제">문제</h2>
<blockquote>
<p>select code_name from code where group_code = '001' and code = user_clsf</p>
</blockquote>
<p>하지만 공통 코드 테이블을 사용하면 위와 유사한 서브쿼리들을 요청해야 한다는 문제가 있다.
회원 분류를 위한 select, 주문상태를 위한 select 등등.
이는 funtion 으로 만들면 일관된 코드, 쉬운 코드로 만들 수 있다.</p>
<h2 id="function">Function</h2>
<p>먼저 사용 전에 Function 사용 허가를 설정해야 한다.</p>
<blockquote>
<h3 id="function-권한-허가">function 권한 허가</h3>
<pre><code>SET GLOBAL log_bin_trust_function_creators = 1;</code></pre></blockquote>
<h3 id="function-생성">Function 생성</h3>
<blockquote>
<pre><code class="language-sql">CREATE FUNCTION '함수명' (
     파라미터명 데이터 타입
) RETURNS 반환데이터타입
BEGIN
    수행할 쿼리
RETRUN 반환값;
END</code></pre>
</blockquote>
<pre><code>

```sql
CREATE DEFINER=`root`@`localhost` FUNCTION `fun_code`(
    -- 받아오는 값
    p_group_code char(3),
    p_code char(3)
) RETURNS varchar(50) CHARSET utf8mb4
BEGIN
    -- 선언부 리턴될 값
    declare r_code_name varchar(50);

    -- 수행할 쿼리
    select code_name 
      into r_code_name 
      from code 
     where group_code = p_group_code 
       and code = p_code;

RETURN r_code_name;
END</code></pre><h3 id="변경된-sql">변경된 SQL</h3>
<pre><code class="language-sql">
select user_id, user_name, user_clsf, 
       fun_code('001', user_clsf) user_clsf_nm
  from user
 where user_id = 1; </code></pre>