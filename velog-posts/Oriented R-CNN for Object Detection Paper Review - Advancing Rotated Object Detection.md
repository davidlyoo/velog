<h1 id="summary">Summary</h1>
<p>The Oriented R-CNN model aims to detect not only the location of objects but also ther orientations. It improves existing two-stage object detectors through:</p>
<ul>
<li><ol>
<li><strong>Lightweight Oriented Region Proposal Network (RPN)</strong>:
Reduces computation by minimizing anchor complexity.</li>
</ol>
</li>
<li><ol start="2">
<li><strong>Midpoint Offset Representation</strong>:
Enhances accuracy by learning vertex offsets rather than directly predicting angles.</li>
</ol>
</li>
<li><ol start="3">
<li><strong>Rotated RoI Align</strong>:
Improves feature extraction for rotated objects.</li>
</ol>
</li>
</ul>
<h1 id="introduction">Introduction</h1>
<p><img alt="" src="https://velog.velcdn.com/images/davidlyoo/post/94d42443-a56f-4fb1-9508-d9ed43e327e5/image.png" /></p>
<p>Traditional two-stage object detectors are <strong>computationally expensive</strong>, often introducing bottlenecks. Oriented R-CNN address these issues with:</p>
<ul>
<li><ol>
<li>Oriented RPN</li>
</ol>
</li>
<li><ol start="2">
<li>Oriented R-CNN Head (Feature Extraction &amp; alignment for oriented RoIs)</li>
</ol>
</li>
</ul>
<h3 id="limitations-of-existing-models">Limitations of Existing Models</h3>
<ul>
<li><p><strong>Rotated RPN</strong></p>
<ul>
<li><p>Uses <strong>54 anchors</strong> (3 scales x 3 ratios x 6 angles)</p>
</li>
<li><p>Performs well when distributed sparsely, ensuring good coverage</p>
</li>
<li><p>However, <strong>massive computation</strong> and <strong>high memory footprint</strong> due to the large number of anchors.</p>
</li>
<li><p><strong>RoI Transformer</strong></p>
<ul>
<li>Converts horizontal RoIs to oriented RoIs through multiple transformations.</li>
<li>Still <strong>computationally expensive</strong> due to redundant operations.</li>
</ul>
</li>
</ul>
</li>
</ul>
<h3 id="proposed-solution">Proposed Solution</h3>
<p>Instead of excessive anchors or complex transformations, the authors suppsose:</p>
<ul>
<li><strong>Light-weight CNN</strong>: Oriented RPN designed with fewer parameters, reduces computational cost and prevents overfitting</li>
<li><strong>Midpoint Offset Representation</strong>: Predicts offsets from the box center to vertices, avoiding <strong>angle regression instability</strong>.</li>
</ul>
<h1 id="oriented-r-cnn">Oriented R-CNN</h1>
<p><img alt="" src="https://velog.velcdn.com/images/davidlyoo/post/46702507-99c0-4f85-964f-645c8a0132fc/image.png" /></p>
<h3 id="oriented-rpn">Oriented RPN</h3>
<ul>
<li><p>Model Architecture</p>
<ul>
<li><p>5-level Feature Pyramid Network (FPN)</p>
</li>
<li><p>Each level consists of:</p>
<ul>
<li>3x3 convolution layer</li>
<li>Two sibling 1x1 convolution layers:<ul>
<li>Regression branch: Outputs bounding-box offsets δ=(δx,δy,δw,δh,δα,δβ)</li>
<li>Classification branch: Determines object presence</li>
</ul>
</li>
</ul>
<p>At each feature map location, A = 3 anchors are used, leading to 6A regression outputs</p>
</li>
<li><p>Three horizontal anchors assigned per spatial location (1:1, 1:2, 2:1)</p>
</li>
<li><p>Bounding-boxes represented as (x, y, w, h) where:</p>
<ul>
<li>x, y: Center coordinates</li>
<li>w, h: Width and height</li>
</ul>
</li>
</ul>
</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/davidlyoo/post/9119eadd-5442-4061-bfdb-4f03a1961abe/image.png" /></p>
<h3 id="midpoint-offset-representation">Midpoint Offset Representation</h3>
<p>Instead of directly predicting the rotation angle θ, Oriented RPN learns offsets (Δα, Δβ) from the bounding-box center to its rotated vertices.</p>
<ul>
<li><strong>Why Midpoint Offset?</strong><ul>
<li>Eliminates angle ambiguity</li>
<li>Reduces gradient discontinuity</li>
<li>Simplifies regression</li>
</ul>
</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/davidlyoo/post/d26efca9-49a0-4b94-8e86-ba7d5957bbec/image.png" /></p>
<h3 id="loss-function">Loss Function</h3>
<p><strong>Anchor Classification</strong></p>
<ul>
<li>Positive Anchor:<ul>
<li>IoU ≥ 0.7 with any ground-truth box.</li>
<li>Highest IoU (≥ 0.3) with a specific ground-truth box. <ul>
<li>Negative Anchor</li>
</ul>
</li>
<li>IoU ≤ 0.3 (considered background)<ul>
<li>All other anchors are ignored during training.</li>
</ul>
</li>
</ul>
</li>
</ul>
<p><strong>Bounding-box Regression</strong></p>
<ul>
<li>Uses Affine Transform (similar to Faster R-CNN)</li>
<li>Loss function:<ul>
<li>Classification Loss: Cross-Entropy Loss</li>
<li>Regression Loss: Smooth L1 Loss</li>
</ul>
</li>
</ul>
<p>By integrating affine transformation, the model enhances bounding-box refinement while maintaing stability.</p>
<h2 id="oriented-r-cnn-head">Oriented R-CNN Head</h2>
<p><img alt="" src="https://velog.velcdn.com/images/davidlyoo/post/ed34b558-6452-4ebf-ba4f-60d3ab6e1a3b/image.png" /></p>
<h3 id="feature-extraction--alignment">Feature Extraction &amp; Alignment</h3>
<ul>
<li>Oriented Proposals are extracted and refined</li>
<li>Rotated RoI Align is applied to capture accurate object features.</li>
</ul>
<p>Process:</p>
<ol>
<li>Projection:</li>
</ol>
<ul>
<li>Since oriented proposals are generally parallelograms, they are first converted into a rectangular shape.<ul>
<li>The shorter diagonal is extended to match the length of the longer diagonal to ensure proper alignment.</li>
</ul>
</li>
</ul>
<ol start="2">
<li>Grid-based Feature Extraction</li>
</ol>
<ul>
<li>The oriented proposal is divided into fixed-size grids (e.g., 7x7)</li>
<li>Each grid region undergoes feature sampling to extract relevant details.</li>
</ul>
<p>Head Structure</p>
<ul>
<li>Classification Branch -&gt; Object classification</li>
<li>Regression Branch -&gt; Final bounding-box refinement</li>
</ul>
<p> By refining object proposals through Oriented R-CNN Head, the model ensures higher accuracy in both classification and localization.</p>
<h1 id="performance">Performance</h1>
<p><img alt="Performance Comparison" src="https://velog.velcdn.com/images/davidlyoo/post/ce9ff5a5-fe4c-41f6-841e-dad77f171c40/image.png" /></p>
<ul>
<li>Midpoint Offset Representation improves precision while reducing complexity.</li>
<li>Rotated RoI Align significantly enhances feature extraction accuracy.</li>
<li>Oriented R-CNN outperforms prior models with lower computational costs.</li>
</ul>
<h1 id="conclusion">Conclusion</h1>
<p>Instead of simply aiming for higher accuracy, this study highlights the importance of designing efficient resource utilization strategies. It made me realize that designing detection models should not be only focused on accuracy hundered percent but also about making practical efficiency This research serves as a strong reference for future works seeking to balance detection precision with computational feasibility.</p>
<h1 id="references">References</h1>
<p>Xie, X., Cheng, G., Wang, J., Yao, X., &amp; Han, J. (2021). Oriented R-CNN for object detection. In Proceedings of the IEEE/CVF International Conference on Computer Vision (ICCV). IEEE. <a href="https://doi.org/10.1109/iccv48922.2021.00350">https://doi.org/10.1109/iccv48922.2021.00350</a></p>