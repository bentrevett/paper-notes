# To Tune or Not to Tune? Adapting Pretrained Representations to Diverse Tasks (March 2019)

Matthew Peters, Sebastian Ruder, Noah A. Smith

---

How do you best adapt a pretrained model to a given target task? Your two options are using the pretrained model as a feature extractor, with the weights frozen, or by directly finetuning the pretrained model. They test the performance of each of these approaches with empirical experiments.

Transfer learning has two stages: pretraining (learning general purpose representations) and adaptation (representation applied to a new task). Not a lot of work has focused on adaptation. The two main methods are feature extraction and fine-tuning. Feature extraction has the model weights frozen and the pretrained representations are using in a downstream model. Fine-tuning does not freeze the weights of the model, allowing the parameters to be fine-tuned on the new task. Freezing allows for task specific architectures and only requires training once, fine-tuning allows adaptation of general-purpose representations to multiple tasks.

They use ELMo and BERT for both feature extraction and fine-tuning across tasks such as NLI (natural language inference) and paraphrase detection. Which conditions cause one approach to outperform the other? Is it dependant on the pretraining objective or the target task? Turns out they're pretty similar unless the source and target taks are either highly similar or dissimilar.

They mention three possible methods to perform the pretraining:

1. Using pretrained word vectors. For feature extraction these are frozen, and in fine-tuning they are not. The use of contextual word representations are becoming more common now as they have improved performance, see ELMo/BERT.

1. Using pretrained sentence embeddings. These involve embedding a whole sentence into an embedding. Objectives are usually: predict the next/previous sentence, NLI, or a combination. During adaptation, the sentence representation is fed to a linear classifier.

1. Masked LM and next-sentence prediction. BERT uses both word and sentence representaitions (via masked LM and next sentence prediction objectives).

Note that they are simply using ELMo and BERT pretrained by someone else. They aren't training them from scratch themselves.

The tasks they test on are:

1. NER (token level annotations) using the CONLL2003 dataset

1. Binary sentiment analysis (SA) on IMDb

1. NLI on both MultiNLI and SICK-E

1. Paraphrase detection (PD) on Microsoft Research Paraphrase Corpus (MRPC)

1. Semantic Textual Similarity Benchmark (STS-B) and SICK-R

That is seven tasks in total, one NER, one SA, two NLI and three STS.

For feature extraction they use a task-specific architecture, whereas for finetuning they use a tasks-specific output layer. They also do a hyperparameter search for each task.

For feature extraction on both ELMo and BERT they get the contextual representations from all layers. During adaptation they learn a linear weighted combination of the layers which is input to a task specific model. They use all layers as they say that the internal layers typically encode the most transferable representations. For SA, the use a bi-attentive classification network. For sentence pair tasks, they use ESIM. For NER, a BiLSTM with a CRF layer.

For fine-tuning on ELMo they max-pool over the LM states and add a softmax for text classification. For the sentence pair tasks they compute cross-sentence bi-attention between the LSTM states, applying a pooling operation then adding a softmax layer. For NER, they use a CRF layer on top of the LSTM states. For fine-tuning on BERT they feed the sentence representation into a softmax layer for text classification and sentence pair tasks. For NER, they extract the representation of the first word piece for each token and add a softmax layer.

Table 2 shows results for ELMo and BERT across all seven tasks with a baseline sentence embedding method, Skip-thoughts, that uses a next-sentence objective similar to BERT. Both ELMo and BERT consistently outperform Skip-thoughts. Performance is similar on all tasks except for: 

1. ELMo as a feature extractor outperforms fine-tuned ELMo for the two NLI tasks (significantly) and two of the three sentence similarity tasks (SICK-R significantly and MRPC moderately). It does outperform fine-tuned ELMo on the last STS task (STS-B), but only by a small amount.

1. BERT when fine-tuned outperforms feature extractor BERT significantly on the three STS tasks.

If using ELMo and your task is NLI or STS, use it as a feature extractor. If using BERT and your task is STS, fine-tune it. In the other cases it doesn't matter, so you should probably use as a feature extractor as it's quicker to train(?)

Why do we get these results? Past research has shown that similar pretraining tasks transfer better. In computer vision (CV) they have found that the transferability of features decreases as the distance between the pretraining and target tasks increase. Skip-thoughts and BERT both are trained using a form of next-sentence prediction, which is similar to the STS task, hence why they both (BERT especially) do well on this task as it has a close alignment between pretraining and target task.

In CV, fine-tuning outperforms feature extraction when transferring from ImageNet supervised classification pretraining to other classification tasks. Research has found fine-tuning is less useful for more distance target tasks, such as semantic segmentation. This is similar to their results, where fine-tuning achieves the greatest performance on closely aligned tasks (next-sentence prediction on BERT and STS tasks) and poor performance for more distant tasks (LM in ELMo and sentence pair tasks).

However, one other explanation for this might be because of some inductive biases within the model, i.e. LSTMs (for ELMo) might just be better when frozen and Transformers (for BERT) might just be better when fine-tuned.

LSTMs consider each token sequentially, whereas Transformers relate each token to every other in each layer. This might help Transformers fine-tune better on sentence pair tasks, where fine-tuned ELMo performs poorly. To test this, they try different ways of encoding sentence pairs with ELMo and BERT. They try ELMo fine-tuning with and without cross-sentence bi-attention (by default they used the cross-sentence bi-attwention). When fine-tuning the ELMo LSTM to a sentence pair task, fine-tuning through the bi-attention layer gives better performance than without it. This shows that the LSTM has difficulty modeling pairwise interactions during sequential processing.

For BERT as a feature extractor, they compare jointly encoding of the sentence pair (the default) against separately encoding the sentence pairs. Separately encoding them leads to a drop in performance, which shows the BERT representations encode cross-sentence relationships and are therefore well-suited for sentence pair tasks.

They also try adding more parameters (via additional layers) in the NER task. They try with an additional CRF layer and an additional biLSTM + CRF layer. They find that additional parameters help with feature-extraction. They say it hurts for fine-tuning unless you use gradual unfreezing.

They find ELMo difficult to fine-tune and requires careful hyperparameter tuning. However, once you find the parameters for one task, other tasks have similar parameters. They found that using slanted triangular learning rates, discriminative fine-tuning and sometimes gradual unfreezing helps.

They also do some small test on the difference between source and target domain, but I don't understand it. They pretrained BERT on a sub-section of MultiNLI and then test how well frozen and fine-tuned BERT do on that same domain. Turns out they both do very similar. How does this test different domains?

How does the information in the different layers of the model change when fine-tuning? They try by using a classifier on each of the layers for both frozen and fine-tuned. They find that you get higher performance using information from higher layers for all tasks, and that fine-tuning is better than frozen. For classification tasks, performance gradually increases as you go up the layers, but for sentence similarity tasks performance plateaus after the 4th layer (out of 12). They say the fact that sentence similarity tasks have all of their information in the first 4 layers explains why fine-tuning is useful in these scenarios. Not sure I get why.

They also measure the mutual information (MI) between the output of a hidden layer and the output of a final layer and find that using feature extraction it is pretty much zero for all layers/tasks except for SA where the last few layers are very small non-zero values. Fine-tuning has much higher MI values, gradually rising as the layers increase in the SST and sharply increasing in the final layers for sentence classification tasks. Thus, knowledge for single sentence classification tasks is mostly concentrated in the last layers, whereas for pair sentence classification tasks the information is built up as the layers increase.

The appendix contains useful model and training details.