# Contextual Word Representations: A Contextual Introduction (2019)

Noah A. Smith

---

Paper is a general understanding of word vectors (aka word embeddings): why they exist, what problems they solve, where they come from, how they have changed over time, and what some of the open questions about them are.

A *word token* is a word observed in a piece of text. In English the boundary between words is usually whitespace and punctuation.

A *word type* is the word in an abstract form. Every word token belongs to its type. When counting the occurences of a word in corpus we are counting the tokens that belong to the same word type.

In computers words are canonically represented as a sequence of characters, a string. In NLP, words are numericalized so that each word type is given a unique non-negative integer value. The vocabulary (the mapping of integers to their word type) can be continuously expanded as new word types were encountered. The integers didn't mean anything. Two words with related meanings might be assigned distance integers, and two words with adjacent word types might have nothing to do with each other. We can think of these integers as *discrete representations*.

In NLP, word types are no longer treated as discrete. Why? Consider some of the ways are used in NLP applications:
    - A word token in a document gives evidence to help predict the category of the document, i.e. positive words indicate a positive product review
    - A word token in a sentence gives evidence to what a word token in the translation of the sentence is

With discrete representations information about how to use a word as evidence cannot be shared across words with similar properties. Consider the example when filling in the missing word at the end of the sentence:
    The papers will be signed Tuesday night, and we'll be able to move in to the new house on ...
The next word token is probably either one of Wednesday, Thursday or Friday. These three words share something and we want our NLP models to use this information.

Knowing when words are similar is critical to NLP programs using supervised machine learning. When the machine learning algorithm should ideally exploit similarity, where anything it discovers about one word should transfer to similar words. 

Where does this similarity come from? Two models of thought: 
    - Humans intrinsically know this information so it can be explicitly coded in data structures
    - Information resides in the text corpora and we can use programs to extract this information
The second is now dominant due to the rise of large text collections using the internet.

From both of these models we can derive the notion of a word type as a *vector* instead of a single integer. This allows us to choose the dimensionality of the word and what each dimension represents, for example:
    - Each word type is given its own dimension and is assigned 1 in that dimension, with 0 in the rest. This is the same as numericalizing words and is called a *one-hot vector*.
    - For a collection of word types that belong to a known class, e.g. days of the week, can get 1 in a dimension, whereas all other words not in that class get a 0 in that dimension
    - Word types from the same underlying root can be grouped together a above, e.g. know, known, knew, knows
    - Etc, etc.

The term for each of these dimensions is *features*. These features can be designed by experts or can be derived automatically using algorithms. 

Words that can be used in a similar way are likely to have related meanings. In a large corpus we can collection information about the ways a word type, w, is used by counting the number of times it appears near every other word. If we look at the distribution of contexts in a corpus where w is found we are taking a *distributional* view of a word meaning.

This can be used for clustering, where words are automatically organized into clusters based on the contexts they appear in, within a corpus. Words that occured in similar contexts were grouper together into a cluster. These clusters could be merged into larger clusters which gives a hierarchy.

These vectors can have their dimensionality reduced, which removes redundancies across dimensions. These more compact vectors are more efficient to compute with and the compression may be a benefit due to the removal of corpus-specific noise. This allows us to the do vector arithmetic to get sensible results, such as the famous example of "man" - "woman" = "king" - "queen". The disadvantage is that the individual dimensions are no longer interpretable. The entire meaning of the word is *distributed* across the entire vector, hence they are called *distributed representations*.

As the corpora used in NLP grew, the number of observable contexts grew as well. The underlying notion is that the value placed in each dimension of the word type's vector is a parameter to be optimized to best fit the observed patterns of the word in the data. These paramters can be optimized with gradient descent algorithms, such as *word2vec*.

When applying neural networks to NLP, each word token is mapped to a vector and then fed to the neural network. The vectors can be fixed in advanced (pretrained) or can be treated as parameters of the neural network model and adapted. Finetuning is initializing the word vectors by pretraining and then adapting them to the task. The word vectors can also be initialized randomly and then adapted to the task.

You can build a bilingual dictionary to align words in two languages into a single vector with a small Euclidean distance, e.g. cucumber and concombre. 

Word vectors can be calculated from the sequence of characters that make up the word. This means that words the same underlying root have similar vectors, and that differently spelled variants of the same word will also have similar vectors, e.g. misspellings of a word.

Assuming each word type would be represented by a fixed data object is convenient but makes assumptions about language that do not fit with reality. In recent neural network NLP systems the first thing that happens is each word token's type vector is passed to a function that transforms it based on the words in its nearby context. This gives a version of the word vector that will be specific to the token in its particular context, e.g. the word vector for "bow" in "i shot my bow and arrow" and "the performers took a bow" will be different. 

This makes the task of representing words easier, before the word vector for "bow" had to represent all possible contexts the word could appear in, now we have a different vector for each context.

ELMo (embeddings from language models) introduces *word token vectors*, aka vectors for words in context, or **contextual word vectors** which are pretrained on a large corpora. The two important insights of ELMo are:
    - If every word token is going to have its own vector, the vector should have a long context of nearby words. This *context vector* is obtained from a neural network which takes in word type vectors from the left and right side of the word token and produces a fixed-length vector. Thus the contextual word vectors include both type vectors and neural network parameters that *contextualize* each word. ELMo has one neural network for left contexts (from the beginning of the document) and one for the right contexts (from the end of the document). 
    - Estimating word vectors requires "fitting the data" by solving an optimization problem. A common data-fitting problem in NLP is language modeling, which is predicting the next word given a sequence of previous words. 

ELMo was followed by a similar approach, ULMFiT, and then succeeded by BERT (bidirectional encoder representations from transformers). BERT is trained slightly differently, it is trained to predict a word given the context on each side and to predict toe words in a sentence given its preceding sentence. 

The contextual word vectors output by these models have been shown to improve performance (compared to non-contextual word vectors) on NLP tasks such as: question answering, named entity recognition, coreference resolution and semantic role labeling.

Three important points:
    - Word vectors are biased. These models are trained on data will reflect what is in the data. If the text corpus contains any cultural biases then these will persist in the word vectors and any system that uses them.
    - Language is more than words. Just knowing the meaning of words is not enough to understand language, it requires understanding how words are put together to form more complicated concepts.
    - NLP is not a single problem. Performance improvements from contextual word vectors have been on a handful of benchmark datasets. NLP can only make progress if there are ways of objectively measuring progress, thus benchmarks are required.