<h1 id="decision-tree--정리">Decision Tree  정리</h1>
<hr />
<h2 id="1-decision-tree란">1. Decision Tree란?</h2>
<p>Decision Tree는 데이터를 분할(split)하면서 학습하는 트리 기반의 분류(Classification) 또는 회귀(Regression) 모델이다.<br />내부 노드는 하나의 속성(attribute)으로 데이터를 분할하고, 리프 노드는 최종 예측 결과를 나타낸다.<br />주로 분류 문제에서 많이 활용된다.</p>
<hr />
<h2 id="2-분할-방식-partition">2. 분할 방식 (Partition)</h2>
<p>데이터를 반복적으로 axis-aligned rectangle로 분할한다.<br />각 분할은 하나의 속성 값에 대한 조건부 분기 형태로 이루어진다.<br />최종적으로 순수한 그룹이 만들어지는 것을 목표로 한다.</p>
<hr />
<h2 id="3-분할-기준-information-gain">3. 분할 기준: Information Gain</h2>
<h3 id="목표">목표</h3>
<p>어떤 속성이 데이터를 가장 잘 나누는지를 측정한다.<br />이를 위해 Information Gain을 기준으로 속성을 선택한다.</p>
<h3 id="정보-이득-information-gain-정의">정보 이득 (Information Gain) 정의</h3>
<ul>
<li><p><strong>Information Gain:</strong><br />$$
IG(S, A) = H(S) - H(S|A)
$$</p>
</li>
<li><p>$H(S)$: 전체 데이터의 엔트로피  </p>
</li>
<li><p>$H(S|A)$: 속성 A로 나눴을 때의 조건부 엔트로피 (weighted average entropy)</p>
</li>
</ul>
<hr />
<h2 id="4-엔트로피-entropy">4. 엔트로피 (Entropy)</h2>
<h3 id="정의">정의</h3>
<p>$$
H(S) = - \sum_{i=1}^{n} p_i \log_2 p_i
$$</p>
<ul>
<li>$p_i$: 클래스 i의 비율 (확률)</li>
</ul>
<h3 id="엔트로피-직관">엔트로피 직관</h3>
<ul>
<li>모든 데이터가 같은 클래스일 때 엔트로피는 0 (순수함)</li>
<li>클래스가 균등하게 섞일수록 엔트로피는 최대 (불확실성 최대)</li>
</ul>
<table>
<thead>
<tr>
<th>클래스 비율</th>
<th>엔트로피</th>
</tr>
</thead>
<tbody><tr>
<td>한 클래스만 있을 때</td>
<td>0</td>
</tr>
<tr>
<td>클래스가 반반일 때</td>
<td>1 (최대)</td>
</tr>
</tbody></table>
<hr />
<h2 id="5-conditional-entropy-분할-후-엔트로피">5. Conditional Entropy (분할 후 엔트로피)</h2>
<p>속성 A를 기준으로 분할했을 때의 엔트로피는 다음과 같이 계산된다:</p>
<p>$$
H(S|A) = \sum_{v \in \text{values}(A)} \frac{|S_v|}{|S|} H(S_v)
$$</p>
<ul>
<li>$S_v$: 속성 A가 값 $v$를 가질 때의 subset  </li>
<li>각 subset의 엔트로피를 전체 크기에 비례하여 가중평균으로 합산</li>
</ul>
<hr />
<h2 id="6-information-gain-계산-과정">6. Information Gain 계산 과정</h2>
<ol>
<li>전체 엔트로피 $H(S)$ 계산  </li>
<li>각 속성의 subset 별 엔트로피 $H(S_v)$ 계산  </li>
<li>weighted child entropy $H(S|A)$ 계산  </li>
<li>Information Gain = 전체 엔트로피 - weighted child entropy  </li>
<li>가장 Information Gain이 큰 속성을 선택</li>
</ol>
<hr />
<h2 id="7-id3-알고리즘-흐름">7. ID3 알고리즘 흐름</h2>
<ul>
<li>현재 노드에서 모든 속성의 Information Gain을 계산한다.</li>
<li>Information Gain이 가장 높은 속성을 선택하여 분할한다.</li>
<li>각 분할된 subset에 대해 재귀적으로 같은 과정을 반복한다.</li>
<li>leaf node에 도달하면 해당 노드에서 다수 클래스를 예측으로 사용한다.</li>
</ul>
<hr />
<h2 id="8-information-gain-계산-예시">8. Information Gain 계산 예시</h2>
<p>아래는 Information Gain을 실제로 계산하는 예시이다.</p>
<hr />
<h3 id="전체-데이터-parent">전체 데이터 (Parent)</h3>
<ul>
<li>전체 샘플 수: 30개</li>
<li>양성 클래스 (+): 14개</li>
<li>음성 클래스 (-): 16개</li>
</ul>
<h4 id="전체-엔트로피-parent-entropy">전체 엔트로피 (Parent Entropy)</h4>
<p>$$
H(S) = - \frac{14}{30} \log_2 \frac{14}{30} - \frac{16}{30} \log_2 \frac{16}{30}
$$</p>
<p>$$
H(S) \approx 0.996
$$</p>
<hr />
<h3 id="분할-후-자식-그룹-children">분할 후 자식 그룹 (Children)</h3>
<h4 id="첫-번째-그룹-17개-인스턴스">첫 번째 그룹 (17개 인스턴스)</h4>
<ul>
<li>양성 클래스 (+): 13개</li>
<li>음성 클래스 (-): 4개</li>
</ul>
<p>$$
H(S_1) = - \frac{13}{17} \log_2 \frac{13}{17} - \frac{4}{17} \log_2 \frac{4}{17}
$$</p>
<p>$$
H(S_1) \approx 0.787
$$</p>
<h4 id="두-번째-그룹-13개-인스턴스">두 번째 그룹 (13개 인스턴스)</h4>
<ul>
<li>양성 클래스 (+): 1개</li>
<li>음성 클래스 (-): 12개</li>
</ul>
<p>$$
H(S_2) = - \frac{1}{13} \log_2 \frac{1}{13} - \frac{12}{13} \log_2 \frac{12}{13}
$$</p>
<p>$$
H(S_2) \approx 0.391
$$</p>
<hr />
<h3 id="자식들의-가중평균-엔트로피-weighted-child-entropy">자식들의 가중평균 엔트로피 (Weighted Child Entropy)</h3>
<p>$$
H(S|A) = \frac{17}{30} \times 0.787 + \frac{13}{30} \times 0.391
$$</p>
<p>$$
H(S|A) \approx 0.615
$$</p>
<hr />
<h3 id="information-gain-계산">Information Gain 계산</h3>
<p>$$
IG(S, A) = H(S) - H(S|A)
$$</p>
<p>$$
IG(S, A) = 0.996 - 0.615 = 0.38
$$</p>
<hr />
<p>따라서 이 속성을 사용했을 때 Information Gain은 <strong>0.38</strong> 이다.</p>
<hr />
<h2 id="9-overfitting-방지-ockhams-razor">9. Overfitting 방지: Ockham's Razor</h2>
<p>가능한 한 단순한 트리를 선호하는 이유는 일반화 성능 때문이다.<br />트리가 복잡해질수록 학습 데이터에는 잘 맞지만 테스트 데이터에는 오히려 성능이 저하될 수 있다.<br />이를 방지하기 위해 가지치기(pruning) 등 추가 기법이 활용된다.</p>
<hr />
<h2 id="10-정보이론과의-연결-huffman-code">10. 정보이론과의 연결: Huffman Code</h2>
<p>Entropy는 정보이론에서 평균 코드 길이의 하한선을 의미한다.<br />Huffman Code는 이론적으로 entropy에 가장 근접한 최적의 평균 코드 길이를 가진 prefix code를 생성하는 방법이다.</p>
<hr />
<h2 id="11-decision-tree의-장단점">11. Decision Tree의 장단점</h2>
<h3 id="장점">장점</h3>
<ul>
<li>직관적이고 이해하기 쉽다.</li>
<li>시각화가 용이하다.</li>
<li>데이터 전처리가 거의 필요 없다.</li>
<li>범주형, 연속형 데이터를 모두 처리 가능하다.</li>
</ul>
<h3 id="단점">단점</h3>
<ul>
<li>쉽게 overfitting이 발생할 수 있다.</li>
<li>데이터가 적을수록 분산이 커진다.</li>
<li>가지치기(pruning)이 필요하다.</li>
</ul>