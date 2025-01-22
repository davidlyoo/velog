<h1 id="summary">Summary</h1>
<p>Faster R-CNN combines a Region Proposal Network with Fast R-CNN, enabling a fully end-to-end, CNN-based approach to generating high-quality region proposals and performing object classification and bounding-box regression. This design significantly improves both accuracy and speed compared to traditional proposal methods like Selective Search.</p>
<hr />
<h1 id="introduction">Introduction</h1>
<p>Conventional two-stage detection approaches often rely on <strong>external region proposal algorithms</strong> (e.g., Selective Search, Edge Boxes) for candidate regions before classification. Although effective, these approaches are <strong>computationally expensive</strong> and relatively <strong>slow</strong>. Faster R-CNN addresses these shortcomings by learning region proposals directly via a CNN, offering an end-to-end and more efficient pipeline.</p>
<hr />
<h1 id="object-detection-with-faster-r-cnn">Object Detection with Faster R-CNN</h1>
<p>Model Overview:
<img alt="Diagram of Faster R-CNN" src="https://velog.velcdn.com/images/davidlyoo/post/472d25f4-21cb-4f1b-84e7-8cd8244450fa/image.png" /></p>
<ul>
<li><strong>1. Input Image → Shared Convolutional Layers</strong><ul>
<li>Pass the input image through a CNN backbone (e.g., ZF or VGG16) to extract a shared convolutional feature map.</li>
</ul>
</li>
<li><strong>2. Region Proposal Network (RPN)</strong><ul>
<li>Slides over the feature map to generate candidate object proposals (anchors) and corresponding objectness scores.</li>
</ul>
</li>
<li><strong>3. RoI Pooling</strong><ul>
<li>Map each proposed region back onto the shared feature map, then apply RoI Pooling to obtain fixed-sized feature maps for each region.</li>
</ul>
</li>
<li><strong>4. Fast R-CNN Head</strong><ul>
<li>Feed these pooled features into Fully Connected Layers (FCLs) for final classification and bounding-box regression.</li>
</ul>
</li>
</ul>
<hr />
<h2 id="faster-r-cnn-workflow">Faster R-CNN Workflow</h2>
<ul>
<li><strong>Step 1</strong>: Input image → <strong>CNN Backbone</strong> → Convolutional feature map</li>
<li><strong>Step 2</strong>: <strong>RPN</strong> → Generate region proposals (anchor boxes + objectness scores)</li>
<li><strong>Step 3</strong>: <strong>RoI Pooling</strong> → Project proposals onto the shared feature map, then pool into a fixed size</li>
<li><strong>Step 4</strong>: <strong>Classification &amp; Bounding-Box Regression</strong> → Output final labels and refined boxes</li>
</ul>
<hr />
<h2 id="detailed-architecture-of-faster-r-cnn">Detailed Architecture of Faster R-CNN</h2>
<h3 id="1-region-proposal-network-rpn">1. Region Proposal Network (RPN)</h3>
<p>The RPN is the cornerstone of Faster R-CNN by replacing traditional region proposal methods like Selective Search. Sharing convolutional layers with Fast R-CNN significantly reduces computational overhead.</p>
<p><strong>RPN Workflow</strong>
<strong>1. Sliding Window</strong>: A small n×n (e.g., 3×3) sliding window is applied to the shared feature map to extract local features.
<strong>2. Anchor Boxes</strong>: For each sliding window position, k anchor boxes (e.g., 9 anchors) with different scales and aspect ratios are generated.
<strong>3. Binary Classification</strong>: Each anchor (2k outputs) is classified as an “object” or “background.”
<strong>4. Bounding-Box Regression</strong>: Each anchor (4k outputs) is refined by predicting offsets (x, y, w, h) to best fit the object.</p>
<p><strong>Why RPN Matters</strong></p>
<ul>
<li>Replaces slow region proposal algorithms (e.g., Selective Search).</li>
<li>Shares convolutional layers with Fast R-CNN, minimizing extra computation.</li>
<li>Handles various object sizes/aspect ratios using multiple anchors. </li>
</ul>
<p><strong>Non-Maximum Suppression (NMS) After RPN</strong></p>
<p>Once the RPN outputs objectness scores and refined boxes, an NMS step is applied to remove highly overlapped proposals that likely refer to the same object. This reduces redundancy and yields a more compact set of candidate regions.</p>
<h3 id="2-fast-r-cnn-head">2. Fast R-CNN Head</h3>
<p>Once the RPN generates high-quality proposals, the Fast R-CNN head processes these proposals via RoI Pooling to produce fixed-sized feature maps.
It then performs:</p>
<ul>
<li><p><strong>Multi-Class Classification</strong>: Determines the object category (plus background).</p>
</li>
<li><p><strong>Bounding-Box Regression</strong>: Further refine each proposal to match ground truth coordinates.</p>
</li>
</ul>
<p>By combining the RPN for region proposals and Fast R-CNN for detection, Faster R-CNN forms a unified, fully CNN-based framework for end-to-end object detection.</p>
<h3 id="3-anchor-box">3. Anchor Box</h3>
<p><img alt="Anchor Box" src="https://velog.velcdn.com/images/davidlyoo/post/07debee7-afdb-4ab9-adf8-179a8ba5674b/image.png" /></p>
<p><strong>What are Anchor Boxes?</strong></p>
<ul>
<li><p>Anchor boxes are <strong>predefined bounding-boxes</strong> of various shapes and sizes placed at each position of the feature map.</p>
</li>
<li><p>In practice, 3 scales x 3 aspect ratios = 9 anchors per position.</p>
</li>
<li><p>Single-scale features, multi-scale predictions— each anchor has its own prediction function.</p>
</li>
</ul>
<p><strong>Positive and Negative Anchors</strong></p>
<ul>
<li><strong>Positive anchors</strong>: those whose <strong>IoU</strong> with a ground truth box is above a certain threshold (e.g., 0.7).</li>
<li><strong>Negative anchors</strong>: those whose IoU with every ground truth box is below a lower threshold (e.g., 0.3).</li>
</ul>
<h3 id="outputs-of-rpn">Outputs of RPN:</h3>
<p><img alt="RPN" src="https://velog.velcdn.com/images/davidlyoo/post/ee754fa2-3f57-4b1b-90bb-5b99e1cf0f13/image.png" /></p>
<ol>
<li><p>Objectness Scores</p>
<ul>
<li>For each anchor, the RPN outputs the probability of containing an object vs. not containing an object (18 outputs for 9 anchors).</li>
</ul>
</li>
<li><p>Bounding-Box Refinements</p>
<ul>
<li>For each anchor, the RPN predicts 4 offsets (x, y, w, h) to fine-tune the anchor location and size (36 outputs for 9 anchors).</li>
</ul>
</li>
</ol>
<h3 id="4-rpn-loss-function">4. RPN Loss Function</h3>
<p><img alt="Loss Function" src="https://velog.velcdn.com/images/davidlyoo/post/08831f65-2241-485e-9979-9c7fe44533c5/image.png" /></p>
<p>The RPN loss function typically consists of two parts:
<strong>1. Classification Loss</strong></p>
<ul>
<li>A binary classification between object(positive anchor) and background(negative anchor).</li>
</ul>
<p><strong>2. Regression Loss</strong></p>
<ul>
<li><p>A smooth L1 loss on the predicted offsets (x,y,w,h) to align each positive anchor with its corresponding ground truth box.</p>
</li>
<li><p>Only positive anchors contribute to the regression loss, while all anchors (positive + negative) are used for classification.</p>
</li>
</ul>
<hr />
<h2 id="training-and-optimization">Training and Optimization</h2>
<p><strong>Faster R-CNN employs a two-step training process:</strong></p>
<ul>
<li><p>1) Alternating Training
RPN and Fast R-CNN are trained alternately to allow the RPN to generate better proposals. This iterative process improves both RPN and Fast R-CNN in tandem.</p>
<ul>
<li><strong>Step 1</strong>. Train RPN alone to generate high-quality proposals.</li>
<li><strong>Step 2</strong>. Use the RPN proposals to train Fast R-CNN.</li>
<li><strong>Step 3</strong>. Fine-tune RPN again with updated Fast R-CNN weights.</li>
<li><strong>Step 4</strong>. Retrain Fast R-CNN.</li>
</ul>
</li>
<li><p>2) End-to-End Training
After the alternating steps, Faster R-CNN can be fine-tuned <strong>jointly</strong>, optimizing the shared layers, RPN, and detection head end-to-end for better performance and efficiency.</p>
</li>
</ul>
<hr />
<h1 id="conclusion">Conclusion</h1>
<p>Faster R-CNN incorporates the <strong>RPN</strong> and <strong>Fast R-CNN</strong> into a unified, fully <strong>CNN-based pipeline</strong>, delivering <strong>significant improvements</strong> in both speed and accuracy for object detection. By <strong>sharing convolutional features</strong> for both proposal generation and classification, it eliminates the need for slow external proposals and achieves near real-time performance. This framework has laid the groundwork for many subsequent two-stage detectors. I believe this architectural design offers ample room for further studies and may serve as a guiding blueprint for future research.</p>
<hr />
<h1 id="performance">Performance</h1>
<p><img alt="Detection Results" src="https://velog.velcdn.com/images/davidlyoo/post/9482a78b-d4dc-492d-84c0-6e9807cdcd45/image.png" /></p>
<ul>
<li>As illustrated in the first image, Faster R-CNN drastically reduces the number of region proposals compared to Selective Search-based methods (SS). It minimizes computational overhead while improving detection quality.</li>
</ul>
<p><img alt="Timing" src="https://velog.velcdn.com/images/davidlyoo/post/baabe42a-c2d6-44b2-ab9d-b568da8d8044/image.png" /></p>
<ul>
<li>By replacing slow proposal methods with the RPN, the system's frame rate increases from around <strong>0.5</strong> to about <strong>5 fps</strong> on VGG16 and even higher with more efficient models.</li>
</ul>
<hr />
<h1 id="references">References</h1>
<p>Ren, S., He, K., Girshick, R., &amp; Sun, J. (2017). Faster R-CNN: Towards real-time object detection with region proposal networks. IEEE Transactions on Pattern Analysis and Machine Intelligence, 39(6), 1137–1149.
<a href="https://doi.org/10.1109/tpami.2016.2577031">https://doi.org/10.1109/tpami.2016.2577031</a></p>
<p>Li, F.-F., Jonhson, J., &amp; Yeung, S. (2017). Lecture 11: Detection and segmentation. <a href="https://cs231n.stanford.edu/slides/2017/cs231n_2017_lecture11.pdf">https://cs231n.stanford.edu/slides/2017/cs231n_2017_lecture11.pdf</a></p>