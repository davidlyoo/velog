<h1 id="summary">Summary</h1>
<p><strong>Fast R-CNN</strong> revolutionizes object detection by addressing the key limitations of R-CNN. It introduces <strong>RoI Pooling</strong>, enabling more efficient computation and better utilization of shared feature maps, but it still primarily relies on CPUs, limiting its speed.</p>
<hr />
<h1 id="introduction">Introduction</h1>
<h3 id="limitations-of-r-cnn">Limitations of R-CNN</h3>
<p>R-CNN brought significant advancements to object detection but still suffered from several key challenges.
<strong>1. Slow Training</strong>: Training R-CNN on large datasets like the PASCAL VOC dataset takes about 84 hours and consumes considerable disk space.
<strong>2. Slow Inference</strong>: Detection takes 47 seconds per image with VGG16.
<strong>3. Spatial Distortion</strong>: Warping region proposals to a fixed size (227x227) introduces spatial loss.
<strong>4. Inefficient Region Proposal Processing</strong>: Each of the 2000 region proposals is processed independently through the CNN, leading to redundant computations and slow performance.</p>
<p>Fast R-CNN addresses these issues by rethinking how region proposals and features are processed, making detection faster and more efficient. R-CNN's inefficiencies paved the way for Fast R-CNN, which optimizes object detection with a unified and efficient pipeline, significantly improving speed and accuracy.</p>
<p>Let's dig in.</p>
<hr />
<h1 id="object-detection-with-fast-r-cnn">Object Detection with Fast R-CNN</h1>
<p>Model Overview:
<img alt="" src="https://velog.velcdn.com/images/davidlyoo/post/c94ec0de-1756-4a91-90ac-d496aa485f19/image.png" /></p>
<h2 id="fast-r-cnn-workflow">Fast R-CNN Workflow</h2>
<ul>
<li><ol>
<li>The input image is passed through a <strong>CNN backbone</strong> to generate a <strong>feature map</strong>.</li>
</ol>
</li>
<li><ol start="2">
<li><strong>Region proposals</strong> are extracted using <strong>Selective Search</strong> to identify potential object regions.</li>
</ol>
</li>
<li><ol start="3">
<li>The region proposals are <strong>projected onto the shared feature map</strong> to align them with the convolutional features.</li>
</ol>
</li>
<li><ol start="4">
<li><strong>RoI Pooling</strong> is applied to convert each region proposal into a fixed-sized feature map.</li>
</ol>
</li>
<li><ol start="5">
<li>Fully Connected Layers (FCLs) perform <strong>classification</strong> and <strong>bounding-box regression</strong>, optimized with a <strong>multi-task loss</strong>.</li>
</ol>
</li>
</ul>
<h2 id="detailed-architecture-of-fast-r-cnn">Detailed Architecture of Fast R-CNN</h2>
<h3 id="spatial-pyramid-pooling-spp">Spatial Pyramid Pooling (SPP)</h3>
<p><img alt="" src="https://velog.velcdn.com/images/davidlyoo/post/58f93675-b03c-43f6-870d-6c1b153f348e/image.png" /></p>
<ul>
<li><p>Fast R-CNN builds on the idea of <strong>Spatial Pyramid Pooling (SPP)</strong> from SPPnet. While SPP divides the feature map into multiple levels to create fixed-size feature vectors, capturing multi-scale spatial information.</p>
</li>
<li><p>Fast R-CNN simplifies this by using <strong>RoI Pooling</strong>, which directly converts each RoI into a fixed-size 7x7 feature map. This approach retains the benefits of SPP while significantly reducing computational complexity.</p>
</li>
</ul>
<p><img alt="" src="https://velog.velcdn.com/images/davidlyoo/post/ed8e5d03-2a2e-4e5f-9acd-1930bbae0a46/image.png" /></p>
<ul>
<li>Instead of resizing input images to a fixed size, Fast R-CNN processes the entire image through a CNN to generate a shared convolutional feature map, which is then used by RoI Pooling to handle region proposals of varying sizes.</li>
</ul>
<h3 id="the-roi-pooling-layer">The RoI pooling layer</h3>
<p><strong>RoI Pooling</strong> is a key component in Fast R-CNN that converts region proposals into fixed-size feature maps. Dividing the feature map of a RoI into a fixed grid and applying <strong>max pooling</strong> to each cell standardizes the output size, enabling consistent processing in FCLs.</p>
<p><img alt="" src="https://velog.velcdn.com/images/davidlyoo/post/6cff025f-3b17-4114-89ca-474fc6596038/image.png" /> <strong>Step 1)  Feature Map Extraction via CNN Backbone</strong></p>
<ul>
<li>The input image is passed through a CNN backbone (e.g., VGG16) to extract a feature map.</li>
<li>The feature map has a reduced spatial resolution compared to the original image, determined by the downsampling ratio of the CNN.<ul>
<li>For example: An 800 x 800 input image           generates a feature map of size 8 x 8 with a      downsampling ratio of 1/100.</li>
<li>Each pixel on the feature map corresponds to a 100 x 100 region in the original image.</li>
</ul>
</li>
</ul>
<p><strong>Step 2) Region Proposal Generation</strong></p>
<ul>
<li><strong>Selective Search</strong> Algorithm identifies the Region of Interest (RoI) in the input image.</li>
<li>Each RoI is represented by:<ul>
<li>A four-tuple (r, c, h, w) where:<ul>
<li>r, c: Top-left corner coordinates</li>
<li>h, w: Height and width of the region</li>
</ul>
</li>
</ul>
</li>
<li>These proposals mark the areas most likely to contain objects for further analysis.</li>
</ul>
<p><strong>Step 3) RoI Projection</strong></p>
<ul>
<li>The coordinates of each RoI, defined in the original image space, are projected onto the feature map.  This involves scaling the RoI dimensions using the downsampling ratio of the CNN.<ul>
<li>For Example: An RoI with dimensions (700, 500) in the original image maps to a 7 x 5 region on the 8 x 8 feature map.</li>
</ul>
</li>
</ul>
<p><strong>Step 4) Grid Division</strong></p>
<ul>
<li>The projected RoI on the feature map is divided into a fixed grid of size H x W.</li>
<li>Each grid cell corresponds to a sub-region of the RoI. The size of each grid cell is approximately:<ul>
<li>Grid cell size = h/H x w/W</li>
</ul>
</li>
</ul>
<p><strong>Step 5) Max Pooling</strong></p>
<ul>
<li>Max pooling is applied independently to each grid cell, and the maximum value is selected to represent that cell.</li>
<li>This step generates a fixed-size feature map for each RoI, preserving key spatial features while standardizing the dimensions.</li>
</ul>
<p>*<em>Output: Fixed-size Feature Map
*</em>- The fixed-size feature maps produced by RoI Pooling are flattened into feature vectors and passed through FCLs for:
    - Classification: Softmax over K + 1 categories
    - Bounding-Box Regression: Category-specific bounding-box regressor</p>
<h3 id="hierarchical-sampling">Hierarchical sampling</h3>
<ul>
<li><p>SPPnet's Limitations:</p>
<ul>
<li>SPPnet struggles with backpropagation due to      the large receptive fields of RoIs, often         spanning the entire image.</li>
<li>Each RoI requires processing the entire         receptive field of the original image, making       backpropagation computationally expensive.</li>
</ul>
</li>
<li><p>Fast R-CNN's Solution:</p>
<ul>
<li>Hierarchical mini-batch sampling <ul>
<li>First, N images are sampled, followed by R/N RoIs        per image.</li>
<li>Example: With N = 2 and R = 128, this           method is 64x faster than R-CNN's               method of sampling one RoI from 128               different images.</li>
</ul>
</li>
</ul>
</li>
<li><p>Streamlined Training Process:
Fast R-CNN optimizes both classification and bounding-box regression in a single fine-tuning stage, replacing R-CNN's stage-wise approach:</p>
<ul>
<li>R-CNN: Trains a softmax classifier, SVMs, and       regressor separately.</li>
<li>Fast R-CNN: Jointly optimizes a softmax         classifier and bounding-box regressors.</li>
</ul>
</li>
</ul>
<h3 id="multi-task-loss">Multi-task Loss</h3>
<p>A Fast R-CNN network has two sibling output layers, designed to jointly handle <strong>classification</strong> and <strong>bounding-box regression.</strong> This <strong>multi-task loss</strong> framework ensures that both tasks are optimized simultaneously, providing efficient and accurate object detection.</p>
<ol>
<li><strong>Classification Loss</strong>: </li>
</ol>
<ul>
<li>Outputs a <strong>discrete probability distribution</strong> per RoI over K + 1 categories (including the background class).</li>
<li>Defined as:
<img alt="" src="https://velog.velcdn.com/images/davidlyoo/post/94ce8df7-a8b0-4a0d-9dfb-538bea56f978/image.png" /></li>
</ul>
<ol start="2">
<li><strong>Bounding-box Regression Loss</strong>:
Refines bounding-box predictions for object localization.
Applies only to non-background classes (𝑢≥1).
Defined as:
<img alt="" src="https://velog.velcdn.com/images/davidlyoo/post/968743e5-b806-4942-bb56-42355f836488/image.png" /></li>
</ol>
<p>t: Predicted bounding-box offsets
v: Ground-truth bounding-box targets</p>
<p><strong>Combined Loss Function</strong>
The overall <strong>multi-task loss</strong> is defined as:
<img alt="" src="https://velog.velcdn.com/images/davidlyoo/post/5dbdcc29-70c0-4a66-a9ef-76c1cb4ab2a0/image.png" />λ: Balancing factor for the two loss terms (commonly λ = 1)
[𝑢≥1]: Ensures that bounding-box regression is only applied to non-background RoIs. (u = 0, if it's background)</p>
<h3 id="truncated-svd-for-faster-detection">Truncated SVD for faster detection</h3>
<p>For detection, the processing of a large number of RoIs significantly increases computational demands, with nearly half of the forward pass time spent on the fully connected layers.
To address this bottleneck, Fast R-CNN leverages <strong>Truncated Singular Value Decomposition (SVD)</strong> to compress these large fully connected layers, accelerating computations and reducing processing time effectively.</p>
<p><strong>How Truncated SVD Works</strong></p>
<ul>
<li><strong>1. Matrix Factorization</strong>:
The weight matrix W (u x v) in the FCL is factorized into three smaller matrices:
<img alt="" src="https://velog.velcdn.com/images/davidlyoo/post/6f9bada7-6184-4178-b16f-c29ab68ac18f/image.png" /></li>
</ul>
<p>U: A 𝑢×𝑡 matrix containing the first 𝑡 left singular vectors.
Σₜ: A t×t diagonal matrix with the top t singular values.
V: A v×t matrix containing the first t right singular vectors.</p>
<ul>
<li><p><strong>2. Compression</strong>:</p>
<ul>
<li><p>The original parameter count uv is reduced to t(u+v), where t≪min(u,v).</p>
</li>
<li><p>This compression significantly decreases the number of computations required in the fully connected layers, resulting in faster forward passes.</p>
</li>
</ul>
</li>
</ul>
<p><a href="https://velog.velcdn.com/images/davidlyoo/post/36133f7c-8003-4b33-882d-255987d2fb55/image.png"></a></p>
<p><img alt="" src="https://velog.velcdn.com/images/davidlyoo/post/63101001-acda-43b0-8137-d4ea538da7bf/image.png" /></p>
<p><strong>3. Layer Substitution</strong>:
The fully connected layer using W is replaced by two smaller layers:
First Layer: Uses Σₜ Vᵀ as weights.
Second Layer: Uses U as weights, along with the original biases.</p>
<p><img alt="" src="https://velog.velcdn.com/images/davidlyoo/post/80bfb495-6b26-4a5c-b622-a887bef5d0c5/image.png" /></p>
<p>Truncated SVD can reduce detection time by more than 30%, achieving significant computational efficiency with minimal impact on detection accuracy.</p>
<hr />
<h1 id="performance">Performance</h1>
<p>Fast R-CNN exhibits remarkable improvements in both accuracy and speed over R-CNN:</p>
<ul>
<li><p><strong>Mean Average Precision (mAP)</strong>:  </p>
<ul>
<li>Achieved <strong>68.4%</strong> on the PASCAL VOC 2012 dataset, showcasing robust detection accuracy.</li>
</ul>
</li>
<li><p><strong>Speed Enhancement</strong>:  </p>
<ul>
<li><strong>146× Faster</strong> than R-CNN without Truncated SVD.  </li>
<li><strong>213× Faster</strong> with Truncated SVD, resulting in an additional <strong>30% reduction</strong> in detection time.</li>
</ul>
</li>
<li><p><strong>Revolutionary Improvements</strong>:  </p>
<ul>
<li>Introduced <strong>RoI Pooling</strong>, enabling efficient processing of region proposals by reusing shared convolutional feature maps.  </li>
<li>Balanced speed and accuracy for large-scale object detection tasks.
<img alt="Fast R-CNN Performance" src="https://velog.velcdn.com/images/davidlyoo/post/6ca1c58a-525d-4bdb-b219-02861b74634f/image.png" />  </li>
</ul>
</li>
</ul>
<p>Fast R-CNN revolutionized object detection by addressing R-CNN's inefficiencies, significantly enhancing both training and inference speeds while maintaining strong detection accuracy.</p>
<hr />
<h1 id="conclusion">Conclusion</h1>
<p>Fast R-CNN marks a pivotal advancement in object detection by addressing the key limitations of its predecessor, R-CNN, while introducing innovative features like <strong>RoI Pooling</strong> and a one-stage training structure. Here's how Fast R-CNN overcomes the challenges faced by R-CNN:</p>
<hr />
<h3 id="r-cnns-limitations-and-fast-r-cnns-solutions"><strong>R-CNN's Limitations and Fast R-CNN's Solutions</strong></h3>
<table>
<thead>
<tr>
<th><strong>R-CNN Limitation</strong></th>
<th><strong>Fast R-CNN Solution</strong></th>
</tr>
</thead>
<tbody><tr>
<td><strong>Slow Training</strong>: Training on large datasets takes over 84 hours and consumes significant disk space.</td>
<td>Jointly optimizes classification and bounding-box regression in a single stage, drastically reducing training time.</td>
</tr>
<tr>
<td><strong>Slow Inference</strong>: Detection takes 47 seconds per image with VGG16.</td>
<td>Shared computation of feature maps and RoI Pooling significantly reduce inference time (up to 213x faster).</td>
</tr>
<tr>
<td><strong>Spatial Distortion</strong>: Warping region proposals to fixed size (227x227) causes spatial loss.</td>
<td>RoI Pooling extracts fixed-size features without distorting the input regions, preserving spatial information.</td>
</tr>
<tr>
<td><strong>Inefficient Region Proposal Processing</strong>: Each of the 2,000 proposals is processed independently through the CNN, leading to redundancy.</td>
<td>Processes the entire image once through the CNN backbone to generate shared feature maps, avoiding redundant computations.</td>
</tr>
</tbody></table>
<hr />
<h3 id="fast-r-cnns-limitations"><strong>Fast R-CNN's Limitations</strong></h3>
<p>While Fast R-CNN resolves many of R-CNN's inefficiencies, it introduces its own challenges:</p>
<ol>
<li><p><strong>Region Proposal Bottleneck</strong>:  </p>
<ul>
<li>Fast R-CNN still relies on external algorithms like <strong>Selective Search</strong> for generating region proposals, which are computationally expensive and predominantly CPU-based. This bottleneck limits its potential for real-time applications.</li>
</ul>
</li>
<li><p><strong>GPU Utilization</strong>:  </p>
<ul>
<li>Unlike later models such as Faster R-CNN, which integrates region proposal generation within the network, Fast R-CNN cannot fully leverage the computational power of GPUs for an end-to-end pipeline.</li>
</ul>
</li>
</ol>
<hr />
<h3 id="reflection"><strong>Reflection</strong></h3>
<p>The most fascinating aspect of Fast R-CNN lies in how a seemingly simple change—<strong>introducing RoI Pooling</strong> to produce fixed-size feature maps at the right stage—leads to such a profound impact on performance. This adjustment, though small in concept, revolutionized object detection by enabling faster and more efficient computation, showcasing how minor innovations in architectural design can yield transformative results. It’s a reminder of the elegance and power of optimizing processes at the right place.</p>
<hr />
<h1 id="references">References</h1>
<p>Girshick, R. (2015). Fast R-CNN. 2015 IEEE International Conference on Computer Vision (ICCV).
<a href="https://doi.org/10.1109/iccv.2015.169">https://doi.org/10.1109/iccv.2015.169</a> </p>
<p>He, K., Zhang, X., Ren, S., &amp; Sun, J. (2015). Spatial pyramid pooling in deep convolutional networks for visual recognition. IEEE Transactions on Pattern Analysis and Machine Intelligence, 37(9), 1904–1916.
<a href="https://doi.org/10.1109/tpami.2015.2389824">https://doi.org/10.1109/tpami.2015.2389824</a> </p>
<p>Singular value decomposition (SVD). (2019). Data-Driven Science and Engineering, 3–46. <a href="https://doi.org/10.1017/9781108380690.002">https://doi.org/10.1017/9781108380690.002</a> </p>
<p>Li, F.-F., Jonhson, J., &amp; Yeung, S. (2017). Lecture 11: Detection and segmentation. <a href="https://cs231n.stanford.edu/slides/2017/cs231n_2017_lecture11.pdf">https://cs231n.stanford.edu/slides/2017/cs231n_2017_lecture11.pdf</a></p>
<p>Grel, T. (2017, March 15). Region of interest pooling explained. deepsense.ai. Retrieved from <a href="https://deepsense.ai/region-of-interest-pooling-explained">https://deepsense.ai/region-of-interest-pooling-explained</a></p>