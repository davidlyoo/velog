<h1 id="summary">Summary</h1>
<p>Region-based Convolutional Neural Network(R-CNN) is a foundational model in object detection, which revolutionized object detection by introducing a method that combines region proposals with deep learning, setting the stage for significant advancements in the field.</p>
<hr />
<h1 id="introduction">Introduction</h1>
<p>Before R-CNN, object detection tasks relied on traditional methods (e.g., the sliding window approach).
Traditional methods were not only computationally expensive but also struggled with detecting objects of varying sizes and shapes.
R-CNN introduced a novel paradigm by combining <strong>region proposals</strong> with <strong>deep convolutional neural networks(CNNs)</strong> to address these issues.</p>
<p><img alt="" src="https://velog.velcdn.com/images/davidlyoo/post/8c312029-05e3-42bb-933f-01d0c1daa88f/image.png" />
This innovation marked a significant milestone, laying the foundation for modern object detection models.</p>
<hr />
<h1 id="object-detection-with-r-cnn">Object Detection with R-CNN</h1>
<p>Model Overview:
<img alt="" src="https://velog.velcdn.com/images/davidlyoo/post/82c549b1-13ef-465a-835c-f093774a27c0/image.png" /></p>
<h2 id="r-cnn-workflow">R-CNN Workflow</h2>
<ul>
<li><ol>
<li>Run a selective search on the image to extract around 2,000 region proposals.</li>
</ol>
</li>
<li><ol start="2">
<li>Warp each proposal into 227x227.</li>
</ol>
</li>
<li><ol start="3">
<li>Extract a 4096-dimensional feature vector by forward propagating it through the pre-trained CNN.</li>
</ol>
</li>
<li><ol start="4">
<li>Score each extracted feature vector using the SVM(Support Vector Machine) trained for that class.</li>
</ol>
</li>
<li><ol start="5">
<li>Apply non-maximum suppression. </li>
</ol>
</li>
</ul>
<h2 id="detailed-architecture-of-r-cnn">Detailed Architecture of R-CNN</h2>
<p> <img alt="" src="https://velog.velcdn.com/images/davidlyoo/post/32e12a63-4a65-4d48-8297-bac0c0e542fa/image.png" /></p>
<h3 id="0-input-image">0. Input Image</h3>
<h3 id="1-region-proposal-generation">1. Region Proposal generation:</h3>
<ul>
<li>These proposals are candidate regions that might contain objects.</li>
</ul>
<h3 id="--selective-search">- Selective Search</h3>
<p><img alt="" src="https://velog.velcdn.com/images/davidlyoo/post/5e47cad6-f019-4e1e-b1e7-2531ba199f2d/image.png" /></p>
<ul>
<li><strong>Step 1</strong>. Initial Sub-Segmentation<ul>
<li>An image is segmented into small regions, each representing a part of an object (e.g., uniform textures or colors).</li>
</ul>
</li>
<li><strong>Step 2</strong>. Recursive Combination of Regions<ul>
<li>A greedy algorithm combines the most similar regions into larger regions iteratively</li>
<li>Similarity is measured based on color, size and compatibility.</li>
</ul>
</li>
<li><strong>Step 3</strong>. Candidate Object Locations<ul>
<li>Generated regions are used as proposals for possible object locations</li>
</ul>
</li>
</ul>
<h3 id="2-feature-extraction">2. Feature Extraction:</h3>
<h5 id="warping-region-proposals">Warping Region Proposals</h5>
<ul>
<li>To ensure compatibility with the CNN's input size, all region proposals are resized to a fixed size (e.g., 227x227).</li>
<li>This warping can cause distortion, potentially leading to a loss of detail especially in regions with extreme aspect ratios.</li>
</ul>
<h5 id="cnn-architecture">CNN Architecture</h5>
<ul>
<li>R-CNN uses AlexNet as its backbone network for feature extraction.</li>
<li>Each proposal is passed through the network and a 4096-dimensional feature vector is extracted from the fc7 layer. (network consists of five convolutional layers and two fully connected layers) to extract feature vectors.</li>
<li>These vectors capture semantic and spatial information, enabling robust classification.</li>
</ul>
<h3 id="3-classification">3. Classification:</h3>
<ul>
<li>The extracted features are classified using SVMs.</li>
<li>A reason for using SVMs instead of a CNN classifier is that SVMs demonstrated a 4% better performance on mAP(mean Average Precision).</li>
<li>A separate SVM is trained for each object class to classify proposals as a specific object class or background.</li>
</ul>
<h3 id="4-bounding-box-regressionlocalization">4. Bounding-box regression(Localization):</h3>
<ul>
<li>Bounding Box Regression refines initial region proposals to match better the ground truth bounding boxes.</li>
<li>Unlike traditional methods (e.g., Deformable Part Models), R-CNN uses <strong>pool5 features</strong>, providing richer and more semantic representations.</li>
</ul>
<h4 id="the-goal-of-the-bounding-box-regressor-is-to-find-an-optimal-transformation-that-maps-p-to-g">The goal of the bounding-box regressor is to find an optimal transformation that maps P to G</h4>
<p>   <img alt="" src="https://velog.velcdn.com/images/davidlyoo/post/cf499568-8548-40ea-b780-6f7295d2f586/image.png" /></p>
<p>Proposals(P) and ground truth boxes are represented as:</p>
<pre><code>            P = (x, y, w, h),  G = (Gx, Gy, Gw, Gh)

         x, y: Center coordinates of the box
         w, h: Width and height of the box</code></pre><p>The goal is to learn a function d that transforms P to make it as close as possible to G.</p>
<p>The function adjusts:</p>
<pre><code>    x, y: By translating the box's center
    w, h: By scaling the width and height (proportionally to the image dimensions)</code></pre><p> The refined bounding box G^ is computed as:
<img alt="" src="https://velog.velcdn.com/images/davidlyoo/post/b6620139-7bff-4c18-b18e-43c58e60ead7/image.png" /></p>
<p>And d-functions are parameterized as:
<img alt="" src="https://velog.velcdn.com/images/davidlyoo/post/338b8fd1-b892-423f-9504-6465ed656e88/image.png" /></p>
<p>, where w is a vector of learnable model parameters. 
(Φ: linear function of the pool5 features)
w can be learned by optimizing the regularized least squares objective (ridge regression):
<img alt="" src="https://velog.velcdn.com/images/davidlyoo/post/da552ca6-d69c-458d-be9d-f10c55cca0c9/image.png" />
(λ is set to 1000, as determined based on a validation set)</p>
<p>The regression targets t for the training pair (P, G) are defined as:
<img alt="" src="https://velog.velcdn.com/images/davidlyoo/post/eb6c7205-20af-4a9f-876c-30e97dcecfe5/image.png" /></p>
<p>Details are provided in Appendix C</p>
<h3 id="5-non-maximum-suppressionnms">5. Non-Maximum Suppression(NMS):</h3>
<ul>
<li><strong>Purpose</strong>:<ul>
<li>To remove redundant bounding boxes overlapping, ensuring only one bounding box per object is retained.</li>
</ul>
</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/davidlyoo/post/51bbe6e1-2098-48f1-ac4e-972ff1b203de/image.png" /></p>
<ul>
<li><strong>How It Works:</strong><ul>
<li>Intersection over Union(IoU) is used as a metric to evaluate the overlap between bounding boxes.</li>
</ul>
</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/davidlyoo/post/7a064110-7617-4d2d-aa7d-ccaf47b77481/image.png" /></p>
<ul>
<li>During NMS, boxes with <strong>IoU</strong> above a certain threshold(e.g., 0.5) are considered redundant, and only the box with the highest confidence score is retained.</li>
</ul>
<hr />
<h3 id="advantages">Advantages:</h3>
<ul>
<li><ol>
<li><strong>Improved Accuracy</strong>: By leveraging deep learning-based feature extraction and fine-tuning, R-CNN outperforms traditional object detection methods.</li>
</ol>
</li>
<li><ol start="2">
<li><strong>Flexible Feature Extraction</strong>: By using pre-trained CNNs, it becomes easier to adapt to new datasets with limited data.</li>
</ol>
</li>
</ul>
<h3 id="limitations-of-rcnn">Limitations of RCNN:</h3>
<ul>
<li><ol>
<li><strong>Slow training and inference</strong>
: Each Region proposal is processed independently through the CNN and implemented on the CPU.</li>
</ol>
</li>
<li><ol start="2">
<li><strong>High storage requirements</strong>
: Features for all region proposals are pre-computed and stored, requiring significant memory.</li>
</ol>
</li>
<li><ol start="3">
<li><strong>Multi-stage training</strong>
: Separate training stages are needed for the CNN, SVMs and bounding box regressors.</li>
</ol>
</li>
</ul>
<hr />
<h1 id="conclusion">Conclusion</h1>
<p>-
R-CNN revolutionized object detection by introducing <strong>region-based feature extraction</strong> and combining it with deep learning.
R-CNN laid the foundation for more advanced models like <strong>Fast R-CNN</strong> and <strong>Faster R-CNN</strong>, enabling end-to-end training and shared computation.
The innovations in R-CNN particularly fine-tuning and bounding-box regression remain foundational in modern object detection.</p>
<ul>
<li>Despite its multi-stage training and computational inefficiency, the paper's groundbreaking use of CNNs for object detection impressed me, as it overcame traditional performance limitations and opened new possibilities in the field.</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/davidlyoo/post/5bbdd321-559f-44f0-87c4-b00042c5569b/image.png" /></p>
<ul>
<li>The table presents the performance of R-CNN obtained by training an SVM using feature vectors extracted from each layer of the CNN.</li>
<li>Also compares the mAP with and without fine-tuning, highlighting the impact of fine-tuning and bounding box regression on performance.</li>
<li>The results demonstrate that the combination of fine-tuning and bounding-box regression yields the highest performance. Fine-tuning optimizes the feature representations for the specific dataset, while bounding-box regression refines localization accuracy.</li>
</ul>
<h2 id="performance">Performance:</h2>
<p>R-CNN achieved an mAP of 58.5% on the PASCAL VOC 2012 dataset</p>
<hr />
<h1 id="references">References</h1>
<p>Girshick, R., Donahue, J., Darrell, T., &amp; Malik, J. (2014). Rich feature hierarchies for accurate object detection and semantic segmentation. 2014 IEEE Conference on Computer Vision and Pattern Recognition, 580–587. <a href="https://doi.org/10.1109/cvpr.2014.81">https://doi.org/10.1109/cvpr.2014.81</a> </p>
<p>Zou, Z., Chen, K., Shi, Z., Guo, Y., &amp; Ye, J. (2023). Object detection in 20 years: A survey. Proceedings of the IEEE, 111(3), 257–276. <a href="https://doi.org/10.1109/jproc.2023.3238524">https://doi.org/10.1109/jproc.2023.3238524</a> </p>
<p>Uijlings, J., et al. (2014). Selective search for object recognition. Stanford University.
<a href="http://vision.stanford.edu/teaching/cs231b_spring1415/slides/ssearch_schuyler.pdf">http://vision.stanford.edu/teaching/cs231b_spring1415/slides/ssearch_schuyler.pdf</a></p>
<p>Li, F.-F., Jonhson, J., &amp; Yeung, S. (2017). Lecture 11: Detection and segmentation. <a href="https://cs231n.stanford.edu/slides/2017/cs231n_2017_lecture11.pdf">https://cs231n.stanford.edu/slides/2017/cs231n_2017_lecture11.pdf</a> </p>
<p>Chhikara, P. (2022, February 1). Intuition and implementation of Non-Max suppression algorithm in object detection. Medium.
<a href="https://towardsdatascience.com/intuition-and-implementation-of-non-max-suppression-algorithm-in-object-detection-d68ba938b630">https://towardsdatascience.com/intuition-and-implementation-of-non-max-suppression-algorithm-in-object-detection-d68ba938b630</a> </p>
<p>Weng, L. (2017, December 31). Object detection for dummies part 3: R-CNN family.
<a href="https://lilianweng.github.io/posts/2017-12-31-object-recognition-part-3/">https://lilianweng.github.io/posts/2017-12-31-object-recognition-part-3/</a> </p>
<p>Iwasa, Y., et al. (2021). Automatic segmentation of pancreatic tumors using deep learning on a video image of contrast-enhanced endoscopic ultrasound. Journal of Clinical Medicine, 10(16), 3589.
<a href="https://doi.org/10.3390/jcm10163589">https://doi.org/10.3390/jcm10163589</a> </p>