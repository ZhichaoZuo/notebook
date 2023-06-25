**(ControlNet) Adding Conditional Control to Text-to-Image Diffusion Models** 

- Paper: https://arxiv.org/abs/2302.05543
- Code: https://github.com/lllyasviel/ControlNet

## Abstract
 present a neural network structure, ControlNet, to **control pretrained large diffusion models to support additional input conditions**.
 
- **task-specific**, end-to-end way; **learning is robust** even when the training dataset is small (< 50k).
- training a ControlNet is as fast as fine-tuning a diffusion model, can be trained on a personal devices

## Introduction
文生图模型只需要用户输入一段简短的描述性prompt，Does this prompt-based control satisfy our needs? Can these large models be applied to facilitate these specific tasks? In specific tasks, can large models **preserve the advantages and capabilities obtained from billions of images**?
- First, the available data scale in a task-specific domain is not always as large as that in the general image-text domain.因此需要设计robust网络训练方法来避免overfit并保持generalization ability
- 针对特定任务，在可接受的时间和内存空间内优化大型模型非常重要。这就需要利用大模型训练用到的权重以及微调策略或者迁移学习
- 要在许多任务中实现学习解决方案，端到端学习是必不可少的。

**Idea：**

- The ControlNet clones the weights of a large diffusion model into a **"trainable copy"** and a **"locked copy"**: the locked copy preserves the network capability learned from billions of images, while the trainable copy is trained on task-specific datasets to learn the conditional control.
- The trainable and locked neural network blocks are connected with an unique type of convolution layer called **"zero convolution"**, where the convolution weights progressively grow from zeros to optimized parameters in a learned manner. 
- Since the production-ready weights are preserved, the training is robust at datasets of different scale. 
- Since the zero convolution does not add new noise to deep features, the training is as fast as fine tuning a diffusion model, compared to training new layers from scratch.

## Related Work
### HyperNetwork and Neural Network Structure
### Diffusion Probabilistic Model
In order to **reduce the computation power required for training a
diffusion model**, based on the idea of latent image，the approach Latent Diffusion Model (LDM) was proposed and further extended to Stable Diffusion.
### Image-to-Image Translation
ControlNet和Image-to-Image Translation的motivation不同，Image-to-image translation在于在不同domain的图片之间学习一个映射（mapping），而ControlNet致力于用task-specific conditions来control diffusion model。（本质上都是Image to image吧）

## Method
The neural network blocks are connected by an unique type of convolution layer called “zero convolution”, i.e., 1×1 convolution layer with both weight and bias initialized with zeros. 
在Figure2里面，c就是额外增加的控制条件，其中**zero convolution** 是一个初始化为0的1×1的卷积层。这样的结构使得第一步训练c(控制条件)没有参与，相当于x经过block直接到y,就是一个简单的使用预训练的diffusion model。后面经过反向传播**zero convolution**不再是全0，控制条件c开始参与训练。（具体证明见论文公式）

<div align = 'center'>
<img src=../img/ControlNet-fig2.jpg width=80% />
</div>
以Stable Diffusion为例，介绍ControlNet

Stable Diffusion uses a pre-processing method similar to VQ-GAN to convert the entire dataset of 512 × 512 images into smaller 64 × 64 “latent images” for stabilized training. 因此ControlNet也需要将image-based conditions转化为64 × 64的feature space to match the convolution size
<div align = 'center'>
<img src=../img/ControlNet-fig3.png width=80% />
</div>

### Training 训练策略

During the training, we ***randomly replace 50% text prompts with empty strings***. This **facilitates ControlNet’s capability to recognize semantic contents from input condition maps**, e.g., Canny edge maps or human scribbles, etc. This is mainly because **when the prompt is not visible for the SD model, the encoder tends to learn more semantics from input control maps as a replacement for the prompt**.
这种训练策略是值得借鉴的！