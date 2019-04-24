# Human-level Control Through Deep Reinforcement Learning (2015)

Volodymyr Mnih, Koray Kavukcuoglu, David Silver, Andrei A. Rusu, Joel Veness, Marc G. Bellemare, Alex Graves, Martin Riedmiller, Andreas K. Fidjeland, Georg Ostrovski, Stig Petersen, Charles Beattie, Amir Sadik, Ioannis Antonoglou, Helen King, Dharshan Kumaran, Daan Wierstra, Shane Legg and Demis Hassabis

---

Reinforcement learning agents have achieved some successes in a variety of domains, however their applicability has been limited to domains in which useful features can be hand-crafted or domains with fully observed, low-dimensional state spaces.

This work applies the success of deep neural networks (specifically convolutional neural networks), that extract features from high-dimensional inputs, to reinforcement learning by using a deep Q-network (DQN). It learns successful policies directly from high-dimensional inputs in an end-to-end fashion.

The agent interacts with an environment through a sequence of observations, actions and rewards. The goal of the agent is to select actions that maximize cumulative future (discounted) reward. Specifically, by using the DQN to approximate the optimal action-value (Q) function.

There have been attempts to use a non-linear function approximator (a neural network) to represent the Q function in the past. These were unstable due to:

1. correlations present in the sequence of observations
1. small updates to Q may cause significant change in the policy and thus the data distribution
1. correlations between Q values and target values (target = reward plus discounted max next Q value)

To get rid of the first two issues, they use *experience replay*. For each rollout, each transition is stored in a buffer. The DQN is trained from transitions uniformly sampled from this buffer. This effectively shuffles the input data, removing correlations between subsequent observations and smoothing over the changes in the data distribution.  

To get rid of correlations between the Q values and target values, they calculate the target values with a second DQN that has identical architecture to the first. This *target network*'s parameters are set to the main network's parameters periodically and are otherwise fixed. As the targets from the target network remain static for a period of time, this allows more stability in training.

They test their algorithm on 49 Atari 2600 games. They use the same architecture and hyperparameters for all games, taking the raw pixel input (210x160 colour @ 60 Hz) and outputting the Q value of each action. DQN performs better than existing RL algorithms on 43 of the games. They also find it is comparable to human performance on most of the games.

They used t-SNE on the last hidden layer (before or after activations?) and found that the DQN represented states that are close in similar reward, but distinct in how they actually look, were represented close together.

The raw pixel input has some significant processing on it before it is passed to the DQN:

1. each pixel is the maximum value between the current and previous frames (this removes flickering in some games when objects only appear in every other frame)
1. the image is greyscaled
1. the image is rescaled to 84x84
1. the input is only every 4th frame (maximum is still between consecutive frames)
1. 4 of these "every 4th frame" are stacked on top of each other

They stack frames to get a sense of "time", i.e. with a single frame you can't tell if an object is going up or down or its velocity, but you can with a sequence of 4 frames. They skip frames as apparently 1 frame with the greyscale/downsampling doesn't really give enough information about the motion of an object. When they do an action, it is repeated for all of the 3 skipped frames.

The DQN is made of 3 convolutional layers, following by a hidden layer and an output layer. All layers, except the output layer, have the ReLU activation function applied.

They set all positive rewards to +1 and all negative rewards to -1, leaving rewards of 0 unchanged. This is done to standardize the rewards across all games, allowing them to use the same hyperparameters. Though it means the agent cannot tell between small positive and large positive rewards.

They train is RMSprop, epsilon-greedy with epsilon decayed from 1 to 0.1 over the first million frames. They train for 50 million frames (approx. 38 days of game experience), with a replay memory buffer size of 1 million most recent frames.

The training algorithm is:

- initialize replay memory
- initialize DQN with random weights
- initialize target network with the same weights as the DQN
- for each episode:
  - get initialize state and pre-process
  - while episode is not over:
    - with probability epsilon, select random action, otherwise take max Q action
    - execute action and receive reward and next state, then pre-process it
    - add state, action, reward and next-state into replay memory
    - sample a batch of transitions from replay memory
    - target is reward if next state is a terminal state, or reward plus discounted max Q value of next state (from target network) if next state is not terminal
    - calculate error from squared difference between DQN predicted Q value of current state and target value
    - every C steps, update target network parameters to be equal to DQN parameters

---

- https://danieltakeshi.github.io/2016/11/25/frame-skipping-and-preprocessing-for-deep-q-networks-on-atari-2600-games/
- https://pytorch.org/tutorials/intermediate/reinforcement_q_learning.html