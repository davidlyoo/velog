<h1 id="summary">Summary</h1>
<p>The ResNet paper introduces the concept of residual learning to address the challenges associated with training very deep neural networks. Traditional deep networks face optimization difficulties, particularly due to vanishing and exploding gradients. ResNet overcomes these issues by reformulating layers to learn residual functions instead of directly mapping inputs to outputs. <strong>This approach allows the training of extremely deep networks (up to 152 layers) while maintaining efficient training and improved performance.</strong> Compared to architectures like VGGNet, ResNet achieves higher accuracy with fewer parameters and lower computational cost.</p>
<hr />
<h1 id="introduction">Introduction</h1>
<h3 id="background-the-challenge-of-deep-neural-networks">Background: The Challenge of Deep Neural Networks</h3>
<ul>
<li>Deeper networks naturally integrate low-, mid-, and high-level features in an end-to-end multi-layered structure.</li>
<li>While increasing depth theoretically improves feature representation, it introduces significant optimization challenges.</li>
<li>A key question posed by the paper: <strong>&quot;Is learning better networks as easy as stacking more layers?&quot;</strong></li>
<li>The answer is <strong>no</strong>, as deeper networks suffer from:<ul>
<li>Vanishing/exploding gradients that hinder convergence from the beginning.</li>
<li>Difficulty in optimization despite techniques like normalized initialization and batch normalization.</li>
</ul>
</li>
</ul>
<h3 id="solution-residual-learning-framework">Solution: Residual Learning Framework</h3>
<ul>
<li>Instead of directly mapping inputs to outputs, layers are reformulated to learn a residual function:<ul>
<li><strong>Residual function:</strong> ( F(x) = H(x) - x )</li>
<li><strong>Original function transformation:</strong> ( H(x) = F(x) + x )</li>
</ul>
</li>
<li>This reformulation allows easier learning by enabling identity mappings when necessary.</li>
<li>To the extreme, an identity mapping is optimal when residuals are zero.</li>
</ul>
<h3 id="shortcut-connections">Shortcut Connections</h3>
<ul>
<li><strong>Key component of ResNet’s architecture</strong>, designed to ease optimization:<ul>
<li>Skipping one or more layers while maintaining information flow.</li>
<li>Adding outputs of residual functions to the outputs of stacked layers.</li>
<li>No additional parameters or computational overhead.</li>
<li>Enables effective end-to-end training of deep networks.</li>
</ul>
</li>
<li>Despite its depth, ResNet achieves lower complexity than VGGNet.</li>
</ul>
<hr />
<h1 id="resnet">ResNet</h1>
<h3 id="model-architecture">Model Architecture</h3>
<p><img alt="" src="https://velog.velcdn.com/images/davidlyoo/post/fb1f65cf-89ed-44c0-a38c-a1682f9bc7ff/image.png" /></p>
<ul>
<li>The core idea behind ResNet is residual learning, formulated as:
[  F(x) = H(x) - x ]<ul>
<li>Instead of directly learning ( H(x) ), the model learns ( F(x) ), making training easier.</li>
<li>The final function is represented as:
[ H(x) = F(x) + x    ]</li>
</ul>
</li>
<li>ReLU activations and biases are omitted in some notations for simplification.</li>
</ul>
<h3 id="bottleneck-architecture">Bottleneck Architecture</h3>
<p>For deeper versions of ResNet (e.g., ResNet-50/101/152), the authors propose a bottleneck design to reduce computation while preserving performance.</p>
<p>Each bottleneck residual block consists of:</p>
<ul>
<li><p>1×1 convolution (reduce dimensionality)</p>
</li>
<li><p>3×3 convolution</p>
</li>
<li><p>1×1 convolution (restore dimensionality)</p>
</li>
</ul>
<p>This allows deeper models to remain computationally efficient and parameter-efficient, enabling scalable deep networks.</p>
<hr />
<h1 id="performance">Performance</h1>
<ul>
<li><p><strong>Solving the degradation problem:</strong></p>
<ul>
<li>Deep networks without residual connections suffer from performance degradation, where deeper networks perform worse than shallower ones.</li>
<li>ResNet enables training of deep networks (e.g., 152 layers) while maintaining strong performance.</li>
</ul>
</li>
<li><p><strong>Improved optimization:</strong></p>
<ul>
<li>Shortcut connections help mitigate vanishing gradients.</li>
<li>Gradients flow more effectively through the network.</li>
</ul>
</li>
<li><p><strong>Computational efficiency:</strong></p>
<ul>
<li>Despite its depth, ResNet has lower complexity than architectures like VGGNet.</li>
</ul>
</li>
</ul>
<hr />
<h1 id="conclusion">Conclusion</h1>
<ul>
<li>ResNet introduces a new paradigm for deep learning by using residual learning.</li>
<li>Overcomes optimization challenges that arise in very deep networks.</li>
<li>Enables training of ultra-deep networks without degradation in performance.</li>
<li>Residual connections introduced by <strong>ResNet have since become foundational in modern architectures</strong>, including ResNeXt, DenseNet, EfficientNet, and even Transformer-based models like BERT and Vision Transformers (ViT). Their ability to stabilize deep network training makes them a core design principle in today's deep learning research and applications.</li>
</ul>
<hr />
<h1 id="references">References</h1>
<p>He K, et al. (2015). <em>Deep Residual Learning for Image Recognition</em>. arXiv:1512.03385</p>