<h2 id="📌-앙상블-학습이란">📌 앙상블 학습이란?</h2>
<blockquote>
<p>여러 개의 예측 모델을 결합하여 더 강력한 모델을 만드는 기법.</p>
</blockquote>
<p>대표적으로 <strong>Bagging</strong>과 <strong>Boosting</strong>이 있으며,</p>
<ul>
<li>Bagging은 <strong>분산(Variance)</strong> 감소</li>
<li>Boosting은 <strong>편향(Bias)</strong> 감소에 초점을 둔다.</li>
</ul>
<hr />
<h2 id="📦-bagging-bootstrap-aggregating">📦 Bagging (Bootstrap Aggregating)</h2>
<h3 id="✅-핵심-개념">✅ 핵심 개념</h3>
<ul>
<li><strong>중복 허용 샘플링 (Bootstrap)</strong> 으로 여러 데이터셋 생성</li>
<li>각 모델을 독립적으로 학습 (병렬)</li>
<li>결과는 평균 or 다수결로 결합</li>
</ul>
<h3 id="⚙️-알고리즘">⚙️ 알고리즘</h3>
<ol>
<li>데이터셋 D에서 중복 허용 샘플링 → D₁, D₂, ..., Dₘ</li>
<li>각각 모델 h₁(x), h₂(x), ..., hₘ(x) 학습</li>
<li>예측<ul>
<li>회귀:<br />$$ \hat{y} = \frac{1}{m} \sum_{i=1}^m h_i(x) $$</li>
<li>분류:<br />$$ \hat{y} = \mathbb{I} \left( \frac{1}{m} \sum y_i &gt; 0.5 \right) $$</li>
</ul>
</li>
</ol>
<h3 id="🎯-bias-vs-variance">🎯 Bias vs Variance</h3>
<ul>
<li><strong>Bias</strong>: 동일한 모델 반복 → 근본적 bias는 해결 X</li>
<li><strong>Variance</strong>: 다수의 예측을 평균 → 변동성 감소</li>
</ul>
<h3 id="📌-특징-요약">📌 특징 요약</h3>
<ul>
<li>병렬 학습 가능</li>
<li>모델 간 상관 있음</li>
<li><strong>불안정한 모델(ex. Tree)</strong>일수록 효과 큼</li>
</ul>
<h3 id="🌲-random-forest--bagging--feature-randomness">🌲 Random Forest = Bagging + Feature Randomness</h3>
<ul>
<li>각 노드에서 일부 feature만 선택하여 분할</li>
<li>모델 간 상관성↓ → 일반화↑</li>
</ul>
<hr />
<h2 id="⚡-boosting-adaboost-기준">⚡ Boosting (Adaboost 기준)</h2>
<h3 id="✅-핵심-개념-1">✅ 핵심 개념</h3>
<ul>
<li><strong>약한 분류기들을 순차적으로 학습</strong></li>
<li>이전 단계에서 <strong>틀린 샘플에 집중</strong></li>
<li><strong>가중합으로 최종 예측기 생성</strong></li>
</ul>
<h3 id="⚙️-알고리즘-절차-adaboost">⚙️ 알고리즘 절차 (Adaboost)</h3>
<ol>
<li><p>초기 가중치 ( w_i = \frac{1}{N} )</p>
</li>
<li><p>매 라운드 t마다:</p>
<ul>
<li>약한 분류기 ( h_t(x) ) 학습</li>
<li>오류율 계산<br />$$ \text{err}_t = \sum w_i \cdot \mathbb{I}[h_t(x_i) \ne y_i] $$</li>
<li>신뢰도 계산<br />$$ \alpha_t = \frac{1}{2} \log \left( \frac{1 - \text{err}_t}{\text{err}_t} \right) $$</li>
<li>가중치 업데이트<br />$$ w_i \leftarrow w_i \cdot \exp(-\alpha_t \cdot y_i h_t(x_i)) $$
또는<br />$$ w_i \leftarrow w_i \cdot \exp(2\alpha_t \cdot \mathbb{I}[h_t(x_i) \ne y_i]) $$</li>
<li>정규화: ( \sum w_i = 1 )</li>
</ul>
</li>
<li><p>최종 분류기:<br />$$ H(x) = \text{sign} \left( \sum_{t=1}^T \alpha_t h_t(x) \right) $$</p>
</li>
</ol>
<h3 id="📌-가중치-직관">📌 가중치 직관</h3>
<ul>
<li><strong>틀린 샘플</strong> → 다음 라운드에서 <strong>가중치 증가</strong></li>
<li><strong>맞은 샘플</strong> → <strong>감소 or 유지</strong></li>
<li>⇒ 어려운 샘플에 더 집중하는 구조</li>
</ul>
<h3 id="🎯-bias-vs-variance-1">🎯 Bias vs Variance</h3>
<table>
<thead>
<tr>
<th>항목</th>
<th>설명</th>
</tr>
</thead>
<tbody><tr>
<td><strong>Bias</strong></td>
<td>감소함 (오류 교정)</td>
</tr>
<tr>
<td><strong>Variance</strong></td>
<td>특정 샘플 과집중 시 증가 가능</td>
</tr>
</tbody></table>
<hr />
<h2 id="🔍-bagging-vs-boosting-요약-비교">🔍 Bagging vs Boosting 요약 비교</h2>
<table>
<thead>
<tr>
<th>항목</th>
<th>Bagging</th>
<th>Boosting</th>
</tr>
</thead>
<tbody><tr>
<td>학습 방식</td>
<td>병렬</td>
<td>순차</td>
</tr>
<tr>
<td>목적</td>
<td>Variance 감소</td>
<td>Bias 감소</td>
</tr>
<tr>
<td>샘플 가중치</td>
<td>동일</td>
<td>단계별 업데이트</td>
</tr>
<tr>
<td>예측 방식</td>
<td>평균/다수결</td>
<td>가중합 후 sign</td>
</tr>
<tr>
<td>과적합 민감도</td>
<td>낮음</td>
<td>높을 수 있음</td>
</tr>
<tr>
<td>결정 경계</td>
<td>부드럽고 단순</td>
<td>정밀하고 복잡</td>
</tr>
</tbody></table>
<hr />
<h2 id="📊-boosting-예제-흐름-슬라이드-기반">📊 Boosting 예제 흐름 (슬라이드 기반)</h2>
<blockquote>
<p>자세한 그림과 구간은 따로 첨부 예정</p>
</blockquote>
<ol>
<li>각 stump tree는 feature 기준으로 데이터를 나눠 예측</li>
<li>각 분류기의 오류율을 기반으로 ( \alpha_t ) 계산</li>
<li>최종 예측<br />$$ H(x) = \text{sign}(\alpha_1 h_1(x) + \alpha_2 h_2(x) + \alpha_3 h_3(x)) $$</li>
<li>예측값이 0보다 크면 +1, 작으면 -1</li>
<li>틀린 샘플은 가중치 증가되어 다음 학습기에 더 큰 영향</li>
</ol>
<hr />
<h2 id="✅-정리">✅ 정리</h2>
<ul>
<li><strong>Bagging</strong>: 독립적 학습기 병렬 학습 → 분산 감소</li>
<li><strong>Boosting</strong>: 순차 학습으로 오류 보정 → 편향 감소</li>
</ul>