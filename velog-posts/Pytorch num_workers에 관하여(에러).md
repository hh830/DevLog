<h2 id="문제-발견">문제 발견</h2>
<pre><code class="language-python"># train_target과 test_target을 하나의 텐서로 합치기
all_targets = torch.cat((train_target, test_target))

# 타겟 값의 평균 계산
target_mean = torch.mean(all_targets).item()
print(f&quot;Target Mean: {target_mean}&quot;)

# 허용 가능한 상대적 오차 설정
acceptable_relative_error = 0.05  # 5%

# 최적의 손실 값 범위 계산
optimal_loss_upper_bound = target_mean * acceptable_relative_error
print(f&quot;Optimal Loss Upper Bound: {optimal_loss_upper_bound}&quot;)</code></pre>
<p>Target Mean: 237.4127960205078
Optimal Loss Upper Bound: 11.87063980102539</p>
<p>파이토치 주피터 노트북을 사용하다가 위 Target Mean과 Optimal Loss Upper Bound가 바뀐다는 문제를 발견했다. (더 작게 바뀜)</p>
<p>주피터노트북을 키자마자/코드를 실행하자마자 위 현상이 발생하는 것 같다.</p>
<p>하지만 시간이 지나면 다시 원래 값이 나온다.
찾아봐도 이유를 알지는 못했지만 아마 엑셀 데이터 값이 커서 그런 것 같다.</p>
<hr />
<p>그 후에도 계속 커널을 시작/재시작하면 초반에는 위 값이 모두 0으로 바뀌고 train_loss와 val_loss가 모두 0이 나오는 문제가 있었다.</p>
<p>심지어 일정 부분 이후부터 train_input, train_target, 등등 모든 데이터가 모두 0이 되는 것이었다 !</p>
<p>이 또한 시간이 지나서 다시 실행시키면 점점 숫자가 커지면서 제 값을 찾아가는 문제가 있었다.</p>
<p>대체 어디서 문제인지 모르겠어서 주피터 노트북에 냅다 train_target에 관한 출력을 모든 줄에 출력해보았다 ㅎ
(이런건 주피터 최고)</p>
<p>그렇게 문제가 되는 줄을 찾아냈는데 !</p>
<pre><code class="language-python"># 데이터셋과 데이터로더 생성(미니배치)
# epoch 너무 느릴 때 데이터를 배치로 나눠 처리하게 하기

# 배치 크기 늘리면 에포크 학습시간 단축됨
# 너무 크면 메모리 사용량 증가시킴
train_dataset = TensorDataset(train_input, train_target)
val_dataset = TensorDataset(val_input, val_target)

print(train_dataset[0])
print(train_target)

num_workers=4

train_loader = DataLoader(train_dataset, batch_size=32, shuffle=True, num_workers=num_workers)
val_loader = DataLoader(val_dataset, batch_size=32, shuffle=False, num_workers=num_workers)</code></pre>
<p>여기서 train_loader와 val_loader를 수행하고 나면 0이 되는 것이었다.</p>
<hr />
<h2 id="해결">해결</h2>
<p>그 이유가 뭔지 더 찾아보니
GPU의 병렬 연산부분을 담당하는 num_workers를 0으로 하면 해결이 됐다. 
윈도우에서는 병렬 연산이 잘 안된다고 하는 것 같다.
하지만 이렇게 하면 병렬 연산을 사용하지 않게 되기 때문에... num_workers를 어떻게든 사용하고 싶어 쭉 찾아보았다.</p>
<p>결국 num_workers를 0으로 해야한다는 것만 알게 되고 이를 사용할 수는 없었다.</p>
<hr />
<p>하지만 num_workers에 관한 다양한 글을 볼 수 있었다.</p>
<blockquote>
<p>Windows에서 num_workers &gt; 0인 DataLoader는 매우 느림(50배 느림)</p>
</blockquote>
<ul>
<li>병렬 처리를 하면 더 빨라져야 하는데 더 느려진다 라는 문제
<a href="https://github.com/pytorch/pytorch/issues/12831">https://github.com/pytorch/pytorch/issues/12831</a></li>
</ul>
<blockquote>
<p>나와 관련된 에러가 뜨는 글
RuntimeError: DataLoader worker (pid ' ') exited unexpectedly with exit code 1.</p>
</blockquote>
<ul>
<li>주피터에서 실행할 때 에러가 뜨진 않지만 출력으로 뜨게 했을 때 </li>
<li><a href="https://aistudy9314.tistory.com/87">https://aistudy9314.tistory.com/87</a></li>
<li><a href="https://github.com/pytorch/pytorch/issues/5301">https://github.com/pytorch/pytorch/issues/5301</a></li>
</ul>