# Towards Interpretable Reinforcement Learning Using Attention Augmented Agents (2019)

Alex Mott, Daniel Zoran, Mike Chrzanowski, Daan Wierstra, Danilo J. Rezende

---

This work presents a soft attention model for reinforcement learning.

It uses the same query, value, key structure as the transformer networks and has an information bottleneck which they say "forces [the rl agent] to focus on task-relevant information".

The model takes in an observation, X, and passes it through a *vision core*. The vision comprises of a CNN followed by an RNN (specifically a ConvLSTM) to produce O. They then split O on the channel dimension to get a key and value tensor, K and V, which have multiple "attention heads". They concatenate a fixed "spatial tensor", S, to K and V, where S is calculated similar to the position encoding in a regular transformer model. Then they have an LSTM which takes in the previous *answer vector*, a, and previous *query vector*, q, that passes its hidden state into a *query network* (just an MLP) to get a new query vector, q, which has an output dimension equal to the number of attention heads in the key, K. The inner product of q and K gives A, the attention logits map (i.e. unnormalized attention). This is then normalized via softmax, broadcast along the channel dimension of V, elementwise multiplied with it and then summed. This gives us the answer vector, a. The answer and query vector are then input to the LSTM (the same used for the query network) to give an *output*, o, and new LSTM hidden state.

One important thing about the model is that the query vectors are a function of the LSTM state and not the observation, thus "the RNN can actively query the input for task-relevant information rather than having to filter out large amounts of information" from the raw pixels. The summation used for the action gets rid of all of the spatial information, hence the season for the spatial tensor, S.

They find that the model learns to query the "what" and "where" separately, showing that it has learned to disginguish between content and space. Specifically, the attention focuses on: tracking the region "ahead" of the player, focusing on enemies and focusing on important moving objects - as well as reward specific elements such as power-ups and the actual score display.

They find their attention mechanism generalizes to unseen factors in the environment by acting consistently. They test this by adding a novel enemy at the pixel level. The agent attends to the new enemy and appropriately reacts by moving towards it, shooting at it and then moving away - the fish does not die as it is inserted directly at the pixel level, so the agent repeats this pattern.

They find in games that require some sort of planning, e.g. Ms Pacman, the agent pays attention to the nearby paths it can take. Once it sees an enemy in that path the agent's actions are directed to moving away from the enemy. In games like Breakout, the agent sets up attention "trip-wires" that activate whenever an object crosses them.

Their model is tested on 57 Atari games with the architecture specifics in section 4 and the appendix.

---

- https://sites.google.com/view/s3ta