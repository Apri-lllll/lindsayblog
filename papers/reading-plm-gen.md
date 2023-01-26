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



### Optimizing PLMs for Text Generation

**Fine-Tuning for Text Generation**

Fine-tuning, which consists of using task-specific data and loss function to further adjust model parameters, is a common method of adapting PLMs for downstream tasks.

However, one drawback of vanilla fine-tuning is the tendency to overfit on small datasets. To mitigate this problem, domain adaptive and task adaptive intermediate fine-tuning has been proposed. DAIFT refers to fine-tuning with data from the required domain, but under a different task. This allows the model to pick up on domain-specific information. On the contrary, in TAIFT, models are tuned on the required task using data from different domains, and learn task-specific knowledge in the process. Both these methods allow a larger set of supervised data to be used.

In addition, multi-task fine-tuning incorporates cross-task knowledge into the PLM. When auxiliary tasks are same as the original task, as in pure multi-task fine-tuning, MTFT alleviates data sparsity. In hybrid MTFT, different tasks are used, which sometimes enhances model performance on the primary task.

Parameter-efficient fine-tuning attempts to downsize the number of learned parameters in the fine-tuning stage. Researchers have introduced adapters, which use feed-forward layers to project PLM layers to low-dimensional vectors, then back to the original size. Fine-tuning only adapter parameters reduces the total number of learned parameters. Other lines of work propose tuning a portion of PLM parameters (eg. cross-attention modules) or distilling the large PLM to several smaller models.

**Prompt Tuning for Text Generation**

Prompt tuning attempts to bridge the gap between pretraining and fine-tuning by reformulating the downstream task using a prompt template. The PLM fills in slots in the template, based on which the final result can be derived. Prompts can be either in cloze or prefix form.

Discrete prompts directly take the form of natural language, and were originally manually designed to describe the desired task (such as in GPT2). Works such as AutoPrompt attempt to overcome the need for manual design. Other approaches towards this goal include paraphrasing, generating using PLMs, and scraping corpuses.

Continuous prompts, on the other hand, are continuous vectors in the embedding space. They do not need to correspond to natural language words, and can have their own learnable parameters. The continuous nature of these prompts means that they can be easily optimized through gradient-descent-based methods. Prefix tuning is one of the pioneering works in this area, and demonstrates the effectiveness of prepending trainable prefixes to original inputs.

**Property Tuning for Text Generation**

(1) Relevance: Relevance refers to whether topical semantics in the output text are highly related to the input, and is especially important in scenarios such as dialog generation. The cross-attention module of most PLMs allows them to link the input and the output. For example, DialoGPT is pretrained on large-scale dialogue sessions, directing the model to generate responses that are relevant to the dialogue history.

(2) Faithfulness: We aim for the output of PLMs to adhere to the information in the input text. This is facilitated by the knowledge and natural language understanding abilities of PLMs. Faithfulness can be enhanced by retrieving salient parts of the source or using auxiliary losses to gauge semantic closeness.

(3) Order-Preservation: In settings such as translation, keeping the order of semantic units consistent is an important concern. Researchers most often use alignment methods, either aligning individual words or mapping all words in the source and target to the same space and aligning the continuous representations.



### Challenges & Solutions

**Data**

Lack of training data often limits the performance of PLMs on downstream tasks. Solutions include transfer learning (from domains with ample data), data augmentation (through simulated data or permutating original data), and multi-task learning.

Another challenge is preventing models from learning and aggregating biases in training data. It has been found that biases are often captured in word embeddings and attention head. Such biases can be mitigated through swapping gendered terms or masking out names and pronouns during training.

The problem of domain transfer has been addressed in the previous section (DAIFT & TAIFT).

**Model**

The large size of most state-of-the-art PLMs hinders their widespread use in real-life scenarios, so the following approaches towards model compression have been developed. Quantization refers to reducing the number of unique values used to represent weights, and important weights are often left untouched to preserve model performance. Pruning refers to removing less important weights or simplifying entire modules to reduce model size. The final approach is model distillation, where a small PLM can directly learn from the vocabulary distribution produced by a large model.

Model extensions can potentially improve the performance of PLMs for specific tasks. Knowledge-enriched PLMs utilize external knowledge sources to improve the factual information retained by the model. ERNIE, for instance, uses a large-scale knowledge graph. Efficient PLMs, such as CALM, aim at achieving competitive results with less pretraining data and costs.

Finally, to improve robustness to noise, researchers have proposed combining character and word embeddings, as well as using data augmentation to help PLMs generalize to semantic neighborhoods of generation instances.

**Optimization**

Improving the coherence, factual accuracy, and controllability of generated text has been a key point of recent research. For coherence, text planning (planning the knowledge graphs of sentences and documents) and discourse relation modeling have demonstrated promising results. Pointer generator methods (which copy text from input to output), additional evaluation metrics, and correction methods trigger PLMs to better preserve factual information. To increase controllability, works such as PPLM allow the model to condition on extra constraints without requiring further training.

The goal of improving training stability has also been approached through numerous optimization methods. Intermediate fine-tuning is an effective solution that has been discussed in previous sections. The mixout strategy randomly exchanges parameters of two PLMs to constrain the deviation between them. Contrastive learning adds a supervised contrastive term to the loss function, alleviating the unstableness entailed by cross-entropy loss.



### Evaluation & Resources

The following two tables summarize metrics and datasets commonly used in text generation.

**Metrics**

| Category            | Metrics                     |
| ------------------- | --------------------------- |
| N-gram Overlap      | BLEU, ROUGE, METEOR, ChrF++ |
| Diversity           | Distinct                    |
| Semantic Similarity | BERTScore                   |

**Datasets**

![0126-1](images\0126-1.png)