<h1 id="pca-principal-component-analysis-정리">PCA (Principal Component Analysis) 정리</h1>
<hr />
<h2 id="1️⃣-pca-개념-요약">1️⃣ PCA 개념 요약</h2>
<ul>
<li><p>고차원 데이터를 저차원으로 축소하면서 데이터의 분산(정보량)을 최대한 보존하는 차원 축소 기법.</p>
</li>
<li><p><strong>핵심 아이디어:</strong>  </p>
<ul>
<li>데이터의 최대 분산 방향을 찾아 그 방향으로 데이터를 투영 (projection).</li>
<li>각 투영 방향을 <strong>주성분 (Principal Component)</strong> 이라 한다.</li>
</ul>
</li>
<li><p><strong>주요 활용:</strong>  </p>
<ul>
<li>차원 축소</li>
<li>노이즈 제거</li>
<li>시각화</li>
<li>연산량 감소</li>
<li>특징 추출 등</li>
</ul>
</li>
</ul>
<hr />
<h2 id="2️⃣-pca-전체-알고리즘-흐름">2️⃣ PCA 전체 알고리즘 흐름</h2>
<h3 id="입력-데이터">입력 데이터</h3>
<ul>
<li>$\mathbf{x}_i \in \mathbb{R}^D$, 총 N개의 데이터 샘플</li>
</ul>
<h3 id="절차">절차</h3>
<p>① <strong>평균 중심화 (Mean Centering)</strong>  </p>
<ul>
<li>데이터의 평균 계산:</li>
</ul>
<p>$$
\mu = \frac{1}{N} \sum_{i=1}^{N} \mathbf{x}_i
$$</p>
<ul>
<li>중심화된 데이터:</li>
</ul>
<p>$$
\tilde{\mathbf{x}}_i = \mathbf{x}_i - \mu
$$</p>
<p>② <strong>공분산 행렬 계산</strong></p>
<ul>
<li>공분산 행렬 S:</li>
</ul>
<p>$$
S = \frac{1}{N} \sum_{i=1}^{N} (\tilde{\mathbf{x}}_i)(\tilde{\mathbf{x}}_i)^T
$$</p>
<p>③ <strong>고유값 분해 (Eigen Decomposition)</strong></p>
<ul>
<li>다음 고유값 문제를 푼다:</li>
</ul>
<p>$$
S \mathbf{u} = \lambda \mathbf{u}
$$</p>
<ul>
<li>$\lambda$: 고유값 (분산의 크기)  </li>
<li>$\mathbf{u}$: 고유벡터 (주성분의 방향)</li>
</ul>
<p>④ <strong>상위 K개의 고유벡터 선택</strong></p>
<ul>
<li>고유값을 내림차순 정렬하여 분산이 큰 순서대로 상위 K개의 고유벡터 선택:</li>
</ul>
<p>$$
U = [\mathbf{u}_1, \mathbf{u}_2, \cdots, \mathbf{u}_K]
$$</p>
<p>⑤ <strong>데이터 변환 (Projection)</strong></p>
<ul>
<li>원본 데이터를 저차원으로 변환:</li>
</ul>
<p>$$
h(\mathbf{x}) = U^T (\mathbf{x} - \mu)
$$</p>
<ul>
<li>결과 $h(\mathbf{x}) \in \mathbb{R}^K$<br />→ 차원이 축소된 표현</li>
</ul>
<hr />
<h2 id="3️⃣-pca의-수식적-의미">3️⃣ PCA의 수식적 의미</h2>
<ul>
<li>PCA는 다음 최적화 문제를 푼다:</li>
</ul>
<p>$$ 
\max_{\mathbf{u}<em>1} \sum</em>{i=1}^{N} (\mathbf{u}_1^T (\mathbf{x}_i - \mu))^2
$$</p>
<p>subject to</p>
<p>$$
|\mathbf{u}_1| = 1
$$</p>
<ul>
<li>이 문제를 풀면 고유값 문제로 귀결된다.</li>
</ul>
<hr />
<h2 id="4️⃣-pca에서-직교성-orthonormality의-의미">4️⃣ PCA에서 직교성 (Orthonormality)의 의미</h2>
<ul>
<li>PCA의 주성분들은 서로 <strong>직교(orthogonal)</strong> 하며,  </li>
<li><strong>정규화(norm=1)</strong> 되어 있음:</li>
</ul>
<p>$$
\mathbf{u}_i^T \mathbf{u}_j = 0 \quad (i \ne j), \quad |\mathbf{u}_j| = 1
$$</p>
<ul>
<li>이유: 공분산 행렬은 대칭행렬이므로 고유벡터가 항상 직교한다.</li>
</ul>
<hr />
<h2 id="5️⃣-pca-투영의-해석">5️⃣ PCA 투영의 해석</h2>
<ul>
<li>PCA는 원본 데이터를 새로운 축으로 바꾼다:</li>
</ul>
<p>$$
h(\mathbf{x}) =
\begin{bmatrix}
\mathbf{u}_1 \cdot \mathbf{x} \
\mathbf{u}_2 \cdot \mathbf{x} \
\vdots \
\mathbf{u}_K \cdot \mathbf{x}
\end{bmatrix}
$$</p>
<ul>
<li>각 요소는 원본 데이터가 주성분 축으로 얼마나 투영되었는지 나타냄.</li>
</ul>
<hr />
<h2 id="6️⃣-pca-예제-복습용">6️⃣ PCA 예제 복습용</h2>
<p>간단 예제 흐름:</p>
<p>1️⃣ 평균 계산 → 2️⃣ 중심화 → 3️⃣ 공분산 행렬 → 4️⃣ 고유값/고유벡터 → 5️⃣ 투영</p>
<hr />
<h2 id="7️⃣-pca-핵심-한-줄-요약">7️⃣ PCA 핵심 한 줄 요약</h2>
<p><strong>PCA = 최대 분산 방향으로 데이터를 투영하여 저차원으로 표현하는 선형 차원 축소 기법이다.</strong></p>