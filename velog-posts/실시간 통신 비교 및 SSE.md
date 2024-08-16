<h1 id="시작하며">시작하며,</h1>
<p>프로젝트에서 가져온 데이터를 수치에 따라 색상으로 표시해주는 기능이 있다. 이 데이터는 시간에 따라 달라지는데 기존 UI에서는 시간에 따른 변화를 볼 수가 없었다. 
그래서 실시간 통신의 필요성을 느끼고 실시간 통신 방법에 대해 찾아보았다.</p>
<p>처음에 언급이 되었던 해결방법은 새로고침 버튼을 만들어서 사용자가 서버에 요청을 보내는 것이다.
하지만 이 기능은 우리 핵심 기능이므로 좀 더 사용자 입장에서 편리한 방법을 원했다.</p>
<hr />
<h1 id="실시간-통신-방식">실시간 통신 방식</h1>
<h2 id="polling">polling</h2>
<p>요청을 주기적으로 계속 보내면서 서버가 전달하고자 하는 내용이 있는지, 이벤트가 발생했는지 확인해서 가져온다.</p>
<ul>
<li>HTTP 오버헤드가 증가한다는 단점 있음.</li>
</ul>
<h2 id="sseserver-sent-event">SSE(Server-Sent-Event)</h2>
<p>클라이언트 요청 없이 서버에서 클라이언트로 <code>단방향 통신</code>을 함
WebSocket과 다르게 별도의 프로토콜을 구축할 필요 없음</p>
<h2 id="websocket">WebSocket</h2>
<p>HTTP 통신이 아닌 TCP에서 동작하고, WS 혹은 WSS라는 통신 프로토콜로 <code>양방향 통신</code>과 영구성을 지님
stateful하고 polling처럼 주기적으로 요청을 받을 필요 없음</p>
<p><a href="https://4sii.tistory.com/325">https://4sii.tistory.com/325</a>
<a href="https://velog.io/@kkimbj18/%EC%8B%A4%EC%8B%9C%EA%B0%84-%ED%86%B5%EC%8B%A0-%EA%B8%B0%EC%88%A0">https://velog.io/@kkimbj18/%EC%8B%A4%EC%8B%9C%EA%B0%84-%ED%86%B5%EC%8B%A0-%EA%B8%B0%EC%88%A0</a></p>
<hr />
<p>위 방법 중에서 <code>SSE</code>와 <code>WebSocket</code> 중 고민했다.
더 찾아보니 <code>WebSocket</code>은 채팅 프로그램 같이 <code>양방향이 실시간 통신</code>이 필요한 경우 사용하고, <code>SSE</code>는 <code>주식 시세 표시 같은 용도</code>에 사용하기 효율적이라고 한다.</p>
<p>우리 프로젝트는 <strong>시간에 따라 쌓인 데이터를 보여주는 목적</strong>이기 때문에 <code>SSE</code>를 선택했다.</p>
<hr />
<h1 id="sse">SSE</h1>
<h3 id="controller">controller</h3>
<pre><code class="language-java">@GetMapping(value = &quot;/sse&quot;, produces = MediaType.TEXT_EVENT_STREAM_VALUE)
    public SseEmitter sseEmitterExample(@RequestParam int x, @RequestParam int y) throws Exception{
        SseEmitter sseEmitter = new SseEmitter(Long.MAX_VALUE); // 파라미터로 timeout 시간
        sseService.addEmitter(sseEmitter);
        sseService.addGrid(x, y);
        sseService.sendEvents();

        return sseEmitter; // 메세지가 발생할 때마다 클라이언트에 반환

    }</code></pre>
<blockquote>
<p><code>MediaType.TEXT_EVENT_STREAM_VALUE</code> 이 content-type 헤더에 들어오면 sse 요청인 것을 알고 한번에 연결을 끊지 않음</p>
</blockquote>
<blockquote>
<p><code>SseEmitter sseEmitter = new SseEmitter(Long.MAX_VALUE)</code>
-&gt; sse event를 위한 객체. 
-&gt; 파라미터로 timeout 시간 넣을 수 있음</p>
</blockquote>
<blockquote>
<p><code>return sseEmitter</code> : emitter 객체를 반환하면 메세지가 발생할 때마다 클라이언트에 반환하게 된다.</p>
</blockquote>
<h3 id="service">service</h3>
<pre><code class="language-java">public void addEmitter(SseEmitter emitter) {
        // 콜백함수 지정
        emitter.onTimeout(() -&gt; emitter.complete()); // 타임아웃 발생
        emitter.onCompletion(() -&gt; emitters.remove(emitter)); // 완료
        emitter.onTimeout(() -&gt; emitters.remove(emitter)); // 에러 발생

        emitters.add(emitter); // 현재 emitter 저장

    }

@Scheduled(fixedRate = 60000) // 60 * 1000 = 60,000 -&gt; 1분 주기 스케줄러
    public void sendEvents() {

        for (SseEmitter emitter : emitters) {
            try {
                ModelInput modelInput = modelDataService.ModelData(gridDto.getX(), gridDto.getY()); // input data

                // 비동기적으로 모델 예측 결과를 가져와 처리
                predictionApiService.postModelServer(modelInput).subscribe(
                        modelResponse -&gt; {
                            try {
                                // ModelResponse를 JSON으로 변환
                                String json = objectMapper.writeValueAsString(modelResponse);

                                // 클라이언트에 전송
                                emitter.send(json);
                                System.out.println(&quot;전송: &quot; + json);
                            } catch (IOException e) {
                                e.printStackTrace();
                                emitter.complete();
                                emitters.remove(emitter);
                            }
                        },
                        error -&gt; {
                            // 에러
                            System.err.println(&quot;Error: &quot; + error.getMessage());
                            emitter.complete();
                            emitters.remove(emitter);
                        }
                );

            } catch (Exception e) {
                e.printStackTrace();
                emitter.complete();
                emitters.remove(emitter);
            }
        }
    }    
</code></pre>
<blockquote>
<p><code>emitters.add(emitter)</code> : emitter 레포지토리에 현재 emitter 저장</p>
</blockquote>
<blockquote>
<p><code>emitter.complete()</code> : SSE 연결 정상적으로 종료</p>
</blockquote>
<blockquote>
<p><code>emitter.remove()</code> : 사용하지 못하는 SseEmitter를 emitters 레포지토리에서 삭제하기 위해</p>
</blockquote>
<p><a href="https://youtu.be/Fjj4ZmLlrP8?si=rvi4Vj91GdxFv9jF">https://youtu.be/Fjj4ZmLlrP8?si=rvi4Vj91GdxFv9jF</a>
위 영상을 참고하고 개념을 잡은 다음 우리 서비스에 맞게 구현하였다.</p>