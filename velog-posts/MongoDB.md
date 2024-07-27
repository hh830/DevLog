<h1 id="mongodb">MongoDB</h1>
<p>-&gt; sql이 아니다 보니 함수형으로 호출하는 방식이 사용됨
<img alt="" src="https://velog.velcdn.com/images/jhh0830/post/c1567af6-d626-42fe-af57-17e2c0ed490d/image.png" /><br /><cite>출처: Beginersbook.com
<br />
문서 : <a href="https://www.w3schools.com/mongodb/">https://www.w3schools.com/mongodb/</a>
<br />  </p>
<hr />
<p>cmd를 열고 mongosh를 친다.</p>
<h3 id="데이터베이스-조회">데이터베이스 조회</h3>
<pre><code>show dbs</code></pre><h3 id="데이터베이스-삭제">데이터베이스 삭제</h3>
<pre><code>db.dropDatabase()</code></pre><h3 id="데이터베이스-선택-없으면-생성됨">데이터베이스 선택 (없으면 생성됨)</h3>
<pre><code>use db명</code></pre><hr />
<h2 id="create">create</h2>
<h3 id="컬렉션-생성">컬렉션 생성</h3>
<ul>
<li>db.createCollection(&quot;book&quot;)</li>
<li>db.book.insertOne(object) : 있으면 insert, 없어도 에러나지 않고 추가로 만들어짐</li>
</ul>
<hr />
<h2 id="insert">insert</h2>
<blockquote>
<h3 id="한-건일-경우">한 건일 경우</h3>
</blockquote>
<pre><code class="language-json">db.posts.insertOne({
  title: &quot;Post Title 1&quot;,
  body: &quot;Body of post.&quot;,
  category: &quot;News&quot;,
  likes: 1,
  tags: [&quot;news&quot;, &quot;events&quot;],
  date: Date()
})</code></pre>
<h3 id="여러-건일-경우">여러 건일 경우</h3>
<pre><code class="language-json">db.posts.insertMany([  
  {
    title: &quot;Post Title 2&quot;,
    body: &quot;Body of post.&quot;,
    category: &quot;Event&quot;,
    likes: 2,
    tags: [&quot;news&quot;, &quot;events&quot;],
    date: Date()
  },
  {
    title: &quot;Post Title 3&quot;,
    body: &quot;Body of post.&quot;,
    category: &quot;Technology&quot;,
    likes: 3,
    tags: [&quot;news&quot;, &quot;events&quot;],
    date: Date()
  },
  {
    title: &quot;Post Title 4&quot;,
    body: &quot;Body of post.&quot;,
    category: &quot;Event&quot;,
    likes: 4,
    tags: [&quot;news&quot;, &quot;events&quot;],
    date: Date()
  }
])</code></pre>
<hr />
<h2 id="delete">DELETE</h2>
<h3 id="컬렉션을-삭제">컬렉션을 삭제</h3>
<pre><code>db.book.drop()</code></pre><h3 id="데이터베이스-삭제-1">데이터베이스 삭제</h3>
<pre><code>db.dropDatabase()</code></pre><h3 id="테이블-내용-삭제">테이블 내용 삭제</h3>
<pre><code>db.book.deleteOne( {bookid: 11} )</code></pre><h3 id="테이블-내용-모두-삭제">테이블 내용 모두 삭제</h3>
<pre><code>db.book.deleteMany( {} )
db.book.deleteMany()는 오류남</code></pre><hr />
<h2 id="조회">조회</h2>
<h3 id="데이터베이스-내용-조회">데이터베이스 내용 조회</h3>
<pre><code>db.book.find()</code></pre><h3 id="특정-조건-찾기">특정 조건 찾기</h3>
<pre><code>db.book.find({bookid: 10} )</code></pre><h3 id="프로젝션-보고자-하는-필드-선택">프로젝션: 보고자 하는 필드 선택</h3>
<blockquote>
<h4 id="bookid와-bookname-필드만-보여줌">bookid와 bookname 필드만 보여줌</h4>
<pre><code>db.book.find({}, {bookid: 1, bookname: 1})</code></pre></blockquote>
<h4 id="bookid와-bookname을-제외한-필드만-보여줌">bookid와 bookname을 제외한 필드만 보여줌</h4>
<pre><code>db.book.find({}, {bookid: 0, bookname: 0})</code></pre><h4 id="dbbookfind-bookid-1-bookname-0은-안되지만-_id-0은-설정-가능함-기본적으로-그냥-붙는-objectid">db.book.find({}, {bookid: 1, bookname: 0})은 안되지만, _id: 0은 설정 가능함 (기본적으로 그냥 붙는 ObjectId)</h4>
<pre><code class="language-json">db.book.find( {bookname: '올림픽 이야기'}, {_id:0, bookid:1, bookname:1})</code></pre>
<h2 id="update">UPDATE</h2>
<h3 id="1-조건에-맞는-하나-업데이트">1. 조건에 맞는 하나 업데이트</h3>
<pre><code class="language-json">db.book.updateOne( { bookname: '올림픽 이야기'}, { $set: {price: 7000} })
{
  acknowledged: true,
  insertedId: null,
  matchedCount: 1,
  modifiedCount: 1,
  upsertedCount: 0
}</code></pre>
<h4 id="11번-아이디가-없을-때-업데이트---매치되는-값-찾지-못함">11번 아이디가 없을 때 업데이트 - 매치되는 값 찾지 못함</h4>
<pre><code class="language-json">db.book.updateOne( {bookid:11}, {$set: {price: 6000, saleprice:5000} })
{
  acknowledged: true,
  insertedId: null,
  matchedCount: 0,
  modifiedCount: 0,
  upsertedCount: 0
}</code></pre>
<h3 id="upsert">UPSERT</h3>
<blockquote>
<h4 id="upsert--true로-하면-조건에-맞는게-없어도-그냥-새로-만들어줌">upsert : true로 하면 조건에 맞는게 없어도 그냥 새로 만들어줌.</h4>
</blockquote>
<pre><code class="language-json">db.book.updateOne( {bookid:11}, {$set: {price: 6000, saleprice:5000}}, { upsert:true })</code></pre>
<h4 id="결과">결과</h4>
<pre><code class="language-json">{
  acknowledged: true,
  insertedId: ObjectId('66a1d225c0438061aade2c43'),
  matchedCount: 0,
  modifiedCount: 0,
  upsertedCount: 1
}</code></pre>
<h4 id="조회-1">조회</h4>
<pre><code class="language-json">{
    _id: ObjectId('66a1d225c0438061aade2c43'),
    bookid: 11,
    price: 6000,
    saleprice: 5000
  }</code></pre>
<h3 id="2-여러개-업데이트---모든-행-price-10씩-증가됨inc">2. 여러개 업데이트 - 모든 행 price 10씩 증가됨(inc)</h3>
<pre><code>db.book.updateMany({}, {$inc: {price:10} })</code></pre><hr />
<h2 id="쿼리-연산자">쿼리 연산자</h2>
<h3 id="비교">비교</h3>
<p>** $eq ** : 값과 같은 거 찾기(동등)<br /></p>
<pre><code class="language-json">db.book.find( {bookid: { $eq: 5 } })</code></pre>
<p>** $ne ** : 값이 같지 않은 거(not)</p>
<p>** $gt **: 값보다 큰 것들 찾기(초과)</p>
<pre><code class="language-json">db.book.find( {bookid: { $gt: 5 } })</code></pre>
<p>** $gte ** : 값도 포함해야 될때. (이상)</p>
<pre><code class="language-json">db.book.find( {price: {$gte: 20000} })</code></pre>
<p>** $lt ** : 미만
** $lte ** : 이하</p>
<p>** $in ** : 배열 내에서 값이 일치하는 것 찾기</p>
<blockquote>
<h4 id="조건-두개-넣을-때-15000원-이상-20000원-이하">조건 두개 넣을 때 (15000원 이상, 20000원 이하)</h4>
<pre><code>db.book.find( {price: {$gte: 15000, $lte: 20000} }</code></pre></blockquote>
<h3 id="논리적">논리적</h3>
<p>** $and *<em>: 여러 조건을 줄 때 쿼리가 모두 일치(and)
*</em> $or ** : 두 쿼리 중 일치하는게 있으면 반환(or)
** $nor ** : 두 쿼리가 모두 일치하지 않으면 반환(nor)
** $not ** : 쿼리와 일치하지 않으면 반환(not)</p>
<blockquote>
<h4 id="bookid가-6보다-작고-price가-20000보다-크거나-같은-거-찾기">bookid가 6보다 작고, price가 20000보다 크거나 같은 거 찾기</h4>
</blockquote>
<pre><code class="language-json">db.book.find(
... {
...     $and: [
...           { bookid: {$lt:6} },
...           { price: {$gte: 20000} }
...     ]
... })</code></pre>
<h3 id="평가">평가</h3>
<p>** $regex ** : 필드 값을 평가할 때 정규 표현식을 사용할 수 있음</p>
<ul>
<li>정규 표현식은 부분 문자열 일치를 기본으로 해서 명시적 와일드 카드 안써도 됨</li>
<li>** $options ** : 정규 표현식 옵션 설정<blockquote>
<p>i : 대소문자 구분하지 않고 검색
m : 여러 줄에 걸쳐 검색. 텍스트의 각 줄을 개별 문자열로 취급
s : .문자가 개행 문자(\n)을 포함한 모든 문자와 일치하는지 확인(여러줄에서 쓰임) 
x : 패턴 내 공백과 주석을 무시</p>
</blockquote>
</li>
</ul>
<p>** $text ** : 텍스트 검색 수행
** $ where ** : 자바스크립트 표현식을 사용하여 값 일치시킴</p>
<hr />
<h3 id="update-연산자">update 연산자</h3>
<p>** $unset ** : 컬럼을 삭제</p>
<blockquote>
<h4 id="bookid가-5보다-큰-데이터-price-컬럼-삭제">bookid가 5보다 큰 데이터 price 컬럼 삭제</h4>
</blockquote>
<pre><code class="language-json">db.book.updateMany(
... {
...     bookid: {$gt: 5}
... },
... {
...     $unset: {price: ''}
... })</code></pre>
<hr />
<h3 id="함수-형태의-sort-limit-skip">함수 형태의 sort(), limit(), skip()</h3>
<h4 id="sort">sort()</h4>
<p>sort({ 필드: 1 } ) : 1이면 오름차순, -1이면 내림차순</p>
<pre><code>db.book.find().sort( {price: 1})</code></pre><h4 id="limitn-n개만-가져오기">limit(n): n개만 가져오기</h4>
<p>5개만 가져오기</p>
<pre><code>db.book.find().limit(5)</code></pre><h4 id="skipn--데이터-스킵">skip(n) : 데이터 스킵</h4>
<p>offset은 없고 대신 skip(n)은 있음
skip(3).limit(5)면
아이디 1,2,3은 스킵하고 4부터 5개 데이터 가져옴</p>
<hr />
<h3 id="집계-파이프-라인aggregation">집계 파이프 라인(Aggregation)</h3>
<h3 id="aggregation">Aggregation</h3>
<p>  : $group, $sort, $limit, $match 등등등 각 스테이지(단계)를 나열해야 할 때(파이프라인)</p>
<ul>
<li>빅데이터에서 데이터를 걸러낼 때 사용</li>
</ul>
<blockquote>
<h3 id="price의-합-조회">price의 합 조회</h3>
</blockquote>
<h4 id="sql">SQL</h4>
<pre><code class="language-sql">  select sum(price) totalPrice from book;</code></pre>
<h4 id="mongodb-1">MongoDB</h4>
<pre><code class="language-json">db.book.aggregate(
... [
...     { $group: { _id: null, totalPrice: { $sum: &quot;$price&quot; } } }
... ])</code></pre>
<h4 id="결과-1">결과</h4>
<pre><code class="language-json">    [ { _id: null, totalPrice: 144500 } ]</code></pre>
<blockquote>
<h3 id="출판사-별-총-가격">출판사 별 총 가격</h3>
</blockquote>
<h4 id="sql-1">SQL</h4>
<pre><code class="language-sql">    select publisher, sum(price) totalPrice from group by publisher;</code></pre>
<h4 id="mongodb-2">MongoDB</h4>
<pre><code class="language-json">db.book.aggregate( [ { $group: { _id: &quot;$publisher&quot;, totalPrice: { $sum: &quot;$price&quot; } } }] )
[
  { _id: '굿스포츠', totalPrice: 21000 },
  { _id: '삼성당', totalPrice: 7500 },
  { _id: '나무수', totalPrice: 13000 },
  { _id: 'Pearson', totalPrice: 13000 },
  { _id: '대한미디어', totalPrice: 57000 },
  { _id: '이상미디어', totalPrice: 33000 }</code></pre>
<blockquote>
<h3 id="출판사-별-총-가격을-오름차순으로-정렬">출판사 별 총 가격을 오름차순으로 정렬</h3>
</blockquote>
<h4 id="sql-2">SQL</h4>
<pre><code class="language-sql">select publisher, sum(price) totalPrice from group by publisher order by totalPrice;</code></pre>
<h4 id="mongo">Mongo</h4>
<pre><code class="language-json">db.book.aggregate( [ { $group: { _id: &quot;$publisher&quot;, totalPrice: { $sum: &quot;$price&quot; } } }, { $sort: {totalPrice: 1}}] )</code></pre>
<blockquote>
<h4 id="bookid가-2보다-큰-것-중에-출판사-별-총-가격을-오름차순으로-정렬">bookid가 2보다 큰 것 중에 출판사 별 총 가격을 오름차순으로 정렬</h4>
</blockquote>
<pre><code class="language-json"># MongoDB
db.book.aggregate( [ { $match: { bookid: { $gt: 2 } } }, {$group: {_id: &quot;$publisher&quot;, totalPrice: {$sum: &quot;$price&quot;}}}, {$sort: {totalPrice:1}}])
# 결과
[
  { _id: '삼성당', totalPrice: 7500 },
  { _id: 'Pearson', totalPrice: 13000 },
  { _id: '굿스포츠', totalPrice: 14000 },
  { _id: '이상미디어', totalPrice: 33000 },
  { _id: '대한미디어', totalPrice: 57000 }
]</code></pre>
<hr />
<h2 id="자바-mongdb-연결">자바 MongDB 연결</h2>
<pre><code class="language-java">package com.mycom.myapp.dao;

import java.awt.print.Book;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;

import com.mongodb.client.MongoClient;
import com.mongodb.client.MongoClients;
import com.mongodb.client.MongoCollection;
import com.mongodb.client.MongoDatabase;
import com.mycom.myapp.dto.BookDto;
import org.bson.Document;
import org.bson.conversions.Bson;

import static com.mongodb.client.model.Filters.eq;
import static com.mongodb.client.model.Updates.combine;
import static com.mongodb.client.model.Updates.set;

// jdbc처럼 연결해줘야 함
public class BookDao {

    static final String CONNECTION_STRING = &quot;mongodb://localhost:27017&quot;; // db는 따로 씀
    static final String DATABASE_NAME = &quot;madangdb&quot;; // db 이름
    static final String COLLECTION_NAME = &quot;book&quot;; // 테이블 이름

    // username, password 필요 x

    private MongoClient mongoClient; // jdbc Connection 유사
    private MongoDatabase database;
    private MongoCollection&lt;Document&gt; collection; // import org.bson.Document

    // 생성자 mongodb 연결 초기화
    public BookDao() {
        try{
            this.mongoClient = MongoClients.create(CONNECTION_STRING); // Client's'임. s 있음
            this.database = this.mongoClient.getDatabase(DATABASE_NAME);
            this.collection = database.getCollection(COLLECTION_NAME);
        } catch (Exception e){
            e.printStackTrace();
        }
    }

    public List&lt;BookDto&gt; listBook(){
        List&lt;BookDto&gt; bookList = new ArrayList&lt;&gt;();

        // collection.find() : 반환 값 형변환 해야 함. iterable하므로 for each 가능
        for(Document doc : collection.find()){
            BookDto bookDto = new BookDto();
            bookDto.setBookId(doc.getInteger(&quot;bookid&quot;)); // document에서 bookid 필드를 가져옴
            bookDto.setBookName(doc.getString(&quot;bookname&quot;));
            bookDto.setPublisher(doc.getString(&quot;publisher&quot;));
            bookDto.setPrice(doc.getInteger(&quot;price&quot;));

            bookList.add(bookDto);
        }

        return bookList;
    }

    public List&lt;BookDto&gt; listBookSearch(String searchWord){
        List&lt;BookDto&gt; bookList = new ArrayList&lt;&gt;();

        Document filterDocument = new Document(&quot;$regex&quot;, searchWord).append(&quot;$options&quot;, &quot;i&quot;);
        Document bookNameFilter = new Document(&quot;bookname&quot;, filterDocument);

        for(Document doc : collection.find(bookNameFilter)){
            BookDto bookDto = new BookDto();
            bookDto.setBookId(doc.getInteger(&quot;bookid&quot;)); // document에서 bookid 필드를 가져옴
            bookDto.setBookName(doc.getString(&quot;bookname&quot;));
            bookDto.setPublisher(doc.getString(&quot;publisher&quot;));
            bookDto.setPrice(doc.getInteger(&quot;price&quot;));

            bookList.add(bookDto);
        }

        return bookList;
    }

    public BookDto detailBook(int bookid) {
        Document doc = collection.find( eq(&quot;bookid&quot;, bookid )).first();
        BookDto bookDto = null;

        if( doc != null ){
            bookDto = new BookDto();
            bookDto.setBookId(doc.getInteger(&quot;bookid&quot;)); // document에서 bookid 필드를 가져옴
            bookDto.setBookName(doc.getString(&quot;bookname&quot;));
            bookDto.setPublisher(doc.getString(&quot;publisher&quot;));
            bookDto.setPrice(doc.getInteger(&quot;price&quot;));

        }
        return bookDto;
    }

    public void insertBook(BookDto bookDto) {
        Document document = new Document(&quot;bookid&quot;, bookDto.getBookId())
                .append(&quot;bookname&quot;, bookDto.getBookName())
                .append(&quot;publisher&quot;, bookDto.getPublisher())
                .append(&quot;price&quot;, bookDto.getPrice());
        collection.insertOne(document);
    }

    public void updateBook(BookDto bookDto) {
        Bson filter = eq(&quot;bookid&quot;, bookDto.getBookId());
        Bson update = combine(
                set(&quot;bookname&quot;, bookDto.getBookName()),
                set(&quot;publisher&quot;, bookDto.getPublisher()),
                set(&quot;price&quot;, bookDto.getPrice())
        );
        collection.updateOne(filter, update);
    }

    public void deleteBook(int bookid) {
        collection.deleteOne(eq(&quot;bookid&quot;, bookid));
    }
}</code></pre>