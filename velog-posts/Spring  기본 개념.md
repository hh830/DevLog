<h2 id="di">DI</h2>
<ul>
<li>의존성 주입</li>
<li>하나의 객체에 다른 객체의 의존성을 제공하는 기술</li>
<li>DIP 원칙과 관련됨</li>
</ul>
<blockquote>
<h3 id="특징">특징</h3>
</blockquote>
<ul>
<li>코드 재활용성 높일 수 있어 유지보수 용이</li>
<li>클래스 간 결합도 낮출 수 있음</li>
<li>인터페이스 기반 설계</li>
<li>단위 테스트 쉬워짐</li>
</ul>
<pre><code class="language-java">// 원래 - 전통적인 코드
// 개발자가 선언하고 내가 객체 생성, 연결
// MyIf, MyIflmpl
MyIF myIf = new MyIFlmpl();
MyIf myIf = MyIflmpl.getInstance();

// spring 코드 - DI
// 변수만 선언하고 객체 생성, 연결을 Spring에 의뢰
// spring이 의뢰된 객체를 생성, 연결해줌
@Autowired
MyIF myIf;</code></pre>
<h2 id="aop">AOP</h2>
<p>: 부가 기능을 핵심 기능에서 분리해 한 곳으로 관리하게 하고 이 부가 기능을 어디에 적용할지 선택하는 기능을 합한 하나의 모듈</p>
<ul>
<li>핵심기능 : 해당 객체가 제공하는 고유의 기능</li>
<li>부가 기능: 핵심 기능을 보조하기 위해 제공되는 기능(로그 추적, 트랜잭션 등)</li>
</ul>
<h2 id="pojo">POJO</h2>
<ul>
<li>특정 기술에 종속되지 않는 순수한 자바 객체</li>
<li>객체지향적 원리에 충실하면서 환경과 기술에 종속되지 않고 필요에 따라 재활용 될 수 있는 방식으로 설계한 오브젝트</li>
<li>Getter, Setter 등</li>
</ul>
<h2 id="psa">PSA</h2>
<p>하나의 추상화로 여러 서비스를 묶어둔 것
어떤 서비스를 이용하기 위한 접근 방식을 일관된 방식으로 유지하여 애플리케이션에서 사용하는 기술이 변경되더라도 최소한의 변경만으로 변경된 요구 사항을 반영하기 위해 사용</p>
<ul>
<li>어노테이션 (@Transaction) 등</li>
</ul>
<hr />
<h3 id="spring을-이해하기-위한-background-개념">Spring을 이해하기 위한 Background 개념</h3>
<ul>
<li>Reflection</li>
<li>Proxy</li>
<li>Annotation</li>
</ul>