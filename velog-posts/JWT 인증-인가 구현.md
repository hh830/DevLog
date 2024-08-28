<h1 id="jwt">JWT</h1>
<p>JWT 개념이 어려운 것 같아 유튜브로 개발자 유미님 스프링 시큐리티 JWT, JWT 심화 강의를 들었다.</p>
<p>머릿속 개념 정리 겸 작성하는 것이므로 틀린 점이 있을 수 있습니다.</p>
<p><a href="https://youtu.be/SxfweG-F6JM?si=ewn24cXnWMeRncD9">https://youtu.be/SxfweG-F6JM?si=ewn24cXnWMeRncD9</a></p>
<hr />
<h2 id="로그인인증">로그인(인증)</h2>
<p>로그인 시 <code>LoginFilter</code>로 가서 <code>attemptAuthentication()</code> 함수에서 요청을 가로채 username과 password를 받게 된다. 이를 <code>UsernamePasswordAuthenticationToken</code>을 사용하여 토큰에 담아 검증을 위한 <code>AuthenticationManager</code>로 전달한다.</p>
<p><code>AuthenticationManager</code>에서는 로그인 내부 검증이 일어난다.
<code>UserDetails</code>에서는 role, password, username, expired, lock 등 여부를 반환한다.
<code>UserDetailsService</code>에서는 <code>UserRepository</code>로 findById 요청을 하여 ID를 확인한다.</p>
<p>로그인 성공 시 <code>LoginFilter</code>에서 <code>JwtUtil</code>를 사용하여 jwt를 생성할 수 있다.</p>
<p>이때 단일 토큰만 사용하게 되면 <strong>토큰 탈취 위험</strong>이 있다.
따라서 access 토큰과 refresh 토큰 두가지를 사용한다.</p>
<p>만료 시간을 access 토큰은 짧게 10분 정도, refresh 토큰은 길게 24시간 정도로 설정하여 생성하였다.</p>
<p>그리고 refresh 토큰은 db(Redis, MySQL 등)에 저장하여 서버에서 주도권을 가지도록 한다.</p>
<p>생성된 토큰은 access는 <strong>헤더</strong>에, refresh는 <strong>쿠키</strong>에 담아 보냈다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/1876f590-07e6-4e69-bd2d-8f40d605857e/image.png" /></p>
<h2 id="검증-filter">검증 Filter</h2>
<p>토큰을 검증할 때 1차로 <code>Security Authentication Filter</code>가 작동하여 1차 검증을 한다.</p>
<p><code>JwtFilter</code>에서는 access 토큰을 검증해 유효한 토큰이면 <code>SecurityContextHolder</code>에 해당 사용자를 등록하여 일시적으로 사용자 세션을 생성한다.
이때 stateless로 유지되기 위해 <code>security config</code>에 stateless 설정을 해야 한다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/2a73c48f-e469-4eaa-be38-ae7d7178e16e/image.png" /></p>
<h2 id="refresh-rotate">Refresh Rotate</h2>
<h3 id="refresh-rotate란">Refresh Rotate란?</h3>
<p>Refresh 토큰으로 Access 토큰, Refresh 토큰을 재발급 받는 것
Refresh 토큰 교체로 보안성이 강화되고, 로그인 지속시간이 길어진다.</p>
<p><code>reissue</code> 요청이 오면 refresh 토큰을 획득하여 토큰이 유효한지 확인하고, DB에 저장되어 있는지까지 확인한다.
모두 유효하다면 access 토큰과 refresh 토큰을 모두 재발급하여 refresh 토큰은 다시 db에 저장하고 기존 refresh 토큰은 삭제한다.</p>
<p><code>refresh entity</code>에는 id, username(사용자 id), refresh(토큰), expiration(만료시간)를 담았다.
<img alt="" src="https://velog.velcdn.com/images/jhh0830/post/621bae59-906b-4d64-99a0-67f525d35f49/image.png" /></p>
<h2 id="로그아웃">로그아웃</h2>
<p>로그아웃 시 기본 <code>spring security filter</code>에 있는 <code>logout filter</code>로 처리된다.
따라서 <code>CustomLogoutFilter</code>를 생성하여 필터단에 추가해주어 기본 <code>LogoutFilter</code>로 가기 전에 <code>CustomLogoutFilter</code>를 실행해 로그아웃 되도록 하였다.</p>
<p>refresh 토큰을 db에서 삭제하며 서버 단에서 토큰 주도권을 통해 로그아웃 되도록 하였다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/9ed0b1d9-f908-4f80-8789-29663d43d633/image.png" /></p>
<p>로그아웃 필터가 로그인 필터(UsernamePasswordAuthenticationFilter)보다 앞에 있는 이유는 기본 filter 순서에서 앞에 있어서 그렇다.</p>
<h3 id="spring-security-기본-filter">spring security 기본 filter</h3>
<pre><code>org.springframework.security.web.session.DisableEncodeUrlFilter@404db674,
org.springframework.security.web.context.request.async.WebAsyncManagerIntegrationFilter@50f097b5,
org.springframework.security.web.context.SecurityContextHolderFilter@6fc6deb7,
org.springframework.security.web.header.HeaderWriterFilter@6f76c2cc,
org.springframework.security.web.csrf.CsrfFilter@c29fe36,
org.springframework.security.web.authentication.logout.LogoutFilter@ef60710,
org.springframework.security.web.authentication.UsernamePasswordAuthenticationFilter@7c2dfa2,
org.springframework.security.web.authentication.ui.DefaultLoginPageGeneratingFilter@4397a639,
org.springframework.security.web.authentication.ui.DefaultLogoutPageGeneratingFilter@7add838c,
org.springframework.security.web.authentication.www.BasicAuthenticationFilter@5cc9d3d0,
org.springframework.security.web.savedrequest.RequestCacheAwareFilter@7da39774,
org.springframework.security.web.servletapi.SecurityContextHolderAwareRequestFilter@32b0876c,
org.springframework.security.web.authentication.AnonymousAuthenticationFilter@3662bdff,
org.springframework.security.web.access.ExceptionTranslationFilter@77681ce4,
org.springframework.security.web.access.intercept.AuthorizationFilter@169268a7]</code></pre><hr />
<h1 id="코드">코드</h1>
<h2 id="1-로그인인증">1. 로그인(인증)</h2>
<h3 id="loginfilter">LoginFilter</h3>
<pre><code class="language-java">import com.example.DCRW.dto.CustomUserDetails;
import com.example.DCRW.dto.LoginDto;
import com.example.DCRW.entity.RefreshEntity;
import com.example.DCRW.repository.RefreshRepository;
import com.fasterxml.jackson.databind.ObjectMapper;
import jakarta.servlet.FilterChain;
import jakarta.servlet.ServletInputStream;
import jakarta.servlet.http.Cookie;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import org.springframework.http.HttpStatus;
import org.springframework.security.authentication.AuthenticationManager;
import org.springframework.security.authentication.UsernamePasswordAuthenticationToken;
import org.springframework.security.core.Authentication;
import org.springframework.security.core.AuthenticationException;
import org.springframework.security.core.GrantedAuthority;
import org.springframework.security.web.authentication.UsernamePasswordAuthenticationFilter;
import org.springframework.util.StreamUtils;

import java.io.IOException;
import java.nio.charset.StandardCharsets;
import java.util.Collection;
import java.util.Date;
import java.util.Iterator;

// 로그인
public class LoginFilter extends UsernamePasswordAuthenticationFilter {
    private final AuthenticationManager authenticationManager;
    private final JwtUtil jwtUtil;
    private final RefreshRepository refreshRepository;

    public LoginFilter(AuthenticationManager authenticationManager, JwtUtil jwtUtil, RefreshRepository refreshRepository) {
        this.authenticationManager = authenticationManager;
        this.jwtUtil = jwtUtil;
        this.refreshRepository = refreshRepository;
    }

    @Override
    public Authentication attemptAuthentication(HttpServletRequest request, HttpServletResponse response) throws AuthenticationException {
        // 요청을 가로채서 요청에 담겨있는 username, password 받기
        LoginDto loginDto = new LoginDto();

        try{
            ObjectMapper objectMapper = new ObjectMapper();
            ServletInputStream inputStream = request.getInputStream();
            String messageBody = StreamUtils.copyToString(inputStream, StandardCharsets.UTF_8);
            loginDto = objectMapper.readValue(messageBody, LoginDto.class);
        } catch (IOException e){
            throw new RuntimeException(e);
        }


        String username = loginDto.getUserName();
        String password = loginDto.getPassword();

        System.out.println(username);
        System.out.println(password);

        // 스프링 시큐리티에서 username과 password를 검증하기 위해 token에 담아야 함
        UsernamePasswordAuthenticationToken authenticationToken = new UsernamePasswordAuthenticationToken(username, password, null); // 세번째 파라미터는 role

        // token에 담은 검증을 위한 AuthenticationManager로 전달
        return authenticationManager.authenticate(authenticationToken);
    }

    // 로그인 성공 시(여기서 jwt 발급)
    @Override
    protected void successfulAuthentication(HttpServletRequest request, HttpServletResponse response, FilterChain chain, Authentication authentication){
        String username = authentication.getName();

        Collection&lt;? extends GrantedAuthority&gt; authorities = authentication.getAuthorities();
        Iterator&lt;? extends GrantedAuthority&gt; iterator = authorities.iterator();
        GrantedAuthority auth = iterator.next();

        String role = auth.getAuthority();

        // 토큰 생성
        String access = jwtUtil.createJwt(&quot;access&quot;, username, role, 600000L); // 프론트 로컬 스토리지에 저장
        String refresh = jwtUtil.createJwt(&quot;refresh&quot;, username, role, 8640000L); // 쿠키에 저장

        // Refresh 토큰 저장
        addRefreshEntity(username, refresh, 86400000L);

        // 응답
        response.setHeader(&quot;access&quot;, access);
        response.addCookie(createCookie(&quot;refresh&quot;, refresh));
        response.setStatus(HttpStatus.OK.value());
    }

    private Cookie createCookie(String key, String value) {
        Cookie cookie = new Cookie(key, value);
        cookie.setMaxAge(24*60*60); // 쿠키 생명주기
        //cookie.setSecure(true); //https 통신을 사용할 경우 넣기
        //cookie.setPath(&quot;/&quot;); // 쿠키가 적용될 범위
        cookie.setHttpOnly(true); // 클라이언트 단에서 자바스크립트에서 해당 쿠키를 접근할 수 없도록

        return cookie;
    }

    // 로그인 실패 시
    @Override
    protected void unsuccessfulAuthentication(HttpServletRequest request, HttpServletResponse response, AuthenticationException failed){
        response.setStatus(401);
    }

    // db에 refresh 토큰 추가
    private void addRefreshEntity(String username, String refresh, Long expiredMs){
        Date date = new Date(System.currentTimeMillis() + expiredMs);

        RefreshEntity refreshEntity = new RefreshEntity();
        refreshEntity.setUsername(username);
        refreshEntity.setRefresh(refresh);
        refreshEntity.setExpiration(date.toString());

        refreshRepository.save(refreshEntity);
    }
}</code></pre>
<h3 id="customuserdetailsuserdetails">CustomUserDetails(UserDetails)</h3>
<pre><code class="language-java">package com.example.DCRW.dto;

import com.example.DCRW.entity.Users;
import org.springframework.security.core.GrantedAuthority;
import org.springframework.security.core.userdetails.UserDetails;

import java.util.ArrayList;
import java.util.Collection;

public class CustomUserDetails implements UserDetails {
    private final Users users;

    public CustomUserDetails(Users users) {
        this.users = users;
    }

    // role 값 반환
    @Override
    public Collection&lt;? extends GrantedAuthority&gt; getAuthorities() {
        Collection&lt;GrantedAuthority&gt; collection = new ArrayList&lt;&gt;();

        collection.add(new GrantedAuthority() {
            @Override
            public String getAuthority() {
                return String.valueOf(users.getRoleCode());
            }
        });

        return collection;
    }

    // password 값 반환
    @Override
    public String getPassword() {
        return users.getPassword();
    }

    // user
    @Override
    public String getUsername() {
        return users.getUserId();
    }

    // 계정이 Expired 됐는지
    @Override
    public boolean isAccountNonExpired() {
        return UserDetails.super.isAccountNonExpired();
    }

    // 계정이 lock 됐는지
    @Override
    public boolean isAccountNonLocked() {
        return UserDetails.super.isAccountNonLocked();
    }

    @Override
    public boolean isCredentialsNonExpired() {
        return UserDetails.super.isCredentialsNonExpired();
    }

    @Override
    public boolean isEnabled() {
        return UserDetails.super.isEnabled();
    }
}</code></pre>
<h3 id="customuserdetailsserviceuserdetailsservice">CustomUserDetailsService(UserDetailsService)</h3>
<pre><code class="language-java">import com.example.DCRW.dto.CustomUserDetails;
import com.example.DCRW.entity.Users;
import com.example.DCRW.repository.UsersRepository;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.core.userdetails.UsernameNotFoundException;
import org.springframework.stereotype.Service;

@Service
public class CustomUserDetailsService implements UserDetailsService {
    private final UsersRepository usersRepository;

    public CustomUserDetailsService(UsersRepository usersRepository) {
        this.usersRepository = usersRepository;
    }

    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        Users users = usersRepository.findByUserId(username);
        if(users != null){
            return new CustomUserDetails(users);
        }

        return null;
    }
}</code></pre>
<h3 id="jwtutil">JwtUtil</h3>
<pre><code class="language-java">import io.jsonwebtoken.Jwts;
import org.springframework.beans.factory.annotation.Value; // properties에서 값 가져옴
import org.springframework.stereotype.Component;

import javax.crypto.SecretKey;
import javax.crypto.spec.SecretKeySpec;
import java.nio.charset.StandardCharsets;
import java.util.Date;

// jwt 생성 및 검증
@Component
public class JwtUtil {
    private SecretKey secretKey;

    public JwtUtil(@Value(&quot;${spring.jwt.secret}&quot;) String secret) {
        this.secretKey = new SecretKeySpec(secret.getBytes(StandardCharsets.UTF_8), Jwts.SIG.HS256.key().build().getAlgorithm());
    }

    public String getUsername(String token){
        return Jwts.parser().verifyWith(secretKey).build().parseSignedClaims(token).getPayload().get(&quot;username&quot;, String.class);
    }

    public String getRole(String token) {

        return Jwts.parser().verifyWith(secretKey).build().parseSignedClaims(token).getPayload().get(&quot;role&quot;, String.class);
    }

    public Boolean isExpired(String token) {

        return Jwts.parser().verifyWith(secretKey).build().parseSignedClaims(token).getPayload().getExpiration().before(new Date());
    }

    public String getCategory(String token){
        return Jwts.parser().verifyWith(secretKey).build().parseSignedClaims(token).getPayload().get(&quot;category&quot;, String.class);
    }

    // payload에 저장될 정보 (username, role, 생성일, 만료일)
    public String createJwt(String category, String username, String role, Long expiredMs) {

        return Jwts.builder()
                .claim(&quot;category&quot;, category)
                .claim(&quot;username&quot;, username) // payload에 claim으로 데이터 넣을 수 있음
                .claim(&quot;role&quot;, role)
                .issuedAt(new Date(System.currentTimeMillis())) // 언제 발행 됐는지(현재 발행 시간)
                .expiration(new Date(System.currentTimeMillis() + expiredMs)) // 언제 소멸될 것인지
                .signWith(secretKey) // 시그니처를 만들어 암호화 진행
                .compact(); // 토큰 compact
    }
}</code></pre>
<hr />
<h2 id="2-로그인검증">2. 로그인(검증)</h2>
<h3 id="jwtfilter">JwtFilter</h3>
<pre><code class="language-java">import com.example.DCRW.dto.CustomUserDetails;
import com.example.DCRW.entity.Users;
import io.jsonwebtoken.ExpiredJwtException;
import jakarta.servlet.FilterChain;
import jakarta.servlet.ServletException;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import org.springframework.security.authentication.UsernamePasswordAuthenticationToken;
import org.springframework.security.core.Authentication;
import org.springframework.security.core.context.SecurityContextHolder;
import org.springframework.web.filter.OncePerRequestFilter;

import java.io.IOException;
import java.io.PrintWriter;

// 세션 생성
public class JwtFilter extends OncePerRequestFilter {
    private final JwtUtil jwtUtil;

    public JwtFilter(JwtUtil jwtUtil) {
        this.jwtUtil = jwtUtil;
    }

    @Override
    protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain) throws ServletException, IOException {
        // 헤더에서 access키에 담긴 토큰을 꺼냄
        String accessToken = request.getHeader(&quot;access&quot;);

        // 토큰이 없다면 다음 필터로 넘김
        if (accessToken == null) {

            filterChain.doFilter(request, response);

            return;
        }

        // 토큰 만료 여부 확인, 만료시 다음 필터로 넘기지 않음
        try {
            // 토큰 소멸 시간 검증
            jwtUtil.isExpired(accessToken);
        } catch (ExpiredJwtException e) {

            //response body
            PrintWriter writer = response.getWriter();
            writer.print(&quot;access token expired&quot;);

            //response status code
            response.setStatus(HttpServletResponse.SC_UNAUTHORIZED);
            return;
        }

        // 토큰이 access인지 확인 (발급시 페이로드에 명시)
        String category = jwtUtil.getCategory(accessToken);

        if (!category.equals(&quot;access&quot;)) {

            //response body
            PrintWriter writer = response.getWriter();
            writer.print(&quot;invalid access token&quot;);

            //response status code
            response.setStatus(HttpServletResponse.SC_UNAUTHORIZED);
            return;
        }

        // username, role 값을 획득
        String username = jwtUtil.getUsername(accessToken);
        String role = jwtUtil.getRole(accessToken);

        Users user = new Users();
        user.setUserId(username);
        user.setRoleCode(Integer.parseInt(role));
        CustomUserDetails customUserDetails = new CustomUserDetails(user);

        // 로그인 진행 - 인증 토큰 생성
        Authentication authToken = new UsernamePasswordAuthenticationToken(customUserDetails, null, customUserDetails.getAuthorities());
        SecurityContextHolder.getContext().setAuthentication(authToken); // 해당 유저 등록 - 일시적 유저 세션 생성

        filterChain.doFilter(request, response);
    }
}</code></pre>
<hr />
<h2 id="3-refresh-rotate">3. Refresh Rotate</h2>
<h3 id="reissuecontroller">ReissueController</h3>
<pre><code class="language-java">import com.example.DCRW.entity.RefreshEntity;
import com.example.DCRW.jwt.JwtUtil;
import com.example.DCRW.repository.RefreshRepository;
import io.jsonwebtoken.ExpiredJwtException;
import jakarta.servlet.http.Cookie;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import lombok.RequiredArgsConstructor;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.ResponseBody;

import java.util.Date;

@Controller
@ResponseBody
@RequiredArgsConstructor
public class ReissueController {

    private final JwtUtil jwtUtil;
    private final RefreshRepository refreshRepository;

    // refresh(쿠키) 토큰을 보내면 access 토큰 재발급
    // -&gt; 여기서 Refresh 토큰도 함께 갱신하는 건 Refresh Rotate
    // Refresh Rotate
    // - 로그인 지속시간 길어짐(한달 두달), 보안성 강화
    // - 발급했던 Refresh 토큰을 모두 기억한 뒤 Rotate 이전의 Refresh 토큰은 사용하지 못하도록
    @PostMapping(&quot;/reissue&quot;)
    public ResponseEntity&lt;?&gt; reissue(HttpServletRequest request, HttpServletResponse response) {

        //get refresh token
        String refresh = null;
        Cookie[] cookies = request.getCookies();
        for (Cookie cookie : cookies) {

            if (cookie.getName().equals(&quot;refresh&quot;)) {

                refresh = cookie.getValue();
            }
        }

        if (refresh == null) {

            //response status code
            return new ResponseEntity&lt;&gt;(&quot;refresh token null&quot;, HttpStatus.BAD_REQUEST);
        }

        // 만료 확인
        try {
            jwtUtil.isExpired(refresh);
        } catch (ExpiredJwtException e) {

            //response status code
            return new ResponseEntity&lt;&gt;(&quot;refresh token expired&quot;, HttpStatus.BAD_REQUEST);
        }

        // 토큰이 refresh인지 확인 (발급시 페이로드에 명시)
        String category = jwtUtil.getCategory(refresh);

        if (!category.equals(&quot;refresh&quot;)) {

            //response status code
            return new ResponseEntity&lt;&gt;(&quot;invalid refresh token&quot;, HttpStatus.BAD_REQUEST);
        }

        // DB에 저장되어 있는지 확인
        Boolean isExist = refreshRepository.existsByRefresh(refresh);
        if(!isExist){
            return new ResponseEntity&lt;&gt;(&quot;invalid refresh token&quot;, HttpStatus.BAD_REQUEST);
        }

        String username = jwtUtil.getUsername(refresh);
        String role = jwtUtil.getRole(refresh);

        //make new JWT
        String newAccess = jwtUtil.createJwt(&quot;access&quot;, username, role, 600000L);
        String newRefresh = jwtUtil.createJwt(&quot;refresh&quot;, username, role, 86400000L);

        // Refresh 토큰 저장 DB에 기존의 Refresh 토큰 삭제 후 새 Refresh 토큰 저장
        refreshRepository.deleteByRefresh(refresh);
        addRefreshEntity(username, newRefresh, 86400000L);

        //response
        response.setHeader(&quot;access&quot;, newAccess);
        response.addCookie(createCookie(&quot;refresh&quot;, newRefresh));

        return new ResponseEntity&lt;&gt;(HttpStatus.OK);
    }

    private void addRefreshEntity(String username, String refresh, Long expiredMs) {
        Date date = new Date(System.currentTimeMillis() + expiredMs);

        RefreshEntity refreshEntity = new RefreshEntity();
        refreshEntity.setUsername(username);
        refreshEntity.setRefresh(refresh);
        refreshEntity.setExpiration(date.toString());

        refreshRepository.save(refreshEntity);
    }

    private Cookie createCookie(String key, String value) {
        Cookie cookie = new Cookie(key, value);
        cookie.setMaxAge(24*60*60); // 쿠키 생명주기
        //cookie.setSecure(true); //https 통신을 사용할 경우 넣기
        //cookie.setPath(&quot;/&quot;); // 쿠키가 적용될 범위
        cookie.setHttpOnly(true); // 클라이언트 단에서 자바스크립트에서 해당 쿠키를 접근할 수 없도록

        return cookie;
    }
}</code></pre>
<h3 id="refreshrepository">RefreshRepository</h3>
<pre><code class="language-java">import com.example.DCRW.entity.RefreshEntity;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;
import org.springframework.transaction.annotation.Transactional;

@Repository
public interface RefreshRepository extends JpaRepository&lt;RefreshEntity, Long&gt; {
    Boolean existsByRefresh(String refresh);

    @Transactional
    void deleteByRefresh(String refresh);
}</code></pre>
<h3 id="refresh-entity">Refresh Entity</h3>
<pre><code class="language-java">import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;
import lombok.Getter;
import lombok.RequiredArgsConstructor;
import lombok.Setter;

@Entity
@Getter
@Setter
public class RefreshEntity {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String username;
    private String refresh;
    private String expiration;
}</code></pre>
<hr />
<h2 id="4-로그아웃">4. 로그아웃</h2>
<h3 id="customlogoutfilter">CustomLogoutFilter</h3>
<pre><code class="language-java">import com.example.DCRW.repository.RefreshRepository;
import io.jsonwebtoken.ExpiredJwtException;
import jakarta.servlet.FilterChain;
import jakarta.servlet.ServletException;
import jakarta.servlet.ServletRequest;
import jakarta.servlet.ServletResponse;
import jakarta.servlet.http.Cookie;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import org.springframework.web.filter.GenericFilterBean;

import java.io.IOException;

public class CustomLogoutFilter extends GenericFilterBean {

    private final JwtUtil jwtUtil;
    private final RefreshRepository refreshRepository;

    public CustomLogoutFilter(JwtUtil jwtUtil, RefreshRepository refreshRepository) {

        this.jwtUtil = jwtUtil;
        this.refreshRepository = refreshRepository;
    }

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {

        doFilter((HttpServletRequest) request, (HttpServletResponse) response, chain);
    }

    private void doFilter(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain) throws IOException, ServletException {
        // 요청 로그아웃인지 아닌지 확인
        String requestUri = request.getRequestURI();
        if (!requestUri.matches(&quot;^\\/logout$&quot;)) {

            filterChain.doFilter(request, response); // 로그아웃이 아니면 다음 필터로
            return;
        }
        String requestMethod = request.getMethod();
        if (!requestMethod.equals(&quot;POST&quot;)) { // post가 아니면 다음 필터로

            filterChain.doFilter(request, response);
            return;
        }

        //get refresh token
        String refresh = null;
        Cookie[] cookies = request.getCookies(); // 쿠키 불러오기
        for (Cookie cookie : cookies) {

            if (cookie.getName().equals(&quot;refresh&quot;)) { // 쿠키에 refresh 있는지

                refresh = cookie.getValue();
            }
        }

        //refresh null check
        if (refresh == null) { // 쿠키에 refresh 토큰이 없으면 400

            response.setStatus(HttpServletResponse.SC_BAD_REQUEST);
            return;
        }

        try {
            // 토큰 만료 확인
            jwtUtil.isExpired(refresh);
        } catch (ExpiredJwtException e) {
            // 만료되었으면 이미 로그아웃 된 상태
            response.setStatus(HttpServletResponse.SC_BAD_REQUEST);
            return;
        }

        // 토큰이 refresh인지 확인 (발급시 페이로드에 명시)
        String category = jwtUtil.getCategory(refresh);
        if (!category.equals(&quot;refresh&quot;)) {

            //response status code
            response.setStatus(HttpServletResponse.SC_BAD_REQUEST);
            return;
        }

        //DB에 저장되어 있는지 확인
        Boolean isExist = refreshRepository.existsByRefresh(refresh);
        if (!isExist) {

            //response status code
            response.setStatus(HttpServletResponse.SC_BAD_REQUEST);
            return;
        }

        //로그아웃 진행
        //Refresh 토큰 DB에서 제거
        refreshRepository.deleteByRefresh(refresh);

        //Refresh 토큰 Cookie 값 0
        Cookie cookie = new Cookie(&quot;refresh&quot;, null);
        cookie.setMaxAge(0);
        cookie.setPath(&quot;/&quot;);

        response.addCookie(cookie);
        response.setStatus(HttpServletResponse.SC_OK);
    }
}</code></pre>
<hr />
<h2 id="5-config">5. Config</h2>
<h3 id="security-config">Security Config</h3>
<pre><code class="language-java">import com.example.DCRW.jwt.CustomLogoutFilter;
import com.example.DCRW.jwt.JwtFilter;
import com.example.DCRW.jwt.JwtUtil;
import com.example.DCRW.jwt.LoginFilter;
import com.example.DCRW.repository.RefreshRepository;
import jakarta.servlet.http.HttpServletRequest;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.authentication.AuthenticationManager;
import org.springframework.security.config.annotation.authentication.configuration.AuthenticationConfiguration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.http.SessionCreationPolicy;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.security.web.SecurityFilterChain;
import org.springframework.security.web.authentication.UsernamePasswordAuthenticationFilter;
import org.springframework.security.web.authentication.logout.LogoutFilter;
import org.springframework.web.cors.CorsConfiguration;
import org.springframework.web.cors.CorsConfigurationSource;

import java.util.Collections;

@Configuration
@EnableWebSecurity // security 관련 configuration
public class SecurityConfig {
    private final AuthenticationConfiguration authenticationConfiguration;
    private final JwtUtil jwtUtil;
    private final RefreshRepository refreshRepository;

    public SecurityConfig(AuthenticationConfiguration authenticationConfiguration, JwtUtil jwtUtil, RefreshRepository refreshRepository) {
        this.authenticationConfiguration = authenticationConfiguration;
        this.jwtUtil = jwtUtil;
        this.refreshRepository = refreshRepository;
    }

    @Bean
    public BCryptPasswordEncoder bCryptPasswordEncoder() {
        return new BCryptPasswordEncoder();
    }

    @Bean
    public AuthenticationManager authenticationManager(AuthenticationConfiguration configuration) throws Exception {
        return configuration.getAuthenticationManager();
    }

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception{
        // cors 설정(로그인과 같은 필터 사용하는 기능에서도 방지)
        http
                .cors((cors) -&gt; cors
                        .configurationSource(new CorsConfigurationSource() {
                            @Override
                            public CorsConfiguration getCorsConfiguration(HttpServletRequest request) {
                                CorsConfiguration configuration = new CorsConfiguration();

                                configuration.setAllowedOrigins(Collections.singletonList(&quot;http://localhost:3000&quot;)); // 리액트 서버 포트 열어줌
                                configuration.setAllowedMethods(Collections.singletonList(&quot;*&quot;)); // get, post 등 모든 메소드 허용
                                configuration.setAllowCredentials(true); // credential 설정 true
                                configuration.setAllowedHeaders(Collections.singletonList(&quot;*&quot;));
                                configuration.setMaxAge(3600L); // 허용 최대 시간
                                configuration.setExposedHeaders(Collections.singletonList(&quot;Authorization&quot;)); // Authorization 헤더에 보낼 것이기 때문에 허용

                                return null;
                            }
                        }));

        // csrf disable - jwt는 세션을 stateless 상태로 관리하기 때문에 csrf 공격을 방어하지 않아도 되서 기본적으로 disable
        http
                .csrf((auth) -&gt; auth.disable());

        // Form 로그인 방식 disable
        http
                .formLogin((auth) -&gt; auth.disable());

        // http basic 인증 방식 disable
        http
                .httpBasic((auth) -&gt; auth.disable());

        // 인가 작업(경로에 따른 허용 권한 접근 관리)
        http
                .authorizeHttpRequests((auth) -&gt; auth
                        .requestMatchers(&quot;/login&quot;, &quot;/&quot;, &quot;/signup&quot;, &quot;/reissue&quot;).permitAll() // 모든 권한 허용
                        .requestMatchers(&quot;/admin&quot;).hasRole(&quot;0&quot;) // admin이라는 접근은 ADMIN 권한만 가능
                        .anyRequest().authenticated()); // 이외 접근은 로그인 한 사용자만 접근 가능

        // 필터 등록 - Login Filter 뒤에 토큰 검증 및 세션 생성하는 jwtFilter
        http
                .addFilterAfter(new JwtFilter(jwtUtil), LoginFilter.class);

        // 필터 등록(시큐리티가 동작할 때 필터가 동작할 수 있도록) - UsernamePasswordAuthenticationFilter 대체해서 필터를 등록하기 때문에 그 자리에 등록하기 위해 addFilterAt()
        http
                .addFilterAt(new LoginFilter(authenticationManager(authenticationConfiguration), jwtUtil, refreshRepository), UsernamePasswordAuthenticationFilter.class);

        // 필터 등록 - Logout Filter 앞에
        http
                .addFilterBefore(new CustomLogoutFilter(jwtUtil, refreshRepository), LogoutFilter.class);

        // 세션 설정 - jwt는 항상 stateless 상태로 관리해야 한다. 매우 중요
        http
                .sessionManagement((session) -&gt; session
                        .sessionCreationPolicy(SessionCreationPolicy.STATELESS));


        return http.build();
    }
}</code></pre>
<h3 id="corsmvcconfig">CorsMvcConfig</h3>
<pre><code class="language-java">import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.CorsRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

// 리액트에서 웹 페이지를 띄우고 사용자가 브라우저 단에서 요청했을 때 cors 문제 발생하지 않도록 설정
@Configuration
public class CorsMvcConfig implements WebMvcConfigurer {
    @Override
    public void addCorsMappings(CorsRegistry corsRegistry){
        corsRegistry.addMapping(&quot;/**&quot;)
                .allowedOrigins(&quot;http://localhost:3000&quot;);
    }

}</code></pre>