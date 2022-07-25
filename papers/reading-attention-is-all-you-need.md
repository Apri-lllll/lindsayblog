# Reading *Attention is All You Need*



*Attention is All You Need* proposes the Transformer model, which solely uses attention mechanisms to tackle sequence transduction problems. The Transformer achieves state-of-the-art results on translation tasks with low training costs.



### Background

Approaching sequence modeling requires models to capture dependencies within sequences. Previous methods mainly utilize two classes of model architecture: recurrent networks and convolutional networks. However, the constraint of sequential computation brought forth by recurrent networks precludes parallelization. Convolutional networks, while successfully reducing sequential computation, struggle to capture long-term dependencies.  Attention mechanisms can model dependencies without regard to distance, but have mostly been used with recurrent networks, so the constraint on sequential computation remains. The Transformer solves both of these problems - it allows for parallelization and learns dependencies between different distances with a constant number of operations.



### Attention Mechanism

![image-20220725172002148](images\image-20220725172002148.png)

An attention mechanism inputs a query(Q), and a set of key(K) - value(V) pairs to compute a weighted sum of the values. The weight assigned to each value is computed using the query and the key. Different methods for computing weights exist; the Transformer uses the scaled dot-product attention, which is computationally efficient. The equation for scaled dot-product attention is:
$$
Attention(Q,K,V)=softmax(\frac{QK^T}{\sqrt{d_k}})V
$$
The original dot product is scaled by a factor of $\frac{1}{\sqrt{d_k}}$ to prevent the $softmax$ function from having small gradients when the dot product grows large.

The Transformer uses multi-head attention, which allows the model to learn from different representation subspaces. Multi-head attention is implemented by computing $h$ different linear projections of $Q,K,V$, performing attention on each of set of projections, concatenating the results of attention, then performing a final linear projection. $h$ is a hyperparameter denoting the number of heads.



### Model Architecture

![image-20220725114107461](images\image-20220725114107461.png)

The Transformer uses the popular encoder-decoder network structure, and the encoder and decoder each consist of numerous stacked layers.

Each encoder layer includes two sublayers:

- Multi-head self-attention layer: Queries, keys, and values all come from the previous encoder layer.
- Fully-connected feed-forward network: The network consists of two linear mappings with a ReLU activation in between.

Each decoder layer includes three sublayers:

- Masked multi-head self-attention layer: Queries, keys, and values all come from the previous decoder layer. To preserve the auto-regressive property of the decoder, all the positions after the current token are masked out (i.e. their weights are set to $-\infin$).
- Multi-head attention layer: Queries come from the previous decoder layer; keys and values come from the output of the encoder.
- Fully-connected feed-forward network: The network structure is the same as in the encoder.

For each of the sublayers, a residual connection is applied around the sublayer, and layer normalization is applied following it.

An important part of the Transformer is positional encoding, which allows the model to be aware of the relative positions of different tokens. Positional encodings are added to input and output embeddings before they are input into the encoder or decoder. They are calculated by:
$$
PE_{pos,2i}= sin(pos/10000^{2i/d_{model}})\\
PE_{pos,2i+1}= cos(pos/10000^{2i/d_{model}})
$$
where $pos$ denotes the position and $i$ denotes the dimension. The advantages of using this method of calculation are twofold: first, the model can learn to attend to relative positions ($PE_{pos+k}$ can be represented as a linear function of $PE_{pos}$); second, the model can extrapolate to  sentences longer than those in the training data.

The model generates the output one element at a time. The output of the final decoder layer is passed through a linear projection followed by the softmax function, producing a series of probabilities for the next element in the output sequence, each corresponding to a token.



### Training & Experimental Results

The following points on training the model were specified in the paper:

- Adam optimizer
- Varied learning rate: Apply learning rate warmup for a fixed number of steps, then decay it proportionally to the inverse square root of the step number.
- Regularization: Use dropout ($p$=0.1) and label smoothing ($\epsilon=0.1$).

The Transformer was tested on two translation datasets: WMT English-to-German and WMT English-to-French. On both of these tasks, it establishes a new state-of-the-art BLEU score, while using a fraction of the training cost of competitive models.

Experiments on model variations validate the importance of the following factors on model performance:

- an appropriate number of heads
- large model size and attention key size ($d_k$)
- the use of dropout

Finally, experiments were also implemented on the task of English constituency parsing. The Transformer, despite the lack of task-specific tuning, produces results that are highly competitive with state-of-the-art models.



### Discussion

The Transformer is a highly effective model utilizing only the attention mechanism. The following advantages of the self-attention are emphasized in the paper: 

- Positions are connected with a constant number of sequential operations.
- Self-attention layers are faster than recurrent layers when the representation dimensionality exceeds the sequence length.
- The weights calculated in attention provide crucial information for improving the interpretability of models.

The authors present the following directions for future work:

- Applying the Transformer to modalities other than text.
- Using local, restricted attention to manage large inputs and outputs.
- Making generation less sequential.



### References

Ashish Vaswani, Noam Shazeer, Niki Parmar, Jakob Uszkoreit, Llion Jones, Aidan N Gomez, Lukasz Kaiser, and Illia Polosukhin. 2017. Attention is all you need. In Advances in Neural Information Processing Systems, pages 6000–6010.