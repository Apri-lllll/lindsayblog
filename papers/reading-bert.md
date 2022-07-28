# Reading *BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding*



This paper introduces BERT (bi-directional encoder representations from transformers), a language representation model characterized by the bi-directionality of representations learned during pretraining. After being pretrained on large unlabeled datasets and fine-tuned with minimal task-specific parameters, BERT achieve state-of-the-art performance on 11 natural language processing (NLP) datasets.



### Background

In NLP, pretraining refers to performing large-scale training on an auxiliary task before further adjusting model architecture and parameters to suit the downstream task. Pretrained language models have demonstrated state-of-the-art performance on tasks of different scales, such as token-level and sentence-level. They can be applied to downstream tasks in two main ways: feature-based and fine-tuning. Feature-based methods, such as ELMo, introduce task-specific architectures which are learned with respect to the downstream task. On the other hand, fine-tuning methods, such as GPT, require minimal additional parameters and simply fine-tune all parameters in the pretrained model.

The main limitation of previous pretrained models, which are standard conditional language models, is that their learned representations are unidirectional. This significantly hinders model performance, especially on tasks such as question-answering that require the model to capture bidirectional context.



### Model Architecture

BERT is first pretrained over unlabeled data, then fine-tuned on labeled data for the specific downstream task. The main model architecture is a multi-level bidirectional transformer. Such a model structure is highly unified for different downstream tasks, and only minimal parameters related to specific ways of input and output need to be fine-tuned.

The input representation scheme of BERT allows both single sentences and pairs of sentences (required in tasks such as question-answering) to be clearly represented. Each input sequence starts with the [CLS] token, and two sentences in the same input sequence are separated by the [SEP] token. For each token, its representation is the sum of the following three embeddings:

- token embedding: WordPiece embeddings with a 30,000-word vocabulary
- segment embedding: represents whether the token is in the first or second sentence
- position embedding: represents the token's position in the sentence

![image-20220728114030](/images/20220728114030.png)



### Pretraining & Fine-Tuning Procedure

BERT is pretrained on two unsupervised tasks: masked language modeling (MLM) and next-sentence prediction (NSP).

MLM refers to randomly masking a proportion on input tokens, and training the model to predict the masked tokens. Compared with standard conditional LM, this allows for bidirectional conditioning. BERT uses the following masking scheme: 15% of tokens are randomly chosen, of which 80% are replaced with the [MASK] token, 10% are replaced by a random token, and 10% remain unchanged. The model learns to predict the chosen tokens with cross-entropy loss.

NSP allows the model to capture sentence-level relationships. In this task, the model is given two sentences, A and B, and is required to output whether B is the next sentence of A. In 50% of the training examples, B does follow A, and in the other 50%, B does not.

BERT is fine-tuned by plugging in the appropriate inputs and outputs for the downstream task, then training the whole network end-to-end. Few parameters need to be trained from scratch, and fine-tuning is relatively inexpensive.



### Experimental Results

Experiments demonstrate that BERT outperforms existing methods on 11 NLP tasks, including GLUE and SQuAD. For each task, the input and output methods are slightly modified. The following image shows several examples.

![image-20220728113137](/images/20220728113137.png)

Ablation studies are performed to verify the necessity of different parts of the proposed method. Removing the NSP pretraining task and replacing MLM with standard left-to-right LM both significantly hurt model performance. The authors also found that increasing model size led to accuracy improvements even on small datasets.

Additionally, the researchers experimented with using BERT for feature-based methods. By taking activations of the model without fine-tuning and passing them through a BiLSTM before the classification layer, they achieved results competitive with state-of-the-art feature-based methods. Concatenating the activations of the last four layers in the pretrained transformer produced the best results.



### Discussion

Compared with GPT, which uses unidirectional standard LM, and ELMo, which concatenates independently trained LTR and RTL LSTMs, BERT representations are jointly conditioned on left and right context on all layers. The accurate performance of BERT can be attributed to its bidirectional nature and the choice of its pretraining tasks, both of which distinguish BERT from previous work.

![image-20220728114518](/images/20220728114518.png)

The effective performance of BERT demonstrates the importance of bi-directionality in pretraining, as well as the possibility of using a unified model for a wide range of downstream tasks.