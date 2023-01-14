# Reading Continuous Prompt Learning Papers



### Introduction

Until the last couple of years, fine-tuning has always been the default method when adopting pretraining language models for downstream tasks. Recently, though, research focus and interest has shifted to prompt-based learning, a developing and promising approach to utilizing PLMs. In prompt-based learning "templates" are filled with the task input to form a prompt which is the input of the PLM. The model then completes the missing information in the prompt, and the output determines the final result. Advantages of prompt-based learning include low parameter costs, remarkable few-shot performance, and high generalization ability.

Initially, discrete templates consisting of words (often interpretable descriptions of the objective task) were used. The main drawback of this method is that, since not all word embeddings correspond to actual words, such discrete templates are non-differentiable and can not be optimized using gradient-descent-based methods. Directly searching different word combinations if infeasible as well due to the large search space. Researchers have designed methods such as paraphrasing, scraping data corpora, and reducing the search space to facilitate automatic prompt generation, but results still fall behind fine-tuning in many scenarios.

Recent progress in using continuous templates made possible further boosts in the performance of prompt-based learning, leading to performance levels that reach or even exceed fine-tuning. When using continuous templates, template parameters are directly defined in the word embedding space, and it is not required that templates correspond to words. Now that parameters are continuous, gradient-descent-based methods are the natural choice for parameter optimization.

The following parts provide a discussion of four prominent works regarding continuous prompt learning: prefix tuning, prompt tuning, WARP (Word-level Adversarial ReProgramming), and P-Tuning. The term prompt tuning is sometimes used to generally refer to prompt-based learning. In this post, I use "prompt learning" for this meaning and reserve the term "prompt tuning" specifically for the method proposed in *The Power of Scale for Parameter-Efficient Prompt Tuning*. I first give an overview of the key components of each method, then devote a section to each.



### Overview of Methods

| Method        | PLM                         | update PLM? | prompt design                                                | other designs                                                | tasks                                                |
| ------------- | --------------------------- | ----------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ---------------------------------------------------- |
| prefix tuning | GPT-2                       | N           | prepend prompt to representations at each layer; reparameterization |                                                              | table-to-text; summarization                         |
| prompt tuning | T5                          | N           | prepend prompt to original input only; no reparameterization | further train PLM on standard LM objective                   | natural language understanding (GLUE, superGLUE)     |
| WARP          | roberta, albert             | N           | trainable prompt tokens can be inserted at arbitrary positions in the input | a trainable verbalizer maps output to a class; restricted to mask filling model | sentence / sentence pair classification (GLUE, etc.) |
| P-Tuning      | bert, roberta, deberta, glm | Y           | prepend prompt to representations at each layer; optional reparameterization | initialize with multi-task learning; uses classification head instead of LM head | classification; sequence labeling                    |



### Prefix Tuning

In prefix tuning, a series of continuous prompt tokens are prepended to the hidden representation in each layer of the PLM. To stabilize training, the authors reparameterize the trainable prefix parameters as a smaller matrix, followed by a large feedforward neural network. The auto-regressive GPT2 model is used to experiment on language generation tasks (table-to-text and summarization). Experiments show that, until a certain threshold, model performance improves as prefix length increases. The authors further explore the effect of prefix initialization and found that for low-data settings, initializing with embeddings of real words lead to stabler and better performance. The relative effectiveness of prompt learning compared with fine-tuning was highlighted in low-data and domain transfer scenarios.

The main contribution of prefix-tuning is demonstrating that prompt-based methods can achieve comparable performance to fine-tuning while requiring significantly fewer parameters. Design considerations were thoroughly examined and tested, providing valuable guidance for stabilizing model performance, especially in low-resource settings.



### Prompt Tuning

The prompt design of prompt tuning differs from that of prefix tuning in two main aspects. First, parameters are prepended only to the original input; model hidden states remain unchanged. Second, parameters are directly optimized without reparameterization. The PLM used in this piece of work is T5, and tasks focus on natural language understanding (GLUE, superGLUE). One challenge addressed in the paper is that T5 is pretrained on a span corruption objective, meaning that it hasn't seen or generated complete natural language during pre-training. The authors propose several methods to further tune model parameters and bridge this gap between pretraining and downstream tasks. They found that training T5 on the standard LM objective for up to 100K steps improved model performance. Experiments also confirmed the effect of initialization and prompt length.

Finally, the paper's main contribution is showing that prompt learning gets more competitive as model size increases. It was shown that larger models are more stable and less reliant on model or experiment designs (prompt length, initialization, pretraining method, LM adaptation method, etc.) The proposed method is also simpler and more parameter-efficient. One inevitable concern is that, large models, while having promising performance, are computationally costly and not always feasible in real-life applications.



### WARP

Different from the previous two methods, WARP focuses on mask-filling models (roberta, albert) and tasks (sentence classification).  For the prompt template, WARP introduces a number of trainable parameters that can be inserted at arbitrary positions in the input sequence. On the other hand, WARP further designs verbalizer parameters, which are used to map the mask-filling results to pre-defined label classes. Experiments show that the above approach reaches sota results on datasets such as GLUE.

WARP is a novel approach to the specific models and tasks the authors focus on. The paper proposes flexible template designs and explores verbalizer implementation, which is less covered in the literature. A drawback of WARP is that it is unclear whether similar strategies generalize well beyond masked language models.



### P-Tuning

Similar to prefix tuning, P-Tuning incorporates parameters into each layer in the PLM. Reparameterization is optional. Aside from prompt design, P-Tuning also proposes to improve model performance in the following two ways: using multi-task learning to initialize the model, and replacing the LM head with a randomly-initialized classification head. As for downstream tasks considered, the paper distinguished between simple classification tasks (such as tasks considered in WARP) and relatively difficult sequence labeling tasks.

Compared with prefix tuning and prompt tuning, P-Tuning stresses universality across both tasks and scales and demonstrates that the same prompt learning method can be effective under diverse experimental settings. The cost of this performance is that, while the aforementioned methods all freeze PLM parameters during training, P-Tuning jointly updates both the prompt and the PLM itself, leading to more trainable parameters and higher training costs.



### References

[1] P. Liu, W. Yuan, J. Fu, Z. Jiang, H. Hayashi, and G. Neubig, “Pre-train, Prompt, and Predict: A Systematic Survey of Prompting Methods in Natural Language Processing.” arXiv, Jul. 28, 2021. Accessed: Dec. 21, 2022. [Online]. Available: http://arxiv.org/abs/2107.13586

[2] X. L. Li and P. Liang, “Prefix-Tuning: Optimizing Continuous Prompts for Generation.” arXiv, Jan. 01, 2021. Accessed: Nov. 01, 2022. [Online]. Available: http://arxiv.org/abs/2101.00190

[3] B. Lester, R. Al-Rfou, and N. Constant, “The Power of Scale for Parameter-Efficient Prompt Tuning.” arXiv, Apr. 17, 2021. Accessed: Oct. 14, 2022. [Online]. Available: http://arxiv.org/abs/2104.08691

[4] K. Hambardzumyan, H. Khachatrian, and J. May, “WARP: Word-level Adversarial ReProgramming.” arXiv, Jun. 02, 2021. Accessed: Nov. 06, 2022. [Online]. Available: http://arxiv.org/abs/2101.00121

[5] X. Liu *et al.*, “P-Tuning: Prompt Tuning Can Be Comparable to Fine-tuning Across Scales and Tasks,” in *Proceedings of the 60th Annual Meeting of the Association for Computational Linguistics (Volume 2: Short Papers)*, Dublin, Ireland, May 2022, pp. 61–68. doi: [10.18653/v1/2022.acl-short.8](https://doi.org/10.18653/v1/2022.acl-short.8).