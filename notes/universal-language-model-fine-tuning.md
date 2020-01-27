# Universal Language Model Fine-tuning for Text Classification (2018)

Jeremy Howard, Sebastian Ruder

---

Key idea: instead of only pre-training the embedding layer, pre-train the entire model as a language on lots of data, then fine-tune as a language model on the training data for your task, then fine-tune for classification. There's a few tricks to make this work - *discriminative fine-tuning*, *slanted triangular learning rates* and *gradual unfreezing*.

The main reason why this hasn't been successful in the past is that people do not know how to fine-tune these models as they overfit and suffer from catastrophic forgetting. 

ALWAYS USES 3 LAYER LSTM. Specifically the AWD-LSTM.

Has relation to transfer learning in CV, hypercolumns (representation via concatenating representations from multiple layers), multi-task learning and fine-tuning.

The idea is to transfer from a source task to a target task. Language modeling is a good source task as it captures: long-term depenencies, hierarchical relations and sentiment. Also has lots of data, does not need labels.

ULMFiT has three main parts:
1. general-domain LM pretraining: train the model on a large, general domain corpus
2. target task LM fine-tuning: now we fine-tune the LM on the data of the target task, this uses *discriminative fine-tuning* and *slanted triangular learning rates*
3. target task classifier fine-tuning: now fine-tune the model with a task specific head on the actual target task

Discriminative fine-tuning is where we use a different learning rate at each layer, higher layers have higher learning rates. Idea is that different layers capture different types of information, lower layers capture more general and thus should not need to be updated/changed as much.

Slanted triangular learning rates (fig 2) is a learning rate schedule that increases rapidly at the start of training and then slowly decreases to the initial learning rate. The idea is that we want the model to quickly converge to a suitable region of the parameter space in the beginning of training and then refine its parameters. 

Other additions are concat pooling and gradual unfreezing. I do not really understand the BPT3C concept.

Concat pooling is where the input to the classifier is not just the final hidden state of the LSTM over the sequence, but is a concatenation of the final hidden state, the max pool of all the hidden states in the sequence and the mean pool of all the hidden states in the sequence. The idea is that information get lost in the hundreds of words contained in a document if we only consider the final hidden state.

Gradual unfreezing is where instead of fine-tuning all layers at once, we gradually unfreeze the model, starting from the last layer as this contains the least general knowledge. Unfreeze last layer, train for one epoch, unfreeze next layer, train for one epoch, repeat. Similar to a concept called *chain-thaw* except that only that only has one layer unfreezed at a time.

Results are on IMDB, Yelp Reviews, TREC, AGNews and DBPedia.

Bidirectional LSTM, embedding dim of 400, 3 layers, 1150 hidden dim, BPTT of 70, different dropout on everything, classifier hid dim of 50, different Adam defaults, batch size of 64 and different learning rates for LM and classifier.

ULMFiT allows you to use less examples, pretraining is good, LM doesn't have to be good but helps with overfitting, fine-tuning the LM needs to be done and needs those special tweaks, gradual unfreezing is the best way to fine-tune the classifier.



