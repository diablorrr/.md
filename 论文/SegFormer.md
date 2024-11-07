> 2024.11.6

> 标题：SegFormer: Simple and Efficient Design for Semantic Segmentation with Transformers



# 先前工作的问题

1. ViT：用的是固定分辨率的positional embedding，但语义分割测试的时候往往图片分辨率是不固定的
2. ViT：输出的是单尺度低分辨率特征，而不是多尺度特征



# 模型

<img src="SegFormer.assets/image-20241106173420461.png" alt="image-20241106173420461" style="zoom:67%;" /> 

- Overlap Patch Embeddings：打成的patch是有重叠的

> 保证局部连续性

- Mix-FFN：3x3卷积放入前馈网络中

> 用Mix-FFN替代Positional Embedding，因为作者认为3x3的卷积就足以动态表达patch间的位置关系（猜测是因为每次卷积核移动的时候，是有重叠的）

Mix-FFN就是局部FFN和全局FFN的混合，因为感受野的不同，分别处理局部和全局的信息

- Overlap Patch Merging：重叠块合并

- MLP Layer：结合了局部注意力和全局注意力信息

用一个MLP层将多尺度特征在通道维度进行统一，之后将特征上采样到1/4。

在通道维度进行concat，再通过MLP层融合特征，最后一个线性层预测

> 较低层次的注意力倾向于保持局部性，较高层次的注意力倾向于保持非局部性



为什么设计 轻量级全MLP解码器？

分层transformer编码器的有效感受野比传统CNN编码器大