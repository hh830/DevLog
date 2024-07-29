<h2 id="설치">설치</h2>
<blockquote>
<p>pip install fastapi
pip install &quot;uvicorn[standard]&quot;</p>
</blockquote>
<h2 id="서버-실행">서버 실행</h2>
<p>서버 실행을 위해 대충 main.py를 만들어 봤다.</p>
<pre><code class="language-python">from fastapi import FastAPI # FastAPI import

app = FastAPI()

@app.get(&quot;/&quot;)
async def root():
    return {&quot;message&quot;: &quot;Hello World&quot;}</code></pre>
<p>app = FastAPI() 필수.
main.py를 만들었으면 아래를 실행해준다.</p>
<blockquote>
<p>uvicorn main:app --reload
main은 파일명, app은 app = FastAPI()를 받은 변수</p>
</blockquote>
<h2 id="확인">확인</h2>
<p><a href="http://127.0.0.1:8000%EC%97%90">http://127.0.0.1:8000에</a> 들어가서 확인 가능하다.
<img alt="" src="https://velog.velcdn.com/images/jhh0830/post/adcea231-0245-47fe-814f-78146488f2ff/image.png" /></p>
<h2 id="api-문서">API 문서</h2>
<p>자동으로 업데이트 됨
<a href="http://127.0.0.1:8000/docs">http://127.0.0.1:8000/docs</a> : swagger ui 제공
<img alt="" src="https://velog.velcdn.com/images/jhh0830/post/543d1db6-b3ab-462a-bea2-fb4c57f84130/image.png" /></p>
<p><a href="http://127.0.0.1:8000/redoc">http://127.0.0.1:8000/redoc</a> : ReDoc 제공</p>
<hr />
<h2 id="post-요청">POST 요청</h2>
<h3 id="pydantic">Pydantic</h3>
<p>ResponseBody 받을 값에 대해 설정해준다.</p>
<pre><code class="language-python">from pydantic import BaseModel
class Item(BaseModel):
    name: str
    price: float
    is_offer: Union[bool, None] = None</code></pre>
<p>여기서 Union은 해당 값이 bool이거나 None일 수 있다는 뜻이다.
즉, 여러 타입을 가질 때 사용한다.</p>
<h3 id="model_dump">model_dump()</h3>
<pre><code class="language-python">@app.post(&quot;/model-data&quot;)
async def createContents(item : inputData): 
    item_dict = item.model_dump() # Pydantic 모델을 딕셔너리로 변환
    return item_dict</code></pre>
<p>받아온 item을 딕셔너리 형태로 바꿔준다.
원래는 item.dict()를 사용했지만 최신 버전에서는 item.model_dump()로 바뀌었다고 한다.
이를 사용하면 item.name 이런식으로 접근 가능</p>
<hr />
<h2 id="비동기와-동기">비동기와 동기</h2>
<h3 id="비동기">비동기</h3>
<p>어떤 코드가 끝날 때까지 기다리지 않고 나머지 코드를 먼저 실행하는 것
빠른 페이지 로딩을 위해 웹 개발에서 비동기 작업이 사용됨
동시성이라고도 불림 - 병렬성은 아님</p>
<h3 id="동기">동기</h3>
<p>어떤 코드가 완료될 때까지 대기하며 로직의 순서대로 수행되는 것</p>
<h3 id="비동기-vs-병렬성">비동기 vs 병렬성</h3>
<p>비동기는 작업이 완료될 때까지 기다리지 않고 다음 작업을 수행하는 것이고,
병렬성은 여러 작업을 동시에 실행하는 차이(동시에 실행할 뿐이지 대기가 있다)</p>
<p>웹 페이지는 비동기가 유리하고,
머신러닝과 같이 CPU에 묶인 작업을 할 때 병렬성과 멀티 프로세싱이 유리하다.</p>
<h3 id="async">async</h3>
<p>비동기 함수임을 나타냄</p>
<h3 id="await">await</h3>
<ul>
<li>비동기 함수의 실행 결과를 기다림 -&gt; Promise 상태가 바뀔때까지 기다림</li>
<li>비동기 코드를 동기 코드처럼 작성할 수 있어 에러 처리가 간단해짐</li>
<li><blockquote>
<p>await을 사용한 코드 결과가 실패 Promise를 반환하면 catch 실행 </p>
</blockquote>
</li>
<li>async 안에 있어야 함<br />
### 비동기에서 대기? 그럼 동기가 아닌가?
> await에서의 대기는 비동기식 대기로, 특정 작업이 완료될 때까지 기다리지만 코드 실행이 중단되지 않고 다른 비동기 작업이 계속 실행되게 해준다.
#### 예시
``` python
import asyncio
# 비동기 함수 정의
async def fetch_data(task_name):
  print(f"Starting {task_name}")
  await asyncio.sleep(2)  # 2초 동안 비동기 대기
  print(f"Completed {task_name}")
  return f"{task_name} data"
# 메인 비동기 함수 정의
async def main():
  tasks = [
      fetch_data("Task 1"),
      fetch_data("Task 2"),
      fetch_data("Task 3")
  ] 
  # 모든 비동기 작업을 병렬로 실행하고 완료될 때까지 대기
  results = await asyncio.gather(*tasks)
  for result in results:
      print(result)
# 이벤트 루프 실행
asyncio.run(main())
```
#### 결과
```python
Starting Task 1
Starting Task 2
Starting Task 3
Completed Task 1
Completed Task 2
Completed Task 3
Task 1 data
Task 2 data
Task 3 data
```
동기라면 fetch_data에서 task를 실행할 때 asyncio.sleep(2)를 만나서 2초를 대기한 후 다른 것이 실행되므로 **Starting Task 1 / Completed 1**이 나오겠지만
위처럼 비동기를 사용하므로 2초 대기하더라도 main에서 Task 2를 실행하므로 ** Starting Task 1 / Starting Task 2 **와 같은 결과가 나온다.

</li>
</ul>
<p>비동기, 동기, 병렬 설명이 쉽게 잘 되어있다.
<a href="https://fastapi.tiangolo.com/ko/async/#async-await_1">https://fastapi.tiangolo.com/ko/async/#async-await_1</a></p>
<hr />
<p><a href="https://fastapi.tiangolo.com/ko/">https://fastapi.tiangolo.com/ko/</a></p>