# Paper Writing
- [Paper Writing](#paper-writing)
  - [Introduction](#introduction)
  - [Related Work](#related-work)
  - [Ablation Study](#ablation-study)
  - [好词好句](#好词好句)
## Introduction
1. **总分结构**。做introduction(或者related work)的时候，可以先列举文献，再挑重点分类别简述\
  - A considerable number of recent works such as **[47, 36, 62, 23, 70, 37, 24, 20, 16, 59]** also emerge to address personalization tasks from different angles. By far, it remains an open question of what is the most convenient way to achieve such personalization. One straightforward approach is to fine-tune a T2I model with exemplar images. Personalized tuning techniques **[47, 36, 23, 67]** such as DreamBooth [47] have shown promising quality by finetuning model weights. Their downsides are yet obvious: finetuning a model remains to be resource-costly for average users, despite the growing effort of more efficient tuning [23]. Prompt-engineering **[60, 20]** serves as a lite alternative for personalizing T2I models. 
  - Recent studies have made various attempts for deepfake detection and achieve remarkable success **[26, 41, 3, 2, 32, 39, 14, 24, 30, 29, 19, 47, 23, 36, 5]**. Several arts discuss low-level differences between pristines and forgeries and suggest using them as classification clues. **Li et al. [24]** assume blending artifacts exist in all pristines and suggest finding the blending boundary beside the detection task; **Qian et al. [39] and Luo et al. [30]** use highfrequency details as additional inputs for their models; **Liu et al. [29]** adopt phase spectrums to capture the up-sampling artifacts of face forgery for the task.

## Related Work
- 介绍完各个方法，一段的最后需要引入自己的方法或者说明以上方法的不足之处（however）；不要仅仅单调的介绍related work
  
······***Inspired by TDAN, our PCD module*** adopts deformable convolution as a basic operation for alignment.

······***Motivated by the success of
these works, we*** employ both temporal and spatial attention in our TSA fusion module to allow different emphases on different temporal and spatial locations.

- **paper的任何部分都是为自己的work服务，Related Work亦是如此**

## Ablation Study
1. 
 We seamlessly integrate three auxiliary self-supervised tasks to boost the generalizability of our model. To evaluate the effectiveness of these tasks, we conduct ablation studies by assigning zero weight to the loss corresponds to each of these tasks (i.e. LR, LT , and LA correspond to the forgery region estimation mask, blending type estimation task, and the blending ratio estimation task, respectively). All models are trained on the four methods of FF++ and evaluated on benchmark datasets using the same settings. Results are shown in Table 7. We can observe that each auxiliary task **plays a vital role in our framework**, **where our method performs the best when** all three self-supervised tasks are incorporated. **On the other hand**, **excluding any of these tasks will decrease the overall performance**, **and** using only the main classification loss lead to significantly worse performance than others. **This validates the effectiveness of the proposed self-supervised tasks**.(Paper: Self-supervised Learning of Adversarial Example: Towards Good Generalizations for Deepfake Detection)

 ## 好词好句

1. on par with  与...相当（as good as）
   - generating high-performing customized outputs **on par with** the current state-of-the-art
2. pros and cons 优缺点
   - Table 1 explains the **pros and cons** of varieties of approaches, in which we gauge from three angles: personalization quality, easy installation & domain adaptation, and input complexity & flexibility.
3. off-the-shelf 现成的
   - a generic and efficient pretraining strategy that bootstraps vision-language pre-training from **off-the-shelf** frozen pre-trained image encoders and frozen large language models.
4. circumvent [to avoid something, especially cleverly or illegally（尤指巧妙或非法地）逃避，规避，绕过]
   - We identify several key factors that **circumvent** this issue.