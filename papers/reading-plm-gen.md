# Reading *A Survey of Pretrained Language Models Based Text Generation*

> To organize what I've learned about pretrained language models (PLMs), I plan to write a 3 posts on the following topics:
>
> - Reading *A Survey of Pretrained Language Models Based Text Generation*: Text generation using PLMs is a large part of my research right now. This survey will allow me to identify topics I can further explore and hopefully help me get new ideas for my research~
> - Reading *Pretrained Models: Past, Present and Future*: This paper is a comprehensive survey of all types of pretrained models. I hope to gain a more comprehensive understanding of pretrained models through reading it.
> - Pretrained Language Models: This post will be continually updated as I read & learn about different individual models.



### Introduction

Text generation, or natural language generation, refers to learning a mapping function from input data to output text. For this task, neural network models have now replaced traditional statistical methods, reducing data engineering requirements and alleviating data sparsity. However, neural network models require large amounts of labeled data for effective training. Pretrained language models (PLMs), which are trained in an unsupervised method and can be utilized for different downstream tasks, are a promising solution to this problem.

This survey covers how PLMs can be applied for text generation. Three main points are identified: obtaining semantic-preserving encodings of input data, designing architectures for generation, and optimizing the generation function.



### Preliminaries

The following table summarizes different input formats and their corresponding tasks:

| Input Formats                           | Tasks                                           |
| --------------------------------------- | ----------------------------------------------- |
| not provided; random noise              | language modeling (unconditional generation)    |
| discrete properties                     | topic-to-text; attribute-based generation       |
| structured data (knowledge base; table) | data-to-text                                    |
| multi-media (image; speech)             | image captioning; speech recognition            |
| text                                    | machine translation; dialog; text summarization |

Current PLMs are mostly developed based on the transformer structure. They can take the structure of encoder-decoder (T5), encoder-only (BERT), or decoder-only (GPT).

The following diagram is an overview of the structure of the survey.

![0123-1](images\0123-1.png)



### Encoding Input Representations

**Unstructured Input**

For paragraph and document inputs, the main challenge is to effectively capture important semantics from complex textual structures. Hierarchical networks can be applied to obtain both word-level and sentence-level representations. Using graph neural networks for information aggregation helps models capture deeper meaning. Topic models and topic-based contrastive learning have been proposed to improve the model's ability to identify salient information.

For multi-language inputs, such as in machine translation, obtaining valid representations for both / all the languages is vital for good performance in downstream tasks. XLM learns representations for two languages in the same embedding space. Multi-lingual PLMs aim to provide effective embeddings for any pair of languages, and often rely on contrastive learning to learn differences between languages.

**Structured Input**

Structured inputs include knowledge graphs, tables, etc. To bridge the semantic gap between inputs and text, researchers either linearize the input into sequences or directly transform them into embeddings that can serve as the input to a PLM.

Another key point of utilizing structured input is incorporating the structural information into models. Mainstream approaches include adding the objective of reconstructing the input from learned representations, adjusting the output according to the input structure, using extra tokens to denote input structure, and designing additional modules to encode input structure.

Finally, text fidelity means the degree to which the output text adheres to the content of the input. Fidelity can be improved by additional training objectives, copying input words to the output, and adding target information to the input.

**Multi-Media Input**

Multi-media input mainly includes image, video, and speech. For image captioning tasks, XLGPT and visionGPT are pretrained models which use GPT as the textual modal. It has also been proposed that including pretraining tasks that guide the model to learn relationships between the visual and text modalities is beneficial. Pioneer models in video captioning, such as VideoBERT, use captioning as the pretraining task and build a separate video-to-text decoder. UniVL further improved model performance by sharing the encoder and decoder modules. Pretraining for speech recognition has seen improvements in the directions of weakly-supervised learning and post-processing for downstream tasks.



### Designing PLMs for Text Generation

**Standard Architecture**

Standard PLMs mostly use the transformer architecture as the backbone. Single-stack transformer architectures can be divided into masked LMs, causal LMs, and prefix LMs. Double-stack architectures feature explicit encoder-decoder structures.

(1) Masked LMs: Models such as BERT are pretrained using MLM tasks, where each masked token is predicted from all other input tokens. This use of the full attention mask is similar to transformer encoders. Though masked LMs cannot be directly used for text generation, they can serve as the encoder part of the generation model.

(2) Causal LMs: Examples include GPT and CTRL. These models condition each tokens only on previous tokens, making them straightforward for auto-regressive text generation. A drawback of this architecture is its neglect of bidirectional information.

(3) Prefix LMs: Prefix LMs combine characteristics of the first two structures by using mixed attention masks. Each source word can attend to all other sources words, while tokens in the target text can attend to previous target tokens and all source tokens. Examples include UniLM and GLM.

(4) Encoder-Decoder: These models use the entire encoder-decoder structure. BART is pretrained to denoise corrupted text, while T5 is pretrained on the span corruption objective.

**Auxiliary Modules**

Aside from basic token embeddings, almost all pretrained models use positional embeddings, since the attention transformation is position-invariant. Most models, such as BERT and GPT, utilize learned absolute positional embeddings, while models such as T5 use relative embeddings. Other auxiliary embeddings can be designed for specific input formats, eg. user embeddings in dialog generation, language embeddings in multi-lingual models.

Other lines of work have focused on developing improved attention modules. Sparse attention increases the efficiency of the model when dealing with long input sequences. For multiple inputs, it is common to separately encode them, then aggregate embeddings through pooling or attention mechanisms.