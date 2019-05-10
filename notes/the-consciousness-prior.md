# The Consciousness Prior (2017)

Yoshua Bengio

---

This paper proposed a new prior for representation learning. It can be combined with other priors to disentangle abstract factors from one another. It is inspired by human consciousness, which can be thought of as a low-dimensional representation of conscious thought. Consciousness is defined as "the perception of what passes in a man's own mind".  

This low-dimensionality of the representation is used as a regularizer which encourages the abstract representation to be such that when a sparse attention mechanism focuses on a few elements of the representation, the small set of variables can be combined to make a useful statement about reality or usefully condition an action or policy.

We have a recurrent neural network (RNN), h_t = F(s_t, h_{t-1}). s is the *observed state*, F is the *representation RNN* and h is the *representation state*. Think of F as the human brain, thus h is the high-dimensional. We want to learn good representation states which contain abstract explanatory factors. We want to be able to transform h so we can extract information about a single one of those factors.

In contrast, the *conscious state*, c, is very low-dimensional, derviced from h by an attention mechanism applied to h: c_t = C(h_t, c_{t-1}, z_t). z is a random noise source. You can think of c as the content of a thought. This is a small subset of all the information available to us unconsciously, but which has been brought to our awareness by the attention mechanism which uses several elements from h. C is the *consciousness RNN*. The random noise means the elements that get focused on have some stochasisity. Thus, the consciousness RNN is used to isolate a high-level abstraction and extract information from it. In general, C will aggregate a few factors of information - not just a single factor - into a more complex composed thought.

We want to assume this conscious thought can encapsulte a statement about the future. We do this with a *verifier network*, V(h_t, c_{t-k}) which outputs a scalar value. Here, the objective is to output h_t with the previous k conscious states. We want to define an objective function or reward function that uses the attended conscious elements in a way in which they can be quantified and optimized for.

The two mechanisms which map the high-level state representation to an objective function are:

1. the attention mechanism in the consciousness RNN which selects and combines a few elements from the high-level state representation into a low-dimensional consciousness "sub-state" objects

1. the predictions or actions derived from the sequence of these conscious sub-states

The difficulty is finding a way for the algorithm to pay attention to the most useful elements. Some form of entropy may be needed to make the attention mechanism stochastic.

There is also a link between consciousness and a natural language utterance. An externally provided setence could elicit an associated conscious state. Thought the conscious state is a richer object (high dimensions?) than the uttered stence. Think about mapping consciousness to sentences, there is always a loss of information. There also needs to be some context, as the same sentence could be interpreted differently depending on the context. This could be done with another RNN, which maps a conscious state to an utterance: u_t = U(c_t, u_{t-1}).

You can think of this as another regularization term, the loss of information from consciousness to utterance. A sentence focuses only on a handful of elements and concepts, unlike our full internal consciousness.

This can be used in unsuperivsed reinforcement learning, testing its ability to discover high-level abstractions, e.g. using an intrinsic reward which fsvours the discovery of how the environment works.

---

- https://www.quora.com/What-is-Yoshua-Bengios-new-Consciousness-Prior-paper-about
- http://thegrandjanitor.com/2018/05/09/a-read-on-the-consciousness-prior-by-prof-yoshua-bengio/
- https://timniven.github.io/ai/papers/2018/08/12/consciousness-prior.html