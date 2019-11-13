# Attention Is All You Need (2017)

Ashish Vaswani, Noam Shazeer, Niki Parmar, Jakob Uszkoreit, Llion Jones, Aidan N. Gomez, Lukasz Kaiser, Illia Polosukhin

---

This paper introduces the transformer model. This model has recurrence or convolutions, it is made up of fully connected layers and attention mechanisms.

Recurrent models have the downside of not being fully parallelizable as computation is sequential. 

Attention mechanisms have taken off, basic concept is to get a representation of your input by taking a normalized, weighted sum of all the input states. 

Researchers started to use convolutional neural networks as these can be parallelized, however the number of operations needed to relate between an input and output position grows with the distance between them, making it hard to learn long term dependencies. 

Self-attention is an application of attention mechanisms relating items at different positions in a single sequence to compute a representation of that sequence.

Sequence-to-sequence models (called sequence transduction models here) use an encoder-decoder structure. The encoder transformers the input sequence of symbols, X, into a sequence of continuous representations, Z. The decoder takes Z and generates an output sequence, Y, one element at a time.

Figure 1 shows the transformer model.

The encoder has N = 6 layers, each identical. Each layer has 2 sub-layers. The first is a multi-head self-attention mechanism and the second is a feed-forward network. Each sub-layer has a residual connection from the input to the output, followed by layer normalization. All sub-layers produce the outputs in the same dimension, d_model = 512.

The decoder also has N = 6 layers, each identical. As well as the same 2 sub-layers in the encoder, it has a third sub-layer between them. This sub-layer performs multi-headed self-attention over the output of the encoder - this also has the residual connection and layer normalization. The first self-attention layer is masked so the decoder cannot attend to subsequent positions within the output, effectively cheating by looking at the next item in the sequence and directly copying it, instead of learning how to translate a sentence.

Attention can be thought of as mapping a *query* and a set of *key-value* pairs to an output, where each of the query, key and value are vectors. The output is a weighted sum of the values, where the weight assigned to each is a function of the query and the key. They use the simple dot product attention that is scaled by root d_k, the size of the key dimension, i.e. softmax(QK^T/sqrt(d_k))V. The other option was to use additive attention, but this is slower. The reason for scaling is for large values of d_k the dot products are large, making the softmax have an extremely small gradient, the scaling reduces this.

Self-attention is good because it reduces computational complexity, increases parallelization and has a constant path length between long-range dependencies.

Instead of applying the attention as normal, they use multi-head attention. They split the d_q, d_k and d_v dimensions into h = 8 *heads*. Thus, 512/8 = 64. Now the attention is not just once between 512 dimensions, but 8 times (in parallel) between 64 dimensions. This allows the model to jointly attent to information from different representation subspaces. 

For the self-attention layer in the decoder that uses both the encoder and decoder representations, the query comes from the decoder and the key and values come from the encoder. For the other self-attention layers, all three come from the output of the previous layer in the encoder/decoder.

The feed-forward network takes the d_model input, passes it through a linear layer to expand it to d_ff = 2048, passes it through a ReLU and then scales it back down to d_model.

They also tie the weights of the embeddings and the output linear layer, as is standard nowadays.

The model has no concept of the position of words within a sentence, instead of learning a positional embedding (common) they use positional encoding. This is done by each embedding dimension having a sine and cosine wave at different frequencies added to them. They use this over positional embedding as they believe it may allow the model to extrapolate to sequence lengths longer than those seen during training.

They train on WMT14 German (4.5M examples) using byte-pair encoding with a shared vocabuary of 37000 tokens. For WMT14 French (36M examples) with a 32000 token vocabulary. 

They use Adam with a learning rate schedule that scales linearly at the beginning and then has a slow exponential decay afterward. They use dropout everywhere, with a value of 0.1 for German and 0.3 for French. They use label smoothing, thus the correct target token has a probability of less than 1 - this apparently hurts perplexity but improves accuracy and BLEU. 

Translation is done by averaging the last 5 checkpoints (for small, 20 for big) and using a beam search.

Results are state-of-the-art for the two datasets while using considerably less compute.