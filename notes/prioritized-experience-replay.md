# Prioritized Experience Replay (2016)

Tom Schaul, John Quan, Ioannis Antonoglou, David Silver

---

Experience replay allows for online reinforcement learning (RL) agents to re-use past experiences. It is used to break correlations between weight updates and to avoid forgetting of experiences.  In past work, experiences are sampled from the replay memory uniformly. The obvious question here is, should more "important" experiences be seen more often?

In this work, the authors *prioritize* certain transitions in the replay memory, with the idea that some transitions can help the RL agent learn more effectively and call this *prioritized experience replay* (PER). They propose weighting transitions in the replay memory by their temporal-difference (TD) error. The first time a transition appears in memory, we haven't yet calculated the TD error for it, so transitions without a TD error are given top priority.

A greedy approach will be to always fill a batch of size B with the B highest TD error samples, however this will bias the algorithm into pretty much always picking the same B samples, causing it to overfit. Thus, they pick transitions using a weighted probability based on their TD error (think softmax). This weighted probably has a hyperparameter, a, where a = 0 means uniform sampling. They do not change the value of a during training.

The first variant, *proportional prioritization*, uses absolute TD error plus a small epsilon value so all transitions have a non-zero probability of being picked. The second variant, *rank-based prioritization*, uses 1/rank(t) where rank(t) is the ranked TD error of the transition, i.e. the 2nd highest TD error will have rank(t) = 2. They initially believed the second would be better as it is more robust to outliers, but it turns out they perform pretty much the same which they say is due to the rewards being clipped to either -1, +1 or 0 in the Atari environments and the DQN using a clipped (Huber) loss function.

Having a large memory with lots of transitions that need to be prioritized requires a few tricks to implemenet efficiently. For *rank-based prioritization*, they cut the memory into segments, i.e. these are high priority, these are low priority, etc. Then when sampling from the memory they pick uniformly in each segment. For *prorportional prioritization*, they use a data structure called a sum-tree, which represents the replay memory as a tree with N (capacity of memory) leaf nodes, where each node is the value of the absolute TD error plus epsilon. Each pair of leaf nodes has a parent which is the sum of the two leaf nodes.

One problem with prioritized sampling is that certain examples will be seen more often than others. This will bias your model towards those samples, causing it to overfit. They do *importance sampling*, which weights the loss calculated by the DQN by how prioritized the sample is. Samples with higher priority have lower weight, so they don't change the parameters of the neural network much, as they will be seen more often. These weights are also normalized so they can only scale the loss value downward. This weight can be annealed with a parameter, b, where b = 1 means they fully use importance sampling, and b < 1 means they do not. They linearly anneal b from some value to 1 during training.

They experiment with double DQN, with the only change in hyperparameters being scaling the learning rate down by 4 as they found the model picking higher error transitions lead to larger gradient values. They keep the same hyperparameters across all Atari games.

They found that PER gives higher scores in 41/49 Atari games and causing learning to be twice as fast.

---

- https://medium.freecodecamp.org/improvements-in-deep-q-learning-dueling-double-dqn-prioritized-experience-replay-and-fixed-58b130cc5682
- https://pemami4911.github.io/paper-summaries/deep-rl/2016/01/26/prioritizing-experience-replay.html
- https://jaromiru.com/2016/11/07/lets-make-a-dqn-double-learning-and-prioritized-experience-replay/
- https://datascience.stackexchange.com/questions/32873/prioritized-replay-what-does-importance-sampling-really-do
- https://github.com/rlcode/per/
- https://github.com/txzhao/rl-zoo/
- https://github.com/jaromiru/AI-blog
- https://github.com/Damcy/prioritized-experience-replay
- https://github.com/openai/baselines/blob/master/baselines/deepq/replay_buffer.py