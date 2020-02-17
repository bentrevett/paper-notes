# How Contextual are Contextualized Word Representations? Comparing the Geometry of BERT, ELMo, and GPT-2 Embeddings (2019)

Kawin Ethayarajh

---

Replacing static word embeddings - representations that are identical for every occurence of a word, no matter the context it appears in - with contextualized word embeddings - representations which depend on the context the word appears in - has yielded significant improvements on many NLP tasks. However, just how contextual are contextualized representations? Is there a unique contextual representations for every word in each unique context, or are representations limited to a finite number of contexts? 

They find that words are, usually, more contextualized at higher layers, similar to how LSTMs produce task specific representations at higher layers.

Having a static representation is bad as polysemous words (words with multiple meanings) are all given the same representation. Contextual word embeddings produce representations sensitive to the context in which they appear. These contextual representations yield improvements on many NLP tasks. Even though they are only trained on a language modelling task, these contextual representations learned by the model contain transferable and task-agnostic properties of language. Research has shown that a simple linear model on top of these contextual representations provides almost state-of-the-art performance - these are called *probing tasks*. 

1. In all layers of BERT, ELMo and GPT-2 the contextual word representations of all words are not isotropic. They are not uniformly distributed w.r.t. direction. They are anisotropic, occupying a narrow cone in vector space. Isotropy is generally considered to be a good thing for static embeddings, so this is surprising.

2. Occurences of the same word in different contexts have non-identical (in terms of cosine similarity) vector representations. This dissimilarity is increased in higher layers, thus the representations in higher layers are more context-specific.

3. In ELMo: representations of words in the same sentence grow more similar to each other as context-specificity increases. In BERT: they become more dissimilar to each other, but are still more similar than randomly sampled words. In GPT-2: words in the same sentence are no more similar to each other than two randomly sampled words.

4. Contextualized representations do not correspond to a finite number of word-sense representations. Static embeddings created by taking the first principal component of a word's contextualized representation outperform GloVe and FastText on some word vector benchmarks.

Experiments are carried out using the SemEval Semantic Textual Similarity task from 2012-2016. These contains sentences in which the same words appear in different contexts. They create a mapping of words to the list of sentences they appear in and their index within those sentences. They ignore words that appear in less than 5 unique contexts.

They measure how contextual a word representation is using three metrics: self-similarity, intra-sentence similarity and maximum explainable variance.

**Self-similarity** of a word is the average consine similarity between each of its contextualized representations across each of its unique contexts. A self-similarity of 1 implies the word is not contextualized at all, all representations are the same across all contexts.

**Intra-sentence similarity** is the average cosine similarity between a word vector and the sentence vector, which is the mean of all the word vectors within that sentence. This measures how context specific the word is compared to the entire sentence - i.e. if the model is just moving the whole sentence contextually instead of the individual words.

**Maximum explainable variance** is the proportion of variance in a word's contextual representation that can be explained by its first principal component. It tells us how well a static embedding could replace a word's contextualized representation, 1 meaning it could be perfectly replaced by a static representation. 

All results are adjusted by isotropy/anisotropy, i.e. all words might be nearby in vector space so a similarity of 0.95 might actually represent contextual representations as the average cosine similarity might be 0.99. They calculate a baseline which is the average cosine similary between the representations of uniformly randomly sampled words (1000) from different contexts. They create a baseline for each layer.

Findings:
-  Contextual representations are anisotrpic in all non-input layers, figure 1. In almost all layers of all models, the representations of all words occupy a narrow cone in the vector space. 
-  Contextualized representations are generally more anisotropic in higher layers.
-  Contextualized word representations are more context-specific in higher layers.
-  Stopwords have the most context-specific representations. Implies that the context a word appears in, and not its polysemy, is what gives variation in the contextualized representation.
-  Context-specificity manifests different in ELMo, BERT and GPT-2.
-  In ELMo, words in the same sentences are more similar to one another in higher layers.
-  In BERT, words in the same sentence are more dissimilar to one another in higher layers.
-  In GPT-2, word representations in the same sentence are no more similar to each other than randomly sampled words.
-  On average, less than 5% of the variance in a word's contextualized representations can be explained by a static embedding.
-  Principal components of contextualized representations in lower layers outperform GloVe and FastText on many benchmarks.