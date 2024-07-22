<h2 id="arraylist와-vector-비교---thread-safe">ArrayList와 Vector 비교 - Thread Safe</h2>
<p>ArrayList
쓰레드 세이프 하지 않음 
-&gt; 쓰레드 동시 자원 되면 이상한 결과가 나올 수 있음 
-&gt; 동기화 해서 문제 없게 해야 됨
Vector
쓰레드 세이프함 
-&gt; 메모리 차지 무거움 </p>
<h1 id="stack--queue">Stack / Queue</h1>
<h2 id="arraydeque">ArrayDeque</h2>
<p>stack 대신 ArrayDeque -&gt; 더 가볍고 빨라짐
Queue 대신 ArrayDeque -&gt; 더 빠름</p>
<p>인터페이스는 스택/큐로, 실제 구현체는 ArrayDeque로
Stack s = new ArrayDeque -&gt; 스택
Queue q = new ArrayDeque -&gt; 큐</p>
<h2 id="우선순위-큐-priorityqueue">우선순위 큐 PriorityQueue</h2>
<ul>
<li>우선순위 - 정렬된 결과를 추출</li>
<li>heap으로 이루어짐</li>
<li>원점에서 가장 가까운, 비용이 가장 싼 것부터, 등의 문제에서 사용 가능 (최단거리)</li>
</ul>
<blockquote>
<h3 id="우선순위-큐-에러">우선순위 큐 에러</h3>
<p>Exception in thread &quot;main&quot; java.lang.ClassCastException: class _240626.우선순위큐$Node cannot be cast to class java.lang.Comparable (_240626.우선순위큐$Node is in unnamed module of loader 'app'; java.lang.Comparable is in module java.base of loader 'bootstrap')
    at java.base/java.util.PriorityQueue.siftUpComparable(PriorityQueue.java:643)
    at java.base/java.util.PriorityQueue.siftUp(PriorityQueue.java:639)
    at java.base/java.util.PriorityQueue.offer(PriorityQueue.java:330)
    at _240626.우선순위큐.main(우선순위큐.java:38)</p>
</blockquote>
<p>-&gt; 우선순위에 정렬 조건을 줘야 함: 
Comparable, anonymous 객체, lamda를 사용</p>
<pre><code class="language-java">PriorityQueue&lt;Node&gt; priorityQueue2 = new PriorityQueue&lt;&gt;();
        priorityQueue2.offer(new Node(3,5,2));
        priorityQueue2.offer(new Node(7,1,4));
        priorityQueue2.offer(new Node(5,2,9));
        priorityQueue2.offer(new Node(1,1,5));

        while(!priorityQueue2.isEmpty()){
            System.out.println(priorityQueue2.poll());
        }
    }
    static class Node{
        int y, x, c;
        Node(int y, int x, int c){
            this.y=y;
            this.x=x;
            this.c=c;
        }

        @Override
        public String toString() {
            return &quot;Node{&quot; +
                    &quot;y=&quot; + y +
                    &quot;, x=&quot; + x +
                    &quot;, c=&quot; + c +
                    '}';
        }</code></pre>
<h3 id="comparable-방법">comparable 방법</h3>
<pre><code class="language-java">static class Node implements Comparable&lt;Node&gt; {
        int y, x, c;
        Node(int y, int x, int c){
            this.y=y;
            this.x=x;
            this.c=c;
        }

        @Override
        public String toString() {
            return &quot;Node{&quot; +
                    &quot;y=&quot; + y +
                    &quot;, x=&quot; + x +
                    &quot;, c=&quot; + c +
                    '}';
        }

        @Override
        public int compareTo(Node o) {
            return this.y - o.y;
        }
    }</code></pre>
<h3 id="lamda-방법">lamda 방법</h3>
<pre><code class="language-java">PriorityQueue&lt;Node&gt; priorityQueue3 = new PriorityQueue&lt;&gt;( (n1, n2) -&gt; n1.y - n2.y );
        priorityQueue3.offer(new Node(3,5,2));
        priorityQueue3.offer(new Node(7,1,4));
        priorityQueue3.offer(new Node(5,2,9));
        priorityQueue3.offer(new Node(1,1,5));

        while(!priorityQueue3.isEmpty()){
            System.out.println(priorityQueue3.poll());
        }</code></pre>
<p>람다식으로  y가 같을 경우 x 오름차순 처리</p>
<pre><code class="language-java">PriorityQueue&lt;Node&gt; priorityQueue3 = new PriorityQueue&lt;&gt;((n1, n2) -&gt; {
            if (n1.y == n2.y) {
                return n1.x - n2.x;
            } else {
                return n1.y - n2.y;
            }
        });
    }</code></pre>
<h3 id="node-클래스가-아닌-배열로-우선순위큐-구현">Node 클래스가 아닌 배열로 우선순위큐 구현</h3>
<pre><code class="language-java">// 배열
        PriorityQueue&lt;int[]&gt; priorityQueue4 = new PriorityQueue&lt;&gt;( (a1, a2) -&gt; a1[0] - a2[0]);
        priorityQueue4.offer(new int[] { 3, 5, 2 });
        priorityQueue4.offer(new int[] { 7, 1, 4 });
        priorityQueue4.offer(new int[] { 5, 2, 9 });
        priorityQueue4.offer(new int[] { 1, 1, 5 });

        while(!priorityQueue4.isEmpty()){
            System.out.println(Arrays.toString(priorityQueue4.poll()));
        }</code></pre>
<hr />
<h1 id="dfsbfs">DFS/BFS</h1>
<ol>
<li>인접행렬 - 정점
2차원 배열에 각 노드가 연결된 형태를 기록</li>
</ol>
<ul>
<li>간선이 가중치, 비용을 가지고 있으면 인접행렬에 T/F가 아니라 값을 넣어줌
<img alt="" src="https://velog.velcdn.com/images/jhh0830/post/a15af10e-d91f-413b-966f-51f8d924063a/image.png" /></li>
<li>방향이 있는 그래프(연결+가중치+방향)
<img alt="" src="https://velog.velcdn.com/images/jhh0830/post/8f7171cc-7c7f-41ba-ad7d-1a5be3414e19/image.png" />
<img alt="" src="https://velog.velcdn.com/images/jhh0830/post/6a2b46ed-8a84-40d0-8029-8b8c4b472e10/image.png" /></li>
</ul>
<ol start="2">
<li>인접리스트 - 정점</li>
</ol>
<ul>
<li>기본 / 비용 / 비용 + 방향
<img alt="" src="https://velog.velcdn.com/images/jhh0830/post/86af963b-24a3-42d8-aeb3-85b752b9356f/image.png" /></li>
</ul>
<h3 id="인접행렬-인접리스트-사용-구분">인접행렬, 인접리스트 사용 구분</h3>
<p>-&gt; 간선 개수, 비중에 따라 인접행렬, 인접리스트 결정
-&gt; 인접행렬이 무조건 메모리 낭비가 심하다는 아님. 조건 비교해야됨</p>
<h4 id="인접행렬">인접행렬</h4>
<p>노드가 많지 않고 간선이 많은 경우</p>
<h4 id="인접리스트">인접리스트</h4>
<p>노드가 많고 간선이 적은 경우</p>
<ol start="3">
<li>간선 리스트 - 간선</li>
</ol>
<h2 id="dfs--bfs">DFS &amp; BFS</h2>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/16352f07-27eb-41ce-921f-52c70acf743d/image.png" /></p>
<h3 id="인접-행렬">인접 행렬</h3>
<ul>
<li>2차원 행렬 사용<pre><code class="language-java">package _240626;
</code></pre>
</li>
</ul>
<p>import java.util.ArrayDeque;
import java.util.Queue;</p>
<p>public class bfsdfs_matrix {
    // 정점 1, 2, 3, 4
    // 연결 정보
    // 1 -&gt; 2, 4
    // 2 -&gt; 3, 4
    // 3 -&gt; 2
    // 4 -&gt; 3</p>
<pre><code>static boolean[][] matrix;
static boolean[] visit; // 중복 방문 방지
static boolean[] visit1;
public static void main(String[] args) {
    // 자료구조
    matrix = new boolean[5][5]; // 0 dummy
    matrix[1][2] = true;
    matrix[1][4] = true;
    matrix[2][3] = true;
    matrix[2][4] = true;
    matrix[3][2] = true;
    matrix[4][3] = true;

    visit = new boolean[5];
    visit1 = new boolean[5];

    bfs(1);
    System.out.println(&quot;&quot;);
    dfs(1);
}

static void bfs(int n){ // n: 시작 정점
    Queue&lt;Integer&gt; queue = new ArrayDeque&lt;&gt;();
    queue.offer(n);
    visit[n] = true;

    while(!queue.isEmpty()){
        // 정점을 꺼낸다.
        int v = queue.poll();
        // 필요한 처리를 수행한다.
        System.out.print(v + &quot; -&gt; &quot;);

        // 계속 방문을 이어간다.
        for(int i=1;i&lt;=4;i++){
            if(!matrix[v][i] || visit[i]){
                continue;
            }
            queue.offer(i);
            visit[i] = true;
        }
    }
}

// stack 대신 동일한 효과 더 편한 재귀 호출을 이용
static void dfs(int n){ // n: 시작 정점
    visit1[n] = true;

    System.out.print(n + &quot; -&gt; &quot;);

    // 계속 방문을 이어간다.
    for(int i=1;i&lt;=4;i++){
        if(!matrix[n][i] || visit1[i]){
            continue;
        }
        dfs(i);
    }
}</code></pre><p>}</p>
<pre><code>### 인접리스트
- 2차원 ArrayList 사용
```java
package _240626;

import java.util.ArrayDeque;
import java.util.ArrayList;
import java.util.List;
import java.util.Queue;

public class bfsdfs_list {
    // 정점 1, 2, 3, 4
    // 연결 정보
    // 1 -&gt; 2, 4
    // 2 -&gt; 3, 4
    // 3 -&gt; 2
    // 4 -&gt; 3

     static List&lt;List&lt;Integer&gt;&gt; adjList = new ArrayList&lt;&gt;();
    static boolean[] visit; // 중복 방문 방지
    static boolean[] visit1;
    public static void main(String[] args) {
        // 자료구조
        for(int i=0;i&lt;5;i++){ // 0 dummy List
            adjList.add(new ArrayList&lt;Integer&gt;()); // 0, 1, 2, 3, 4 ArrayList 객체가 만들어짐
        }

        adjList.get(1).add(2);
        adjList.get(1).add(4);
        adjList.get(2).add(3);
        adjList.get(2).add(4);
        adjList.get(3).add(2);
        adjList.get(4).add(3);

        visit = new boolean[5];
        visit1 = new boolean[5];

        bfs(1);
        System.out.println(&quot;&quot;);
        dfs(1);
    }

    static void bfs(int n){ // n: 시작 정점
        Queue&lt;Integer&gt; queue = new ArrayDeque&lt;&gt;();
        queue.offer(n);
        visit[n] = true;

        while(!queue.isEmpty()){
            // 정점을 꺼낸다.
            int v = queue.poll();
            // 필요한 처리를 수행한다.
            System.out.print(v + &quot; -&gt; &quot;);

            // 계속 방문을 이어간다.
            List&lt;Integer&gt; list = adjList.get(v);
            for(Integer i : list){
                if(visit[i]){
                    continue;
                }
                queue.offer(i);
                visit[i] = true;
            }
        }
    }

    // stack 대신 동일한 효과 더 편한 재귀 호출을 이용
    static void dfs(int n){ // n: 시작 정점
        visit1[n] = true;

        System.out.print(n + &quot; -&gt; &quot;);

        // 계속 방문을 이어간다.
        List&lt;Integer&gt; list = adjList.get(n);
        for(Integer i : list){
            if(visit1[i]){
                continue;
            }
            dfs(i);
        }
    }
}</code></pre><h2 id="최단-경로-문제--가장-짧게-걸리는--">최단 경로 문제 ( 가장 짧게 걸리는 ~ )</h2>
<ol>
<li>가중치가 없을 때 - 가중치가 다 똑같음</li>
<li>bfs로 풀어야 됨. (dfs x)</li>
</ol>
<hr />
<h2 id="백준-dfs와-bfs---실버2">백준 DFS와 BFS - 실버2</h2>
<p>인접리스트 사용
재귀(dfs)와 큐(bfs)로 풀이함
입력 값이 정렬되서 들어오지 않기 때문에 입력된 값을 정렬 시켜줘야 함!</p>
<pre><code class="language-java">package _240626;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.nio.Buffer;
import java.util.*;

public class BOJ_1260 {
    static List&lt;List&lt;Integer&gt;&gt; list = new ArrayList&lt;&gt;();
    static boolean[] visit;
    static boolean[] visit1;
    static int N, M;
    static StringBuilder stringBuilder = new StringBuilder();

    public static void main(String[] args) throws IOException {
        BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(System.in));
        StringTokenizer stringTokenizer = new StringTokenizer(bufferedReader.readLine());
        N = Integer.parseInt(stringTokenizer.nextToken()); // 정점
        M = Integer.parseInt(stringTokenizer.nextToken()); // 간선
        int V = Integer.parseInt(stringTokenizer.nextToken()); // 탐색 시작 번호

        visit = new boolean[N+1];
        visit1 = new boolean[N+1];

        for(int i=0;i&lt;=N;i++) {
            list.add(new ArrayList&lt;Integer&gt;());
        }

        for(int i=0;i&lt;M;i++){
            stringTokenizer = new StringTokenizer(bufferedReader.readLine());
            int a = Integer.parseInt(stringTokenizer.nextToken());
            int b = Integer.parseInt(stringTokenizer.nextToken());
            list.get(a).add(b);
            list.get(b).add(a);
        }

        for(int i=0;i&lt;=N;i++){
            Collections.sort(list.get(i));
        }

        dfs(V);
        stringBuilder.append(&quot;\n&quot;);
        stringBuilder.append(V).append(&quot; &quot;);
        bfs(V);
        System.out.println(stringBuilder);
    }

    static void dfs(int node){
        visit[node] = true;
        stringBuilder.append(node).append(&quot; &quot;);
        List&lt;Integer&gt; nodelist = list.get(node);
        for(Integer i : nodelist){
            if(visit[i]) continue;
            dfs(i);
        }
    }

    static void bfs(int node){
        Queue&lt;Integer&gt; queue = new ArrayDeque&lt;&gt;();
        visit1[node] = true;
        queue.offer(node);

        while(!queue.isEmpty()){
            int num = queue.poll();
            List&lt;Integer&gt; nodeList = list.get(num);
            for(Integer i : nodeList){
                if(visit1[i]) continue;
                stringBuilder.append(i).append(&quot; &quot;);

                visit1[i] = true;
                queue.offer(i);
            }
        }
    }
}</code></pre>
<h2 id="백준-골드4-알고스팟">백준 골드4 알고스팟</h2>
<p>알고리즘 팀 스터디</p>
<p>최단거리를 계산하기 위해 bfs와 우선순위 큐를 사용하여 벽을 부수는 횟수가 가장 적은 것부터 탐색함
dx, dy 배열을 사용하여 현재 노드의 상하좌우를 탐색하여 방문하지 않은 경우 큐에 넣음
상하좌우 계산된 좌표와 벽이 있으면 cnt + 1, 벽이 없으면 cnt로 큐에 저장
노드가 N과 M에 도달할 때까지 탐색하고 그때 cnt를 출력</p>
<pre><code class="language-java">package _240626;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.*;

public class 알고스팟 {
    static int matrix[][], visit[][];
    static int cnt = 0, N, M;
    static int dx[] = { 1, 0, -1, 0};
    static int dy[] = {0, 1, 0, -1};

    public static void main(String[] args) throws IOException {
        BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(System.in));
        StringTokenizer stringTokenizer = new StringTokenizer(bufferedReader.readLine());

        N = Integer.parseInt(stringTokenizer.nextToken());
        M = Integer.parseInt(stringTokenizer.nextToken());

        matrix = new int[N+1][M+1];
        visit = new int[N+1][M+1];

        for(int i=1;i&lt;=M;i++){
            String str = bufferedReader.readLine();
            for(int j=1;j&lt;=N;j++){
                matrix[j][i] = Integer.parseInt(str.split(&quot;&quot;)[j-1]);
            }
        }
        bfs(1, 1);
        System.out.println(cnt);
    }

    static void bfs(int x, int y){
        PriorityQueue&lt;Node&gt; queue = new PriorityQueue&lt;&gt;((p1, p2) -&gt; p1.cnt - p2.cnt);
        queue.offer(new Node(x, y, 0));
        visit[x][y] = 1;

        while(!queue.isEmpty()){
            Node node = queue.poll();

            // 도착점
            if(node.x == N &amp;&amp; node.y == M){
                cnt = node.cnt;
                break;
            }

            for(int i=0;i&lt;4;i++){
                int nx = node.x + dx[i];
                int ny = node.y + dy[i];

                if(nx &lt;= 0 || ny &lt;= 0 || nx &gt; N || ny &gt; M) continue;

                // 벽이 없는 곳
                if(matrix[nx][ny] == 0 &amp;&amp; visit[nx][ny] == 0) {
                    queue.offer(new Node(nx, ny, node.cnt));
                    visit[nx][ny] = 1;
                }

                // 벽이 있는 곳
                if(matrix[nx][ny] == 1 &amp;&amp; visit[nx][ny] == 0){
                    queue.offer(new Node(nx, ny, node.cnt + 1));
                    visit[nx][ny] = 1;
                }
            }
        }
    }

    static class Node{
        int x, y, cnt;

        public Node(int x, int y, int cnt) {
            this.x = x;
            this.y = y;
            this.cnt = cnt;
        }


    }

}</code></pre>