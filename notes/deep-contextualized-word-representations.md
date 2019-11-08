# Deep Contextualized Word Representations (2019)

Matthew E. Peters, Mark Neumann, Mohit Iyyer, Matt Gardner, Christopher Clark, Kenton Lee, Luke Zettlemoyer

---

This paper introduces ELMo (Embeddings from Language Models)

Basic concept: instead of just using an embedding layer, use a pre-trained biLSTM too.

These embeddings are "contextualized" as the embeddings they output for a word depend on the context that the word exists in. For example the word "bow" in the two sentences, "I took a bow" and "He was wielding a bow and arrow" both have different meanings which can only be obtained by their context. Normal embedding layers (non-contextualized) will have the word "bow" in each of the sentences produce the same embedding vector, whereas contextualized word embeddings will have the word "bow" in the example above produce a different vector.

They are "deep" contextualized word embeddings because they use a deep neural network, specifically an LSTM. However they do not just use the final layer of their multi-layer LSTM. They state that the high layers capture more context-dependent aspects, whereas the lower layers capture more syntactic aspects. 

They use two layer bidirectional LSTMs as well as a character CNN and a linear layer over the "internal states", that is it learns a weighting for how much of each layer's hidden state it should use for that word. For example, for word "bow" if you want a 70/30 split of context dependent to syntactic aspects, your embedding for "bow" is the hidden states of the final layer * 0.7 plus the hidden states of the lower layer * 0.3. Remember there are only two layers. Even if the model is frozen, you want to learn this weighting as it is task specific. 

It is trained as a language model - predict the next word in a sequence of words.

Model has 2 layers, 4096 hidden units and 512 input units and a residual connection between the layers. The character CNN has 2048 filters, but it doesn't say what size they are, as well as two highway layers and a linear projection to 512 units.

To use it, freeze the biLSTM but learn the linear weighting and concatenate it with the word passed through a standard embedding layer.

They get between 6 - 20% relative error reductions on tasks: question answering (SQuAD), textual entailment (NLI), semantic role labeling (SRL), coreference resolution (CONLL 2012), named entity recognition (COnLL 2003) and sentiment analysis (SST-5).

---

https://www.mihaileric.com/posts/deep-contextualized-word-representations-elmo/
https://allennlp.org/elmo
https://paperswithcode.com/paper/deep-contextualized-word-representations
https://soundcloud.com/nlp-highlights/56-deep-contextualized-word-representations-with-matthew-peters