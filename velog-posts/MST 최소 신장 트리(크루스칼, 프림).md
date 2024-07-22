<h2 id="mst최소-신장-트리">MST(최소 신장 트리)</h2>
<blockquote>
<h4 id="신장트리">신장트리</h4>
</blockquote>
<ul>
<li>무향(방향이 없다</li>
<li>사이클 x</li>
<li>-&gt; 여기서 비용이 최소인 것이 <strong>최소 신장 트리</strong>(모두 연결 최소 비용)</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/953038cb-3936-4c60-8306-1aad17c95d4c/image.png" />
이러한 사이클이 없어야 함.</p>
<h3 id="크루스칼-알고리즘">크루스칼 알고리즘</h3>
<p>어떻게 연결해야만 사이클이 안생기고 최소로 연결할 수 있는지
간선을 따라 최소 비용인 정점을 탐색</p>
<ol>
<li>서로소 집합을 이용해 사이클 여부를 확인할 수 있음</li>
<li>싼 노드랑 비싼 노드 정렬 -&gt; 싼 노드 선택해 연결 반복(모두 연결할 때까지 V-1번/사이클 생기면 버려야 됨)</li>
</ol>
<blockquote>
<h4 id="서로소-집합">서로소 집합</h4>
<p>공통 원소가 없는 두 집합</p>
</blockquote>
<ul>
<li>union(합집합으로 만듬), find(특정 원소가 속한 집합이 무엇인지) 연산으로 조작 가능</li>
</ul>
<p>** 서로소 집합 findSet()에서 parent[] 배열에 관해**
<img alt="" src="https://velog.velcdn.com/images/jhh0830/post/634df8f1-7c51-44d1-b5f8-146293f70eb6/image.png" /></p>
<p>findSet(4) = 1, findSet(5) =1 처럼 같은 값을 가지면 이미 그 집합에 속해있다.
-&gt; 이때 4와 5를 연결하면 사이클이 생김
<img alt="" src="https://velog.velcdn.com/images/jhh0830/post/96d60dc0-d9f0-448d-9952-2b8e872055fe/image.png" /></p>
<p>서로 다른 집합일 때 union을 해야함</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/2486280c-0d61-4183-96a8-05f6a902e1cd/image.png" /></p>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/e34a06c9-aa8d-4983-875f-6fc9cbf225fe/image.png" /></p>
<h3 id="프림-알고리즘">프림 알고리즘</h3>
<p>시작점 하나를 잡고 가장 가까운 노드를 찾아가 V를 만들어 가는 알고리즘</p>
<ul>
<li>갈 수 있는 노드 중에 가장 비용이 적은 정점을 고름 -&gt; 우선순위 큐 사용
(다익스트라와 비슷 - 하지만 꺼낸 다음에 cost를 만들 필요는 없다)</li>
<li>노드 중심으로 탐색하여 가장 비용이 적은 정점을 선택</li>
</ul>
<p>-&gt; 간선이 많은 경우는 프림 알고리즘, 간선이 적은 경우는 크루스칼 알고리즘이 유리함</p>
<hr />
<p>조별시간에 프림과 다익스트라가 어떤 점이 다른가에 대해 의문이 생겨 토론하였다.</p>
<h3 id="프림-vs-다익스트라">프림 vs 다익스트라</h3>
<p>프림</p>
<ul>
<li>최소 신장 트리를 만드는 것으로</li>
<li>각 정점에서 자신과 연결된 간선의 가중치를 저장
<img alt="" src="https://velog.velcdn.com/images/jhh0830/post/7bc2fdae-7997-4e98-a980-71faf74e4998/image.png" /></li>
</ul>
<p>다익스트라</p>
<ul>
<li>한 정점에 대한 최단 경로를 구함</li>
<li>시작 정점이 바뀔때마다 최단 경로가 바뀜
<img alt="" src="https://velog.velcdn.com/images/jhh0830/post/0e36ff6d-b4ce-411c-be70-f552b9796586/image.png" /></li>
</ul>
<p><a href="https://velog.io/@sugyeonghh/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%ED%94%84%EB%A6%BC-%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98%EA%B3%BC-%EB%8B%A4%EC%9D%B5%EC%8A%A4%ED%8A%B8%EB%9D%BC%EC%9D%98-%EC%B0%A8%EC%9D%B4">https://velog.io/@sugyeonghh/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%ED%94%84%EB%A6%BC-%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98%EA%B3%BC-%EB%8B%A4%EC%9D%B5%EC%8A%A4%ED%8A%B8%EB%9D%BC%EC%9D%98-%EC%B0%A8%EC%9D%B4</a></p>
<hr />
<h2 id="백준-도시-분할-계획">백준 도시 분할 계획</h2>
<p><a href="https://www.acmicpc.net/problem/1647">https://www.acmicpc.net/problem/1647</a>
골드 4, MST 문제다.
<img alt="" src="https://velog.velcdn.com/images/jhh0830/post/115fedef-36a0-4d86-b521-14ba59469f7c/image.png" /></p>
<p>문제를 처음에 이해를 못했었다.
아예 연결되어있지 않은 상태에서 마을을 2개로 분할하는 방법을 모두 따져야 하는건가 생각했다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/031c92b6-e6d8-431e-abd3-b46e27c6ef15/image.png" />
만약 최소 신장 트리가 위와 같이 구성된다면 위 상태에서 마을을 2개로 분할하면 된다.</p>
<p>하지만 마을 유지비가 최소 비용이 되어야 한다.
이 말은 MST에서 최대 비용인 길을 끊어내 마을을 분할시킨다는 뜻이 된다.</p>
<p><img alt="" src="https://velog.velcdn.com/images/jhh0830/post/145bf187-fa0d-43a7-b328-dd05b15076af/image.png" />
그래서 가장 값이 큰 6인 길을 없애면 마을이 위와 같이 2개로 만들어진다.</p>
<p>그래서 총 유지비는 1+2+3+4+5가 된다.</p>
<p>코드 풀이도 MST 최소 신장 트리를 만들어 비용을 모두 합친 다음 그 중 최대 비용을 빼는 식으로 구하였다.
아래는 크루스칼 알고리즘을 사용하였다.</p>
<pre><code class="language-java">package _240702;

import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.StringTokenizer;

public class BOJ_도시분할계획 {
    static Edge[] edges;
    static int N, M, result;
    static int parent[];
    static class Edge{
        int v1, v2, c;
        Edge(int v1, int v2, int c){
            this.v1 = v1;
            this.v2 = v2;
            this.c = c;
        }
    }

    public static void main(String[] args) throws Exception{
        BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(System.in));
        StringTokenizer stringTokenizer = new StringTokenizer(bufferedReader.readLine());
        N = Integer.parseInt(stringTokenizer.nextToken());
        M = Integer.parseInt(stringTokenizer.nextToken());

        parent = new int[N+1];
        edges = new Edge[M];

        for(int i=0;i&lt;M;i++){
            stringTokenizer = new StringTokenizer(bufferedReader.readLine());
            int A = Integer.parseInt(stringTokenizer.nextToken());
            int B = Integer.parseInt(stringTokenizer.nextToken());
            int C = Integer.parseInt(stringTokenizer.nextToken());
            edges[i] = new Edge(A, B, C);
        }

        Arrays.sort(edges, (n1, n2) -&gt; n1.c - n2.c);
        makeSet();

        int cnt = 0;
        int max = 0;
        for(Edge edge : edges){
            if(union(edge.v1, edge.v2)){
                result += edge.c;
                max = Math.max(max, edge.c);
                cnt++;
            }
            if(cnt == N-1){
                break;
            }
        }
        System.out.println(result - max);
    }

    static void makeSet(){
        for(int i=0;i&lt;N;i++){
            parent[i] = i;
        }
    }

    static int findSet(int x){
        if(parent[x] == x) return x;
        return parent[x] = findSet(parent[x]);
    }

    static boolean union(int x, int y){
        int px = findSet(x);
        int py = findSet(y);

        if(px == py) return false;

        if(px &lt; py) parent[py] = px;
        else parent[px] = py;

        return true;
    }
}</code></pre>