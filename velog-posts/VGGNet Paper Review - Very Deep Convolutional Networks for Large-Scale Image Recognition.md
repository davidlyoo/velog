<h1 id="summary">Summary</h1>
<p>VGGNet is a deep convolutional neural network designed to improve image classification performance by <strong>increasing network depth while using small convolutional layers (3 x 3 conv)</strong>. This architecture enables efficient feature extraction and great performance on large-scale image recognition tasks, particularly in the ImageNet challenge.
<img alt="" src="https://velog.velcdn.com/images/davidlyoo/post/1fe8c973-e4ab-4a77-8225-d809d2d407d4/image.png" /></p>
<hr />
<h1 id="introduction">Introduction</h1>
<p>VGGNet was developed to explore the impact of <strong>network depth</strong> in convolutional neural networks. Unlike earlier architectures that used larger convolutional filters (e.g., 7 x 7 or 11 x 11), VGGNet leverages small (3 x 3) convolution filters stacked in all layers.
This design provides several advantages:</p>
<ul>
<li><strong>Better feature extraction</strong> through multiple stacked small filters  </li>
<li><strong>Fewer parameters</strong> compared to architectures using large filters  </li>
<li><strong>Increased non-linearity</strong>, which helps the model capture more complex patterns</li>
</ul>
<p>By increasing depth from 8 layers to 16 and 19 layers, VGGNet greatly improves classification accuracy while remaining computationally feasible.</p>
<hr />
<h1 id="vggnet-architecture">VggNet Architecture</h1>
<h3 id="model-structure">Model Structure:</h3>
<p>VGGNet follows a structured and uniform architecture that prioritizes depth, small filters, and efficient parameter utilization. Leveraging increased non-linearity through stacked 3x3 convolutional layers enhances feature extraction and allows the network to learn more complex patterns.
<img alt="" src="https://velog.velcdn.com/images/davidlyoo/post/8fb10251-04be-4145-92dd-ed68d01e6e83/image.png" /></p>
<p><strong>Stacked Small Filters Instead of Large Filters</strong>
Rather than using a single large filter (e.g., 7 x 7), VGGNet stacks multiple 3 x 3 convolutional layers. A stack of three 3 x 3 convolutional  layers (stride = 1) has the same effective receptive field as a single 7 x 7 convolutional layer, but with several advantages:</p>
<ul>
<li>Using small filters enables deeper networks while maintaining computational feasibility.<ul>
<li>Introduces more non-linearity, allowing the model to learn more complex features</li>
<li>Maintains the same receptive field while reducing the number of parameters</li>
</ul>
</li>
</ul>
<p><strong>1. Increased Non-Linearity for Stronger Feature Extraction</strong></p>
<ul>
<li>Each convolutional layer is followed by a ReLU activation, ensuring that the network learns non-linear patterns.</li>
<li>More non-linearity introduces additional complexity to the decision boundary, making feature representations more discriminative.</li>
<li>Without non-linearity, even a deep network would behave as a single linear transformation, limiting its capacity to learn complex features.</li>
<li>Stacked non-linear activations allow the network to capture hierarchical patterns, improving classification performance.</li>
</ul>
<p><strong>2. Fewer Parameters with Efficient Receptive Fields</strong> 
One of the key advantages of using stacked 3×3 filters is the reduction in the number of parameters while maintaining an effective receptive field.</p>
<h4 id="parameter-comparison"><strong>Parameter Comparison</strong></h4>
<p>Assuming both the input and output of a three-layer 3×3 convolution stack have C channels:</p>
<ul>
<li><p>Three stacked 3×3 convolutions:<br />[ 3 × (3² C²) = 27C²  ]  </p>
</li>
<li><p>A single 7×7 convolution:
[ 7² C² = 49C²  ]  </p>
</li>
<li><p>Conclusion:<br />[ 27C² (stacked 3×3) vs. 49C² (single 7×7)  ]<br />→ <strong>Stacked small filters are more efficient.</strong></p>
</li>
</ul>
<p><strong>By leveraging small filters, VGGNet significantly reduces the number of parameters while maintaining strong feature extraction capabilities</strong>.</p>
<hr />
<h1 id="training--evaluation"><strong>Training &amp; Evaluation</strong></h1>
<p>VGGNet was trained on the ImageNet dataset, which consists of <strong>1.3 million training images and 50,000 validation images across 1,000 classes</strong>. To improve model generalization and feature extraction, several key techniques were used during training and testing.  </p>
<p><img alt="" src="https://velog.velcdn.com/images/davidlyoo/post/293a4b91-286c-4c5d-8c3f-796902b4ea86/image.png" />  </p>
<h3 id="1-data-augmentation-for-better-generalization"><strong>1. Data Augmentation for Better Generalization</strong></h3>
<p>To prevent overfitting and enhance model robustness, VGGNet applied data augmentation techniques such as:  </p>
<ul>
<li><strong>Random cropping</strong>: Extracting 224×224 patches from resized images to simulate variations in object size and position.  </li>
<li><strong>Horizontal flipping</strong>: Randomly flipping images to improve feature invariance.  </li>
<li><strong>Color jittering</strong>: Adjusting brightness, contrast, and saturation to simulate different lighting conditions.  </li>
</ul>
<h3 id="2-multi-scale-training"><strong>2. Multi-Scale Training</strong></h3>
<p>VGGNet was trained using multi-scale input images, meaning images were resized to different scales before cropping.  </p>
<ul>
<li>This allowed the model to learn scale-invariant features, improving classification performance on images of varying sizes.  </li>
<li>Unlike earlier models that used fixed input sizes, VGGNet processes images at multiple scales to improve robustness and generalization.  </li>
</ul>
<h3 id="3-testing--performance-evaluation"><strong>3. Testing &amp; Performance Evaluation</strong></h3>
<p>During evaluation, the model followed two key testing strategies:  </p>
<ul>
<li><strong>Single-crop testing</strong>: A single 224×224 center crop from each test image was used for inference.  </li>
<li><strong>Multi-crop testing</strong>: Multiple overlapping crops were extracted from an image, and the predictions were averaged to improve accuracy.  </li>
</ul>
<p>Multi-crop testing yielded better results by reducing variance in predictions, but it also increased computational cost.  </p>
<hr />
<h1 id="performance">Performance</h1>
<p>VGGNet achieved remarkable performance in the ImageNet Large Scale Visual Recognition Challenge (ILSVRC-2014), finishing 2nd place in the classification task, just behind GoogLeNet.  </p>
<ul>
<li>The <strong>VGG-16</strong> and <strong>VGG-19</strong> models demonstrated <strong>high classification accuracy</strong> while maintaining a relatively straightforward architecture.  </li>
<li>VGGNet’s deep, uniform architecture proved to be highly influential in the development of later CNN models.  </li>
<li>The model showed significant improvements in top-5 error rate, reducing misclassification rates compared to earlier architectures like AlexNet.  </li>
</ul>
<h3 id="key-results-from-ilsvrc-2014"><strong>Key Results from ILSVRC-2014</strong></h3>
<table>
<thead>
<tr>
<th>Model</th>
<th>Top-5 Error (%)</th>
</tr>
</thead>
<tbody><tr>
<td>AlexNet (2012)</td>
<td><strong>16.4%</strong></td>
</tr>
<tr>
<td>ZFNet (2013)</td>
<td><strong>11.7%</strong></td>
</tr>
<tr>
<td><strong>VGGNet (2014, 2nd place)</strong></td>
<td><strong>7.3%</strong></td>
</tr>
<tr>
<td>GoogLeNet (2014, 1st place)</td>
<td><strong>6.7%</strong></td>
</tr>
</tbody></table>
<p>Despite being computationally more expensive than GoogLeNet, <strong>VGGNet’s simpler and more systematic design influenced many future architectures, including ResNet and DenseNet</strong>.   </p>
<hr />
<h1 id="conclusion">Conclusion</h1>
<p>VGGNet demonstrated that <strong>increasing network depth while using small convolutional filters</strong> is an effective strategy for improving image classification performance. By replacing large filters with stacked 3×3 convolutions, the model achieves <strong>stronger feature extraction and better computational efficiency</strong>.</p>
<p>However, VGGNet also has limitations:</p>
<ul>
<li>High computational cost due to the large number of layers.</li>
<li>No residual connections, which later architectures (e.g., ResNet) introduced to improve gradient flow and training stability.</li>
</ul>
<p>Despite its limitations, <strong>VGGNet remains one of the most influential CNN architectures</strong> in deep learning history. Its structured and uniform design made it easier to understand and implement compared to more complex models like GoogLeNet.  </p>
<p>In my view, <strong>VGGNet's simplicity and systematic approach to depth expansion</strong> were key factors in its success. While later models like ResNet introduced more sophisticated mechanisms such as skip connections to handle deeper networks, <strong>VGGNet still serves as a fundamental reference model for deep CNN architectures</strong>.  </p>
<hr />
<h1 id="references">References</h1>
<p>Simonyan, K., &amp; Zisserman, A. (2015). Very deep convolutional networks for large-scale image recognition. <em>arXiv preprint arXiv:1409.1556</em>. <a href="https://doi.org/10.48550/arXiv.1409.1556">https://doi.org/10.48550/arXiv.1409.1556</a></p>
<p>Li, F.-F., Jonhson, J., &amp; Yeung, S. (2017). Lecture 6: CNN Architectures.
<a href="https://cs231n.stanford.edu/slides/2024/lecture_6_part_1.pdf">https://cs231n.stanford.edu/slides/2024/lecture_6_part_1.pdf</a></p>