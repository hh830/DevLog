<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/5ce324f5-de5b-4781-81cb-02c67cbf2939/image.png" /></p>
<pre><code class="language-sql">CREATE TABLE pay (
    idpay INT PRIMARY KEY AUTO_INCREMENT,
    price INT NOT NULL,
    payinfo VARCHAR(45),
    status_statusid INT,
    FOREIGN KEY (status_statusid) REFERENCES status(statusid)
);

CREATE TABLE orderlist (
    orderid INT PRIMARY KEY AUTO_INCREMENT,
    totalprice INT NOT NULL,
    customer_custid VARCHAR(45) NOT NULL,
    pay_idpay INT UNIQUE, -- pay_idpay에 고유 제약 조건 추가
    FOREIGN KEY (customer_custid) REFERENCES customer(custid),
    FOREIGN KEY (pay_idpay) REFERENCES pay(idpay)
);</code></pre>
<p>분명 1대1로 했는데 reverse engineer로 erd를 표시하면 자꾸 1대다로 표시된다 ㅡㅡ</p>
<p>아래 글을 참고하였다.
<a href="https://loosie.tistory.com/49">https://loosie.tistory.com/49</a></p>
<p>MySQL Workbench에서는 1대N에서 N이 1대 0...1 관계도 포함하기 때문에 이렇게 표시 된다구 한다...</p>