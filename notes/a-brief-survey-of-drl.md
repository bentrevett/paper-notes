# A Brief Survey of Deep Reinforcement Learning (2017)

Kai Arulkumaran, Marc Peter Deisenroth, Miles Brundage, Anil Anthony Bharath

---

Deep learning is allowing reinforcement learning (RL) to scale to problems that were previously intractable, such as learning to play video games from pixels. Can also apply to robotics, allowing policies to be learned from a camera feed.

They briefly cover: deep Q-networks (DQN), trust region policy optimization (TRPO), and asynchronous actor critic (A2C).

RL is a mathematical framework for experience-driven autonomous learning. It had success in the past, but had difficulty scaling up to high-dimensional problems. Now deep learning (DL) has taken off we have function approximation and representation learning from deep neural networks, we can use these in our RL problems by converting the high-dimensional inputs (images, text, audio) into low-dimensional features. This combination of DL and RL is called deep reinforcement learning (DRL).

One of the first DRL success stories was learning to play Atari 2600 games from pixels using a DQN. This used a convolutional neural network (CNN) to obtain features from the raw pixel input. The second major success was AlphaGo, which was able to play Go at a superhuman level. It used a combination of neural networks trained via supervised learning and deep learning, as well as traditional search algorithms.

RL is not just useful for playing games. RL is a general way of optimizing performance on a problem by trial-and-error. It has been used for managing power consumption, designing machine translation models and constructing new optimization functions.

The essence of RL is learning through interaction. The RL *agent* interacts with the environment, and after observaing the consequences of its actions can then learn to adjust its own behaviour in response to the reward achieved.

The *agent*, controlled by a machine learning algorithm, observes a *state* from its *environment*. The agent interactions with its environment by taking an *action* in a state. When the agent takes an action, the environment and agent transition to a new state based on the current state and chosen action. The best sequence of actions is determined by the *rewards* provided by the enviroment. Every state transition causes the environment to return a scalar reward to the agent. The goal of the agent is to learn a *policy* (control strategy) that maximizes the *expected return* (cumulative discounted reward).

The challenge in RL is that the agent needs to learn about consequences of actions in the environment by trial-and-error. It does not have access to a model of the state transition dynamics for the environment.

Formally, RL can be described as a *Markov decision process* (MDP) which consists of:

- a set of *states*, plus distribution over starting states
- a set of *actions*
- *transition dynamics* that map a state-action pair onto a distribution of next states
- a *reward function* that returns a scalar return based on the state, action and next state
- a *discount factor*, between 0 and 1, where lower values place more emphasis on immediate (short term) rewards

The policy can be thought of as mapping states to a probability distribution over actions, with the goal to learn a policy that maximizes the *expected return*.

A key concept of RL is the *Markov property*, only the current state affects the next state. This assumption is held by many RL algorithms, although it is only true of fully observable environments. A generalization of this is *partially observation MDPs* (POMDPs) in which the agent receives an observation based on the current state and the previous action. It is common to use recurrent neural networks (RNNs) to solve POMDPs in DRL as the RNN is a dynamic system unlike typical feedforward neural networks.

There are some major challenges faced in RL:

- The optimal policy must be inferred by trial-and-error interaction with the environment, the only learning signal the agent receives is the scalar reward.
- The observations of the agent depend on its actions and can contain strong temporal correlations.
- Agents must deal with long-range time dependencies, as often the consequences of an action only materialize after many transitions of the environment. This is called the *credit assignment problem*.

There are two main approaches to solving RL: methods based on *value functions* and methods based on *policy search*. There are also *actor-critic* approaches that are a hybrid of the two, using both value functions and policy search.

Value function methods are based on estimating the value (expected return) of being in a given state. The *state-value function*, V, gives the expected return when starting in a given state and following the given policy afterwards. We also have a *state-action-value function*, Q, also called a *quality function*. This is similar to the state-value function except the initial action is provided, so it gives us the expected return following a given policy when starting in a specific state and carrying out a given action.

To actually learn a quality function we exploit the Markov property and write it in terms of a recursive equation called the *Bellman equation*. This means we can *bootstrap* the quality function by using the current estimate of the quality function to improve our estimate. This is where we get the *temporal difference* (TD) error, the difference between the quality of the action taken and the discounted quality value of the best action in the next state plus the immediate reward. Q-learning is an *off-policy* algorithm as the best action from the next state used to calculate our TD error might not be the actual action we took in the next state. In contrast, the *SARSA* (state-action-reward-state-action) algorithm is *on-policy* as it calculates temporal difference error using the action it actually took in the next state.

Policy search methods do not maintain a value function model, but directly search for an optimal policy. Typically, a parameterized policy is chosen where the parameters are updated to maximize the expected return. When constructing the policy directly, it is common to output parameters for a probability distribution. For continuous actions this could be the mean and standard deviations of Gaussian distributions, and for discrete actions this could be a multinomial distribution. This gives us a stochastic policy which we can directly sample actions. One benefit of policy search is that we can use gradient-free methods.

This summary has already gotten too long, so I won't write down the rest of it.