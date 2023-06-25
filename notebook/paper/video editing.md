
# Video Editing

**FateZero: Fusing Attentions for Zero-shot Text-based Video Editing** 

- Paper: https://arxiv.org/abs/2303.09535
- Code: https://github.com/ChenyangQiQi/FateZero
  
## Abstract
基于扩散模型的生成模型在文生图领域已经取得了很好的效果，但是由于扩散模型在生成过程机油很强的随机性，因此在视觉内容编辑方面运用该模型仍然存在挑战，尤其在视频领域。

- 本文提出基于文本的zero-shot视频编辑方法FateZero
- 为了编辑视频的连续性：
  1. 像较于直接的DDIM inversion技术，FateZero在inversion阶段捕获中间层的attention maps，这有效的保持了structure和motion信息；
  2. 通过引入时空注意力机制，对去噪Unet的自注意力机制进行的变化
- 视频风格和局部属性的编辑；形状编辑
  
## Introduction
不同于图像编辑，视频编辑需要保持编辑后的视频的时间一致性，而这却是训练好的text-to-image model所没有学习到的。



## Conclution
- Limitation: 难以产生完全新的动作（e.g.,‘swim’−→‘fly’）以及差异较大的物体(e.g.,‘swan’
−→‘pterosaur’)

