# Maybe Deep Neural Networks are the Best Choice for Modeling Source Code (2019)

Rafael-Michael Karamptatsis, Charles Sutton

---

Applying NLP to code has shown promising results. However, one issue is that code introduces a larger vocabulary than natural language. The classic requirement of a fixed vocabulary size gives rise to poor performance. They present an open vocabulary language model that is not limited to a fixed vocabulary of identifier names. They segment names into subwords.

The hypothesis is that most of software is *natural*, as it was written for humans to read it displays similar statistical properties to natural languages. A language model is used to quantify the degree of naturalness. The more generalizable a language model is to unseen examples from the same domain, the more natural the language is. Language models learn word embeddings that can be used in downstream tasks.

However, research has found that a challenge in using language models on source code is the amount of out-of-vocabulary (OOV) tokens introduced as developers invent new identifier names. OOV tokens cannot be predicted by the language model as they do not appear in the training set. This also occurs in natural language, but it is not as extreme.

They introduce an open vocabulary language model, that does not have a fixed vocabulary at training time. It is based on the idea of subword units, or subtokens. The model outputs subtokens instead of tokens.

Embedding and hidden dimensions of 512. GRU unrolled for 200 time-steps.

The LM predicts subtokens rather than full tokens. Each sequence of subtokens has a special identifier for the end of an actual token. Common tokens are assigned a full subtoken unit, i.e. `public` is common so it is subtokenized remaining as `public`. Using subtokens reduces data sparsity as well as reducing the amount of OOV tokens in the test set.

To find out how to subtokenize each token, they use BPE. They use merge thresholds of 2k, 5k and 10k.

They use a beam search to show the top-k complete tokens, you know when you have hit the end of a search as you have the special end of token identifier.

If you want to use the model on your own dataset: take the pretrained model, change the unrolled time-steps to 20 and train for a single epoch over your data.

They use three different test scenarios:

- static - LM trained on a fixed corpus then tested on a separate test set
- dynamic - train LM, see how well it does on test set then add those test examples to the training set, but never test on them again
- maintenance - LM tested on one file at a time using all other files for training, involves retraining the model for each file in the test set

They find their model performs better than n-gram and cache models and LMs without their "open vocabulary". Their model can also leverage larger corpora than the other models.

They test on Java, C and Python, getting similar results for all three.

---

- https://github.com/mast-group/OpenVocabCodeNLM/