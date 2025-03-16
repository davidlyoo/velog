<h1 id="attention과-transformer-핵심-개념-정리">Attention과 Transformer: 핵심 개념 정리</h1>
<h2 id="1-attention-mechanism의-등장">1. Attention Mechanism의 등장</h2>
<blockquote>
<p><strong>기존 RNN/LSTM의 한계:</strong>  </p>
</blockquote>
<ol>
<li>고정된 크기의 벡터로 문장을 표현 → 정보 손실 발생  </li>
<li>긴 문장을 학습할 때 <strong>Vanishing Gradient 문제</strong> 발생 → 장기 의존성(Long-term Dependency) 학습이 어려움  </li>
</ol>
<blockquote>
<p><strong>Attention의 개요:</strong>  </p>
</blockquote>
<ul>
<li>Attention이란?  <ul>
<li><strong>중요한 정보에 가중치를 부여하여 특정 부분에 집중</strong>하는 메커니즘  </li>
<li>기존 RNN처럼 하나의 벡터로 전체 문장을 압축하는 방식이 아니라, <strong>입력의 특정 부분을 선택적으로 반영 가능</strong>  </li>
</ul>
</li>
</ul>
<hr />
<h2 id="2-attention-mechanism-개요">2. Attention Mechanism 개요</h2>
<p>Attention은 <strong>입력 데이터에서 특정 부분에 집중</strong>하는 메커니즘입니다.<br />즉, 문장에서 중요한 단어에 가중치를 부여하여 더 집중할 수 있도록 합니다.</p>
<h3 id="21-query-key-value와-attention-연산">2.1 Query, Key, Value와 Attention 연산</h3>
<p>Attention의 핵심은 Query, Key, Value 연산을 통한 weight 계산입니다.</p>
<ul>
<li><p><strong>Query (Q)</strong>: 어떤 정보를 찾을 것인지 현재 처리 중인 입력 데이터</p>
</li>
<li><p><strong>Key (K)</strong>: 각 데이터의 고유한 특징을 나타내는 벡터</p>
</li>
<li><p><strong>Value (V)</strong>: Query와 Key의 관계를 바탕으로 최종적으로 사용할 데이터</p>
</li>
</ul>
<blockquote>
<ol>
<li><strong>Query와 Key 간의 연관 정도(유사도)를 구합니다</strong>.  </li>
</ol>
</blockquote>
<p><img alt="" src="https://velog.velcdn.com/images/davidlyoo/post/5b480e4b-7ad6-4309-abbb-d94743e05915/image.png" /></p>
<p><strong>내적(dot product)의 의미</strong>: Query와 Key의 내적이 클수록 두 단어 간의 연관도가 높음을 의미<br />→ 즉, Query와 가장 유사한 Key에 높은 가중치가 부여됨  </p>
<blockquote>
<ol start="2">
<li>중요한 정보를 더 강조하기 위해 Softmax를 적용하여 가중치를 구합니다.</li>
</ol>
</blockquote>
<p><img alt="" src="https://velog.velcdn.com/images/davidlyoo/post/72a3a3b5-8256-4de6-90c6-5c8402c641bd/image.png" /></p>
<p>Softmax를 통해 가중치를 정규화하고, 값이 클수록 더 중요한 정보로 반영됩니다.  </p>
<blockquote>
<ol start="3">
<li><strong>가중치 값과 Value를 곱하여 최종 Attention 값을 계산</strong>합니다.</li>
</ol>
</blockquote>
<p><img alt="" src="https://velog.velcdn.com/images/davidlyoo/post/1eb99749-2943-435d-b122-05d2625826a3/image.png" /></p>
<p>이 과정을 통해 <strong>중요한 정보를 더욱 강조하여 최종 출력값을 결정</strong>할 수 있습니다.  </p>
<hr />
<h2 id="3-attention의-한계와-해결책">3. Attention의 한계와 해결책</h2>
<h3 id="31-문맥context-정보-부족-문제">3.1 문맥(Context) 정보 부족 문제</h3>
<blockquote>
<p>Attention Mechanism은 <strong>문장에서 단어 간의 유사도만을 반영</strong>하므로,<br /><strong>순서 정보가 사라지는 문제</strong>가 발생합니다.</p>
</blockquote>
<h4 id="🚨-문제점">🚨 문제점:</h4>
<ul>
<li><strong>문장의 단어 순서가 바뀌어도 Attention 값이 동일</strong>  </li>
<li>Ex) <code>&quot;The dog chased the cat.&quot;</code> 와 <code>&quot;The cat chased the dog.&quot;</code> 가 동일한 Attention 값을 가짐  </li>
</ul>
<h4 id="✅-해결책-positional-encoding">✅ 해결책: Positional Encoding</h4>
<blockquote>
<p>Transformer는 <strong>Positional Encoding</strong>을 도입하여 이 문제를 해결했습니다.<br />각 단어의 위치에 따라 특정 벡터를 더해, 위치 정보를 반영할 수 있도록 합니다.</p>
</blockquote>
<p><img alt="" src="https://velog.velcdn.com/images/davidlyoo/post/28c80dfb-5db0-482b-864a-39164c651703/image.png" />
pos : 단어의 위치 (0부터 시작)
𝑖: 벡터 차원 내 인덱스 (짝수/홀수 구분)
𝑑: 모델의 차원</p>
<p>→ 사인, 코사인 함수를 사용하여 <strong>위치 정보가 포함된 벡터를 추가</strong>합니다.<br />→ 단어의 순서가 바뀌면 Attention 값도 달라짐</p>
<hr />
<h3 id="32-문맥context-정보-부족-문제-해결-self-attention">3.2 문맥(Context) 정보 부족 문제 해결: Self-Attention</h3>
<p>문장 내 단어들은 서로 의미적으로 영향을 주지만, 일반적인 Attention 메커니즘만으로는 이를 완벽하게 반영하기 어렵습니다.</p>
<h4 id="✅-해결책-self-attention-도입">✅ 해결책: Self-Attention 도입</h4>
<blockquote>
<p>Self-Attention은 <strong>Query = Key</strong>로 설정하여 <strong>자기 자신과의 관계를 학습</strong>하는 방식입니다.<br />즉, 문장의 각 단어가 <strong>다른 단어들과 얼마나 관련이 있는지를 학습</strong>합니다.</p>
</blockquote>
<p><strong>❓ 그런데, 동음이의어는 어떻게 구분할까?</strong><br />&quot;bank&quot;<code>라는 단어가 문장에서</code>&quot;river bank&quot;<code>과</code>&quot;money bank&quot;` 중 어느 의미인지 어떻게 구별할까?  </p>
<blockquote>
<ul>
<li>주변 단어들과의 관계를 Self-Attention을 통해 학습  </li>
<li><strong>문맥이 중요한 단어에 더 높은 weighted sum value를 부여</strong>  </li>
</ul>
</blockquote>
<p>→ 즉, <strong>맥락 정보가 반영</strong>되도록 조정됩니다.</p>
<hr />
<h2 id="4-transformer의-구조와-응용">4. Transformer의 구조와 응용</h2>
<h3 id="41-transformer의-기본-구조">4.1 Transformer의 기본 구조</h3>
<p>Transformer는 <strong>Encoder-Decoder 구조</strong>로 이루어져 있습니다.
<img alt="" src="https://velog.velcdn.com/images/davidlyoo/post/850f5cc2-6d27-4c44-9f03-f074e365a9d7/image.png" /></p>
<table>
<thead>
<tr>
<th>Encoder</th>
<th>Decoder</th>
</tr>
</thead>
<tbody><tr>
<td>입력 문장을 처리하여 <strong>Context Vector</strong> 생성</td>
<td>Context Vector를 바탕으로 출력 문장 생성</td>
</tr>
<tr>
<td>Self-Attention + Feed Forward Network (FFN)</td>
<td>Masked Self-Attention + FFN</td>
</tr>
</tbody></table>
<ul>
<li><strong>Encoder</strong>: 입력을 받아 의미적 표현(Context)을 학습  </li>
<li><strong>Decoder</strong>: Encoder에서 학습한 정보를 바탕으로 출력을 생성  </li>
</ul>
<h4 id="42-transformer-응용의-최신-트렌드">4.2 Transformer 응용의 최신 트렌드</h4>
<p>Transformer는 다양한 방식으로 응용되고 있습니다.  </p>
<ul>
<li><strong>BERT</strong> (Bidirectional Encoder): 인코더만 활용해 문맥을 양방향으로 학습  </li>
<li><strong>GPT</strong> (Autoregressive Decoder): 디코더만 활용해 자연어 생성을 수행  </li>
<li><strong>ViT</strong> (Vision Transformer): CNN 없이 Transformer를 활용하여 이미지 처리  </li>
</ul>
<hr />
<h2 id="5-masking의-개념">5. Masking의 개념</h2>
<h3 id="51-masking의-필요성">5.1 Masking의 필요성</h3>
<ul>
<li>Transformer는 <strong>입력을 한꺼번에 처리하여 병렬적으로 학습</strong>합니다.  </li>
<li>하지만, 문장 생성 과정에서는 <strong>이후 단어를 미리 보면 안 됨</strong> → 이를 방지하기 위해 <strong>Masking 기법</strong>을 적용합니다.  </li>
</ul>
<h3 id="52-masking-기법">5.2 Masking 기법</h3>
<ul>
<li><strong>미래 단어를 참고하지 않도록 하기 위해 Look-ahead Masking을 적용</strong>  </li>
<li>이후 단어를 보지 않도록 <strong>Value 값에 -∞를 추가</strong>  </li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/davidlyoo/post/93b9f772-3dd0-403e-b725-e39f0cd694fd/image.png" /></p>
<ul>
<li>여기서 Mask 값으로 <strong>-∞</strong> 를 적용  </li>
<li>Softmax 연산 후 exp(-∞) = 0 이므로, 해당 값이 완전히 제거됨  </li>
</ul>
<blockquote>
<ul>
<li>즉, 이후 단어를 참고하지 않도록 강제로 무시하는 효과를 가짐**</li>
</ul>
</blockquote>
<hr />
<p>📌 참고 자료  </p>
<ul>
<li>Transformer 논문: <em>Attention Is All You Need</em> (Vaswani et al., 2017)</li>
<li>YouTube 강의: <a href="https://www.youtube.com/watch?v=6s69XY025MU">https://www.youtube.com/watch?v=6s69XY025MU</a></li>
</ul>