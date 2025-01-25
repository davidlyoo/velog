<h1 id="summary">Summary</h1>
<p>The <strong>Feature Pyramid Network (FPN)</strong> is designed to enhance <strong>multi-scale object detection</strong> by leveraging both <strong>high-level semantic features</strong> and <strong>low-level spatial details</strong>. Unlike previous approaches that relied on <strong>image pyramids</strong>, which are computationally expensive, FPN constructs a feature hierarchy within a deep CNN using a <strong>top-down pathway with lateral connections</strong>. This enables accurate detection across a wide range of object sizes while keeping computational costs low.</p>
<hr />
<h1 id="introduction">Introduction</h1>
<h3 id="recognizing-objects-at-different-scales-a-fundamental-challenge">Recognizing Objects at Different Scales: A Fundamental Challenge</h3>
<p>One of the most significant challenges in computer vision is <strong>detecting objects at vastly different scales</strong> within an image. Objects can appear at various sizes due to their distance from the camera, perspective transformations, and variations in image resolution. <strong>Traditional object detection models struggle to accurately recognize both small and large objects</strong> using a single feature representation.</p>
<h3 id="the-role-of-feature-pyramids-in-scale-invariant-object-detection">The Role of Feature Pyramids in Scale-Invariant Object Detection</h3>
<p>To address this challenge, <strong>feature pyramids have been widely adopted</strong> in computer vision tasks.
<strong>Image Pyramids</strong> are a classical approach that allow models to recognize objects at multiple scales by scanning over different positions and pyramid levels. This technique ensures scale-invariant feature extraction, allowing detectors to be more robust to size variations.
However, image pyramids have significant computational drawbacks:</p>
<ul>
<li>High computational cost: Constructing explicit image pyramids requires processing multiple resized versions of the same image, which increases inference time</li>
<li>Train-test inconsistency: Training deep learning models on multi-scale image pyramids is challenging and inefficient.</li>
</ul>
<h3 id="challenges-with-low-level-features-in-object-detection">Challenges with Low-Level Features in Object Detection</h3>
<p>High-resolution feature maps typically capture low-level features such as edges and textures.
However, relying solely on low-level features can degrade a model's representational capacity, as these features lack high-level semantic understanding and lead to <strong>poor localization</strong>.</p>
<hr />
<h1 id="background">Background</h1>
<p><img alt="" src="https://velog.velcdn.com/images/davidlyoo/post/fff4487c-2c07-4483-95ff-faf07b1a041a/image.png" /></p>
<h3 id="the-evolution-of-multi-scale-object-detection">The Evolution of Multi-Scale Object Detection</h3>
<p><strong>1. Image Pyramids: The Classical Approach</strong></p>
<ul>
<li>Early solutions relied on explicit <strong>image pyramids</strong>, which resized input images at multiple scales.</li>
<li>While effective, this method was computationally expensive and impractical for deep learning models.</li>
</ul>
<p><strong>2. Early CNN-Based Detector</strong></p>
<ul>
<li>Initial CNN models (e.g., AlexNet, VGG) used a <strong>single feature map</strong> for detection.</li>
<li>Limitations: Unable to detect objects of varying size effectively.</li>
</ul>
<p><strong>3. The shift to Deep Learning-Based Multi-Scale Feature Extraction</strong></p>
<ul>
<li><strong>Single Shot MultiBox Detector (SSD)</strong> attempted to use multiple feature maps from different levels of the network.</li>
<li>However, <strong>SSD relied on mid-level feature maps (Conv4_3)</strong> which lacked high-level semantic information, making it less effective for detecting small objects.</li>
</ul>
<p><strong>4. The Emergence of Feature Pyramid Networks (FPN)</strong></p>
<ul>
<li><strong>FPN introduced a top-down pathway with lateral connections</strong>, addressing the limitations of SSD.</li>
<li>This allowed lower-resolution feature maps to inherit high-level semantic information while preserving high-resolution spatial details.</li>
<li><strong>FPN effectively merged the advantages of both image pyramids and deep CNN feature maps.</strong></li>
</ul>
<hr />
<h1 id="feature-pyramid-network">Feature Pyramid Network</h1>
<h2 id="fpn-architecture">FPN Architecture</h2>
<p>FPN enhances multi-scale feature representation by integrating high-level semantic features with low-level spatial details through a <strong>feature hierarchy consisting of a top-down pathway and lateral connections</strong>.</p>
<p>FPN consists of two key architectural components:</p>
<ul>
<li><strong>1. Bottom-up Pathway</strong></li>
<li><strong>2. Top-down Pathway &amp; Lateral Connections</strong></li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/davidlyoo/post/dbcaac9e-0d14-4181-91ee-9223007f7560/image.png" /></p>
<h4 id="1-bottom-up-pathway">1. Bottom-up Pathway</h4>
<p><strong>This follows the standard feedforward computation of a deep CNN (e.g., ResNet) to generate multi-resolution feature maps.</strong></p>
<ul>
<li>The CNN backbone processes the input image through multiple layers, progressively reducing spatial resolution but increasing feature abstraction.</li>
<li>Feature maps at different stages capture different levels of information:<ul>
<li><strong>Shallow layers</strong>: High-resolution but low-level details (e.g., edges, textures).</li>
<li><strong>Deep Layers</strong>: Low-resolution but high-level semantic meaning.</li>
</ul>
</li>
</ul>
<p><strong>In FPN, the final output of each stage in ResNet is used to build the feature pyramid.</strong></p>
<ul>
<li>표 md 형식으로 제공할 것 바로 복사 할 수 있게</li>
</ul>
<p><strong>Standard object detection models (e.g., Faster R-CNN) typically use only C4 or C5, but FPN utilizes all feature levels.</strong></p>
<h4 id="2-top-down-pathway--lateral-connections">2. Top-down Pathway &amp; Lateral Connections</h4>
<p><strong>The top-down pathway and lateral connections are what make FPN fundamentally different from conventional CNN-based object detection models.</strong>
CNNs naturally produce high-resolution feature maps in early layers and low-resolution, high-semantic feature maps in deeper layers.
<strong>(2-1) Top-down Pathway</strong></p>
<ul>
<li>Starts from C5 (highest-level feature map) and progressively upsamples it.</li>
<li>Each upsampled feature map is then merged with the corresponding bottom-up feature map (e.g., C4, C3, C2).</li>
<li>This process enhances the semantic richness of lower layers while maintaining their high resolution.</li>
</ul>
<p><strong>(2-2) Lateral Connections</strong></p>
<ul>
<li>Each bottom-up feature map (C2, C3, C4) is connected laterally to its corresponding upsampled feature map from the top-down pathway.</li>
<li>A 1 x 1 convolution is applied before merging to normalize channel dimensions.</li>
<li>Final feature maps (P2, P3, P4, P5) are obtained after fusion.</li>
</ul>
<p>This hierarchical feature fusion enables small objects to retain high-resolution details while leveraging strong semantic context.</p>
<h3 id="feature-pyramid-networks-for-rpn-and-fast-r-cnn">Feature Pyramid Networks for RPN and Fast R-CNN</h3>
<p>For a detailed explanation of the original Region Proposal Network (RPN) and Fast R-CNN, refer to my previous <a href="https://velog.io/@davidlyoo/Faster-R-CNN-Paper-Review-An-End-to-End-Solution-for-Efficient-Object-Detection">Fast R-CNN Paper Review</a>
This section focuses on how FPN enhances both components.</p>
<hr />
<h2 id="applying-fpn-to-rpn-and-fast-r-cnn">Applying FPN to RPN and Fast R-CNN</h2>
<p>FPN improves both <strong>RPN</strong> (proposal generation) and <strong>Fast R-CNN</strong> (detection) by introducing <strong>multi-scale feature representations</strong> through a <strong>feature pyramid</strong>.  
Instead of relying on a <strong>single feature level (e.g., C4 in ResNet)</strong>, FPN <strong>distributes computation across multiple levels</strong>, ensuring <strong>better detection for small, medium, and large objects</strong>.  </p>
<h3 id="1-removal-of-conv1-in-rpn">1. Removal of Conv1 in RPN</h3>
<ul>
<li><strong>Standard RPN includes an additional Conv1 layer</strong> before the 3×3 convolution.  </li>
<li><strong>FPN removes this Conv1 layer</strong>, reducing redundancy while improving memory efficiency.  </li>
<li>The <strong>3×3 convolution and sibling 1×1 convolutions</strong> are applied <strong>directly to each level of the feature pyramid</strong>, ensuring consistency across scales.  </li>
</ul>
<h3 id="2-anchor-assignment-across-feature-pyramid-levels">2. Anchor Assignment Across Feature Pyramid Levels</h3>
<p>Rather than applying <strong>multi-scale anchors to a single feature map</strong>, FPN assigns <strong>fixed-scale anchors</strong> to different feature levels, effectively <strong>distributing object detection tasks across multiple scales</strong>. </p>
<table>
<thead>
<tr>
<th align="center">Feature Level</th>
<th align="center">Anchor Size (pixels²)</th>
</tr>
</thead>
<tbody><tr>
<td align="center">P2</td>
<td align="center">32²</td>
</tr>
<tr>
<td align="center">P3</td>
<td align="center">64²</td>
</tr>
<tr>
<td align="center">P4</td>
<td align="center">128²</td>
</tr>
<tr>
<td align="center">P5</td>
<td align="center">256²</td>
</tr>
<tr>
<td align="center">P6</td>
<td align="center">512²</td>
</tr>
</tbody></table>
<ul>
<li>Each feature level retains <strong>three aspect ratios</strong> (<strong>1:1, 1:2, 2:1</strong>), leading to a total of <strong>15 anchors across the pyramid</strong>.  </li>
</ul>
<hr />
<h2 id="feature-pyramid-based-rpn">Feature Pyramid-based RPN</h2>
<p>The <strong>Region Proposal Network (RPN)</strong> in Faster R-CNN <strong>generates object proposals</strong> but originally operates on a <strong>single feature level</strong> (e.g., C4 in ResNet). This leads to <strong>poor recall for small objects</strong>.</p>
<h3 id="standard-rpn-workflow">Standard RPN Workflow</h3>
<ol>
<li>Extract a <strong>feature map</strong> from a CNN backbone.  </li>
<li>Apply a <strong>3×3 convolution</strong> to extract region-specific features.  </li>
<li>Use <strong>two sibling 1×1 convolutions</strong>:  <ul>
<li><strong>One for classification</strong> (object vs. background).  </li>
<li><strong>One for bounding box regression</strong>.  </li>
</ul>
</li>
<li>Predict <strong>multi-scale anchors</strong> on the same feature map.  </li>
</ol>
<h3 id="limitations-of-standard-rpn">Limitations of Standard RPN</h3>
<ul>
<li><strong>Fixed feature scale</strong>: The use of a <strong>single feature map</strong> makes it difficult to detect objects of varying sizes.  </li>
<li><strong>Anchor scale mismatch</strong>: Since predefined anchors are applied to a single feature level, <strong>small objects may not activate deep feature maps effectively</strong>, leading to <strong>poor localization</strong>.  </li>
</ul>
<h3 id="how-fpn-enhances-rpn">How FPN Enhances RPN</h3>
<ul>
<li>Instead of <strong>a single feature map</strong>, <strong>RPN runs on each level of the feature pyramid (P2–P6)</strong>.  </li>
<li>Objects of varying sizes are detected <strong>at their corresponding feature levels</strong>, thereby enhancing <strong>recall and localization accuracy</strong>.  </li>
<li>This allows <strong>better small-object detection</strong> without increasing computational overhead.  </li>
</ul>
<hr />
<h2 id="feature-pyramid-networks-for-fast-r-cnn">Feature Pyramid Networks for Fast R-CNN</h2>
<p>The standard <strong>Fast R-CNN</strong> model in Faster R-CNN <strong>extracts RoIs from a single feature map (C4 in ResNet)</strong>, leading to <strong>suboptimal feature extraction</strong> for objects of different sizes.  </p>
<h3 id="limitations-of-standard-fast-r-cnn">Limitations of Standard Fast R-CNN</h3>
<ol>
<li><strong>Fixed feature resolution</strong>: Using a <strong>single feature level</strong> reduces performance when detecting <strong>objects at varying scales</strong>.  </li>
<li><strong>Suboptimal feature extraction</strong>: <strong>Small RoIs</strong> may not capture enough fine-grained details, <strong>leading to missed detections</strong>.  </li>
</ol>
<h3 id="how-fpn-enhances-fast-r-cnn">How FPN Enhances Fast R-CNN</h3>
<ul>
<li><strong>FPN assigns RoIs to different feature levels</strong> based on their sizes, ensuring <strong>scale-aware feature extraction</strong>.  </li>
<li><strong>Small RoIs</strong> are mapped to <strong>higher-resolution feature maps (P2, P3)</strong>, while <strong>large RoIs</strong> are assigned to <strong>lower-resolution feature maps (P5, P6)</strong>.  </li>
</ul>
<h3 id="roi-assignment-in-fpn-fast-r-cnn">RoI Assignment in FPN-Fast R-CNN</h3>
<p>The <strong>feature level Pk</strong> for each <strong>Region of Interest (RoI)</strong> is determined using:  </p>
<p><img alt="" src="https://velog.velcdn.com/images/davidlyoo/post/9b7f6933-8f22-4927-a1d4-90246415b5ce/image.png" /></p>
<ul>
<li>k0 : predefined level index (default: P4).  </li>
<li>w, h : width and height of the RoI.  </li>
<li>Small RoIs are assigned to higher-resolution feature maps (P2, P3).</li>
<li>Large RoIs are assigned to lower-resolution feature maps (P5, P6).</li>
</ul>
<p>This ensures that <strong>small RoIs use higher-resolution features, and large RoIs use lower-resolution features</strong>, improving overall detection performance.  </p>
<h1 id="performance">Performance</h1>
<h3 id="coco-benchmark-results">COCO Benchmark Results</h3>
<table>
<thead>
<tr>
<th align="center"><strong>Model</strong></th>
<th align="center"><strong>AP</strong></th>
<th align="center"><strong>AP@50</strong></th>
<th align="center"><strong>AP@75</strong></th>
<th align="center"><strong>AP (small)</strong></th>
<th align="center"><strong>AP (medium)</strong></th>
<th align="center"><strong>AP (large)</strong></th>
</tr>
</thead>
<tbody><tr>
<td align="center">Faster R-CNN (C4)</td>
<td align="center">21.9</td>
<td align="center">42.7</td>
<td align="center">21.2</td>
<td align="center">5.8</td>
<td align="center">24.1</td>
<td align="center">31.4</td>
</tr>
<tr>
<td align="center"><strong>Faster R-CNN (FPN)</strong></td>
<td align="center"><strong>36.2</strong></td>
<td align="center"><strong>59.1</strong></td>
<td align="center"><strong>39.0</strong></td>
<td align="center"><strong>18.2</strong></td>
<td align="center"><strong>39.0</strong></td>
<td align="center"><strong>48.2</strong></td>
</tr>
</tbody></table>
<ul>
<li><strong>Significant improvement for small objects</strong> (<strong>AP: 18.2 vs. 5.8</strong>).  </li>
<li><strong>Overall AP increased by 14.3 points compared to Faster R-CNN without FPN</strong>.  </li>
</ul>
<hr />
<h2 id="ablation-study-how-important-are-fpn-components">Ablation Study: How Important Are FPN Components?</h2>
<p>To understand <strong>which components of FPN contribute most to performance improvements</strong>, we analyze three key factors:</p>
<ol>
<li><strong>Effect of FPN in RPN (Feature Pyramid-based RPN)</strong></li>
<li><strong>Importance of Lateral Connections</strong></li>
<li><strong>Effect of Feature Pyramid Representation (Multi-Level RoI Assignment)</strong></li>
</ol>
<h3 id="1-effect-of-fpn-in-rpn-feature-pyramid-based-rpn">1. Effect of FPN in RPN (Feature Pyramid-based RPN)</h3>
<table>
<thead>
<tr>
<th align="center"><strong>Method</strong></th>
<th align="center"><strong>AR@1k</strong></th>
</tr>
</thead>
<tbody><tr>
<td align="center">Standard RPN (C4)</td>
<td align="center">48.3</td>
</tr>
<tr>
<td align="center"><strong>FPN in RPN</strong></td>
<td align="center"><strong>56.3 (+8.0)</strong></td>
</tr>
</tbody></table>
<ul>
<li><strong>Applying FPN to RPN improves region proposal recall (AR@1k) by 8.0 points</strong>.  </li>
<li>Since <strong>anchors are now assigned across multiple feature levels</strong>, recall improves significantly, <strong>especially for small objects</strong>.  </li>
<li><strong>Conclusion</strong>: <strong>Multi-scale feature extraction at the proposal stage is essential for improving recall.</strong>  </li>
</ul>
<hr />
<h3 id="2-importance-of-lateral-connections">2. Importance of Lateral Connections</h3>
<table>
<thead>
<tr>
<th align="center"><strong>Method</strong></th>
<th align="center"><strong>AP</strong></th>
</tr>
</thead>
<tbody><tr>
<td align="center">FPN without lateral connections</td>
<td align="center">26.5</td>
</tr>
<tr>
<td align="center"><strong>FPN with lateral connections</strong></td>
<td align="center"><strong>36.2</strong></td>
</tr>
</tbody></table>
<ul>
<li>Removing <strong>1×1 lateral connections</strong> causes a <strong>9.7-point drop in AP</strong>, showing that these connections are crucial.  </li>
<li><strong>Why?</strong> Lateral connections <strong>preserve fine-grained spatial details</strong> from low-level feature maps, which is <strong>essential for small-object detection</strong>.  </li>
<li><strong>Conclusion</strong>: <strong>Lateral connections play a key role in refining high-resolution features for better localization.</strong>  </li>
</ul>
<hr />
<h3 id="3-effect-of-feature-pyramid-representation-multi-level-roi-assignment">3. Effect of Feature Pyramid Representation (Multi-Level RoI Assignment)</h3>
<table>
<thead>
<tr>
<th align="center"><strong>Method</strong></th>
<th align="center"><strong>AP</strong></th>
</tr>
</thead>
<tbody><tr>
<td align="center">RoI assignment to a single level (P2)</td>
<td align="center">28.0</td>
</tr>
<tr>
<td align="center"><strong>Multi-Level RoI Assignment (FPN)</strong></td>
<td align="center"><strong>36.2</strong></td>
</tr>
</tbody></table>
<ul>
<li>Assigning <strong>all RoIs to P2 alone (highest-resolution feature map)</strong> performs <strong>worse</strong> than multi-level assignment.  </li>
<li><strong>Why?</strong> Using <strong>only P2 results in excessive small-object anchors</strong>, but <strong>ignores the semantic richness of deeper feature maps</strong>.  </li>
<li><strong>Conclusion</strong>: A <strong>multi-scale RoI pooling strategy improves object detection at all scales</strong>.  </li>
</ul>
<hr />
<p>From ablation study</p>
<ol>
<li><strong>FPN-RPN improves region proposal recall</strong> (<strong>+8.0 AR@1k</strong>), helping detect small objects early.  </li>
<li><strong>Lateral connections are essential</strong> (<strong>+9.7 AP</strong>), refining fine-grained spatial features.  </li>
<li><strong>Feature pyramids for RoI assignment significantly improve detection</strong> (<strong>+8.2 AP</strong>).  </li>
</ol>
<hr />
<h1 id="conclusion">Conclusion</h1>
<p>FPN has revolutionized multi-scale object detection by seamlessly integrating high-level semantic features with low-level spatial details. By introducing a <strong>top-down pathway with lateral connections</strong>, FPN enables robust detection across a wide range of object sizes while maintaining computational efficiency.
A key advantage of FPN is its ability to enhance feature representation across different scales without incurring excessive computational costs. As a result, FPN has established itself as a fundamental building block for modern object detection frameworks and continues to shape the evolution of deep learning-based detection architectures.</p>
<hr />
<h1 id="references">References</h1>
<p>Lin, T.-Y., Dollár, P., Girshick, R., He, K., Hariharan, B., &amp; Belongie, S. (2017). Feature pyramid networks for object detection. <em>Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition (CVPR)</em>, 2017, 2117–2125. <a href="https://doi.org/10.1109/CVPR.2017.106">https://doi.org/10.1109/CVPR.2017.106</a></p>