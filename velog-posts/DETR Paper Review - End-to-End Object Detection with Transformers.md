<h1 id="summary">Summary</h1>
<p>DEtection TRansformer (DETR) introduces a transformer-based approach to object detection, redefining it as a direct set prediction problem. Unlike many other modern detectors which rely on predefined anchor boxes and non-maximum suppression (NMS), DETR utilizes bipartite matching and transformers to directly predict objects in an end-to-end fashion.</p>
<p><strong>Key Innovations:</strong></p>
<ul>
<li><ol>
<li>Set-based global loss: Ensures unique predictions via bipartite matching.</li>
</ol>
</li>
<li><ol start="2">
<li>Transformer encoder-decoder architecture: Captures global context via self-attention and processes object queries in parallel, eliminating the need for sequential region-based detection pipelines.</li>
</ol>
</li>
</ul>
<hr />
<h1 id="introduction">Introduction</h1>
<p><strong>Object detection has traditionally relied on hand-designed components</strong> such as predefined anchor boxes, IoU-based matching, and NMS. These methods have been widely successful but come with several challenges:  </p>
<h4 id="limitations-of-traditional-object-detectors"><strong>Limitations of Traditional Object Detectors</strong></h4>
<ol>
<li><p><strong>Predefined Anchor Boxes:</strong>  </p>
<ul>
<li>Require careful tuning for each dataset.  </li>
<li>Increase computational complexity and introduce hyperparameter sensitivity.  </li>
</ul>
</li>
<li><p><strong>Complex Post-Processing Steps:</strong>  </p>
<ul>
<li>These challenges call for a model that eliminates heuristic-based matching and post-processing.</li>
<li>IoU-based Matching: IoU-based matching assigns multiple bounding boxes to the same object based on heuristics, leading to inconsistencies during training.</li>
<li>NMS: Required to remove redundant predictions but introduces heuristic decision rules.</li>
</ul>
</li>
</ol>
<h4 id="detr-a-set-prediction-approach"><strong>DETR: A Set Prediction Approach</strong></h4>
<p>DETR redefines object detection as a <strong>set prediction problem</strong>, eliminating the need for anchors and post-processing. Instead of relying on region proposals and IoU-based assignment, DETR directly predicts <strong>a fixed-size set of objects</strong> in a single forward pass.</p>
<hr />
<h1 id="detr">DETR</h1>
<h2 id="key-innovations-of-detr"><strong>Key Innovations of DETR</strong></h2>
<p>By treating object detection as a direct set prediction problem, DETR simplifies the detection pipeline while ensuring unique, permutation-invariant predictions.</p>
<ol>
<li><p><strong>Anchor-Free and Post-Processing-Free:</strong>  </p>
<ul>
<li><strong>No predefined anchor boxes.</strong>  </li>
<li><strong>No need for NMS, as predictions are uniquely assigned.</strong>  </li>
</ul>
</li>
<li><p><strong>Permutation Invariance &amp; Set Prediction:</strong>  </p>
<ul>
<li>Non-autoregressive decoding: DETR predicts all objects at once without relying on sequence order.  </li>
<li>Unlike traditional detectors, DETR treats predictions as an unordered set, <strong>ensuring order-invariant training</strong>.  </li>
<li>However, since there is no predefined order, <strong>we need a way to associate predicted objects with ground-truth objects</strong>.  </li>
</ul>
</li>
<li><p><strong>Bipartite Matching via the Hungarian Algorithm:</strong>  </p>
<ul>
<li>DETR uses <strong>bipartite matching (Hungarian algorithm)</strong> to assign predictions to ground-truth objects. </li>
<li>Since the number of detected objects can vary, DETR selects the <strong>best-matching predictions</strong> out of a fixed number of queries.  </li>
<li><strong>One-to-one matching ensures that each object is assigned to a single prediction, eliminating the need for NMS.</strong>  </li>
</ul>
</li>
</ol>
<hr />
<h2 id="detr-architecture">DETR Architecture</h2>
<p>The DETR model</p>
<ul>
<li>A set prediction loss (bipartite matching loss)</li>
<li>Architecture that predicts a set of objects and models their relation</li>
</ul>
<h3 id="object-detection-set-prediction-loss">Object detection set prediction loss</h3>
<p>The loss computation in DETR is divided into two main stages:  </p>
<ol>
<li><strong>Matching:</strong> Assigning predicted objects to ground-truth objects using bipartite matching.  </li>
<li><strong>Hungarian Loss Computation:</strong> Calculating the classification and bounding box losses based on the matched pairs.</li>
</ol>
<hr />
<p> <strong>1. Matching Stage</strong>
DETR generates <strong>a fixed number (N) of object queries</strong>, which are then matched to ground-truth objects using <strong>bipartite matching (Hungarian algorithm).</strong></p>
<ul>
<li><strong>Number of object queries (N) ≥ number of ground-truth objects</strong><br />→ If there are fewer ground-truth objects, &quot;no object&quot; class is assigned to the extra predictions.</li>
</ul>
<p><strong>Matching Cost Computation</strong>
<img alt="" src="https://velog.velcdn.com/images/davidlyoo/post/4e892849-5c9d-4a5c-a1da-964f65182425/image.png" /></p>
<p>The Hungarian algorithm calculates the <strong>matching cost</strong> to determine the optimal assignment between predictions and ground-truth objects. The matching cost considers:</p>
<ul>
<li><strong>Classification score:</strong> The probability of predicting the correct class.</li>
<li><strong>Bounding box similarity:</strong> Based on IoU between predicted and ground-truth boxes.</li>
</ul>
<p>This matching process <strong>serves the same role as heuristic-based assignment rules</strong> in traditional object detectors (e.g., anchor-based models), but <strong>ensures one-to-one matching without duplicates.</strong></p>
<hr />
<p><strong>2. Hungarian Loss Computation</strong>
<img alt="" src="https://velog.velcdn.com/images/davidlyoo/post/f09416ff-cfd8-4a71-bd4b-8fc0d316c3c1/image.png" /></p>
<p>Once the matching is determined, DETR computes the <strong>Hungarian Loss</strong>, which consists of two components:</p>
<p><strong>(1) Classification Loss</strong></p>
<ul>
<li>Uses <strong>Negative Log-Likelihood (NLL) Loss</strong> to optimize class predictions.  </li>
<li>Ensures that each predicted object is classified correctly.  </li>
<li>Includes an additional &quot;no object&quot; class to handle unmatched predictions.</li>
</ul>
<p><strong>(2) Bounding Box Loss</strong>
Since DETR directly predicts bounding boxes without using anchors, it requires a <strong>stable loss function</strong> that balances different object sizes.<br />To achieve this, DETR combines two loss functions:</p>
<ul>
<li><strong>L1 Loss:</strong> Minimizes the direct difference between predicted and ground-truth box coordinates.</li>
<li><strong>Generalized IoU (GIoU) Loss:</strong> Addresses scale differences and ensures stability.</li>
</ul>
<p>These two losses are <strong>linearly combined</strong> and <strong>normalized by the number of ground-truth objects</strong> in the batch.</p>
<hr />
<p><strong>Why Use Generalized IoU Loss?</strong></p>
<ul>
<li><strong>L1 Loss alone is scale-dependent</strong>, meaning smaller objects may have lower absolute errors but higher relative errors.  </li>
<li><strong>GIoU Loss introduces scale invariance</strong>, helping the model generalize better across different object sizes.  </li>
<li>Combining L1 and GIoU Loss ensures both <strong>precise localization</strong> and <strong>scale robustness</strong> in bounding box regression.</li>
</ul>
<hr />
<h3 id="detr-contains-three-main-components"><strong>DETR contains three main components</strong></h3>
<ul>
<li><ol>
<li>CNN backbone: using ResNet50 as a backbone network to extract a compact feature representation</li>
</ol>
</li>
<li><ol start="2">
<li>Encoder-decoder transformer</li>
</ol>
</li>
<li><ol start="3">
<li>Feed Forward Network (FFN): makes the final detection prediction</li>
</ol>
</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/davidlyoo/post/4b61f998-d49b-45f3-9639-10f316d56c1d/image.png" /></p>
<h4 id="backbone-network">Backbone Network</h4>
<ul>
<li>Starting from the initial image: getting image feature map</li>
<li>Backbone generates a lower-resolution activation map (e.g., C=2048, H = H0/32, W = W0/32)</li>
</ul>
<h4 id="encoder-decoder-transformer">Encoder-decoder Transformer</h4>
<ul>
<li><p><strong>Encoder</strong></p>
<ul>
<li>1x1 convolution reduces the channel dimension of the high-level activation map new feature map z</li>
<li>Each encoder layer has a standard architecture and consists of a Multi-Head Self Attention, FFN + fixed positional encoding that are added to the input of each attention layer</li>
</ul>
</li>
<li><p><strong>Decoder</strong></p>
<ul>
<li>The decoder follows the standard transformer architecture:<ul>
<li>Multi-Head Self-Attention (MHA)</li>
<li>Encoder-Decoder Cross-Attention Mechanism</li>
</ul>
</li>
</ul>
</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/davidlyoo/post/e4773940-eec8-4598-8a90-125082b6564e/image.png" /></p>
<p>Unlike standard transformers, DETR’s decoder processes all N objects in parallel at each decoder layer.</p>
<ul>
<li>Object Queries<ul>
<li>Learnable embeddings of size N, representing potential object detections.</li>
<li>These embeddings interact with the encoded feature map to extract object-specific information.</li>
</ul>
</li>
</ul>
<p>The object queries are added to the input of each attention layer, transforming them into output embeddings, which are then decoded independently into:</p>
<ul>
<li>Bounding box coordinates</li>
<li>Class labels</li>
</ul>
<h4 id="feed-forward-network-ffn">Feed Forward Network (FFN)</h4>
<ul>
<li>A three-layer perceptron (MLP) with ReLU activation and hidden dimension d.</li>
<li>A linear projection layer is used for final predictions.</li>
<li>Predicts normalized bounding box coordinates (center, height, width) relative to the input image.</li>
<li>A separate linear layer predicts class labels using a softmax function.</li>
<li>A fixed-size set of N bounding boxes is produced, where N is typically much larger than the actual number of objects in the image.<ul>
<li>An additional special class label (&quot;no object&quot;) is used to represent background regions.</li>
</ul>
</li>
</ul>
<h4 id="auxiliary-decoding-losses">Auxiliary decoding losses</h4>
<ul>
<li>Auxiliary losses are applied during training to help the model output the correct number of objects per class.</li>
<li>Hungarian loss and prediction FFNs are added after each decoder layer.</li>
<li>All prediction FFNs share their parameters across decoder layers.</li>
<li>A shared layer-normalization (LayerNorm) is used to normalize inputs to the prediction FFNs, ensuring consistent learning across decoder stages.</li>
</ul>
<hr />
<h1 id="performance">Performance</h1>
<p><strong>Comparison with Faster R-CNN</strong>
<img alt="" src="https://velog.velcdn.com/images/davidlyoo/post/6f267aee-4272-4e13-ace2-fabf0071b08a/image.png" /></p>
<p>Despite its simplicity, DETR achieves competitive performance with Faster R-CNN, demonstrating that transformers can replace region-based object detection frameworks. However, notable differences exist:</p>
<ul>
<li>DETR excels at detecting large objects, benefiting from the non-local computations of the transformer.</li>
<li>DETR struggles with small objects, as it lacks an explicit feature pyramid like FPN in Faster R-CNN.</li>
<li>Training Efficiency: DETR requires longer training schedules but eliminates heuristic-based hyperparameter tuning.</li>
</ul>
<hr />
<h1 id="conclusion">Conclusion</h1>
<p>DETR introduces a transformer-based object detection framework, marking a significant departure from traditional region-based architectures. This work highlights the importance of borrowing techniques from different research areas, such as leveraging self-attention mechanisms for object detection. While DETR shows strong performance on large objects, improving its ability to detect small objects remains a future research direction—similar to how FPN improved Faster R-CNN.</p>
<hr />
<h1 id="references">References</h1>
<p>Carion N, et al. (2020). End-to-end object detection with transformers. European Conference on Computer Vision (ECCV). <a href="https://doi.org/10.1007/978-3-030-58452-8_13">https://doi.org/10.1007/978-3-030-58452-8_13</a></p>