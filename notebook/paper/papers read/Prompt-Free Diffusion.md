**Prompt-Free Diffusion -Taking “Text” out of Text-to-Image Diffusion Models** 

- Paper: https://arxiv.org/abs/2305.04441
- Code: https://github.com/SHI-Labs/Prompt-Free-Diffusion

Motivation:
- T2I中text prompt无法精确控制生成的效果，taking “Text” out of a pretrained T2I diffusion model, 

Contribution:
- propose SeeCoder(encoder), **substituting the commonly used CLIP-based or LLM-based text encoder.**
- SeeCoder可以很好的和其他T2Imodel结合
- 可以很好的应用到下游任务上，例如动画人物生成、虚拟衣物试穿等




## Abstract
T2I, **one pain point persists: the text prompt engineering,** and searching high-quality text prompts for customized results is more art than science.
**“an image is worth a thousand words”**(只用Text无法充分涵盖精确地视觉信息，控制生成效果不佳)
the attempt to describe a desired image with texts often ends up being ambiguous and cannot comprehensively cover delicate visual details, hence necessitating more additional controls from the visual domain.

因此文章做出了一个尝试，将Text从T2I diffusion model中拿掉，减轻prompt engineering的工作

**input**：a reference image as “context”, an optional image structural conditioning, and an initial noise, **with absolutely no text prompt**.
core architecture： SeeCoder(encoder), substituting the commonly used CLIP-based or LLM-based text encoder.

## Introduction
- T2I引发了许多新的任务。
  - The high demand for personalized synthetic results has promoted several text-to-image (T2I) related technologies, including **model finetuning**, **prompt engineering**, and **controllable editing**, to a new level of importance.
- ControlNet仍然存在诸多挑战。
  - For example, it still faces the following issues: a) the current approach **meets challenges in generating user-designated textures, objects, and semantics**; b) searching prompt-in-need sometimes is problematic and inconvenient; and c) **prompt engineering** for quality purposes is still required.
- 总结。在vision和language间存在gap(**解决**：拿掉language，即Text prompt，用reference image替代)
  - In conclusion, all these issues come from the **fundamental knowledge gap between vision and language**. Captions alone may not provide a comprehensive representation of all visual cues, and providing structural guidance does not eliminate this problem.

- 基本思想就是用vision encoder 替换 language encoder
<div align = 'center'>
<img src = ../img/Prompt-Free%20Diffusion-fig2.png width=50% />
</div>

- SeeCoder 的结构
<div align = 'center'>
<img src = ../img/Prompt-Free%20Diffusion-fig3.png width=80% />
</div>