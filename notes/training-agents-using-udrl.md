# Training Agents using Upside-Down Reinforcement Learning (2019)

Rupesh Kumar Srivastava, Pranav Shyam, Filipe Mutz, Wojciech Jaśkowski, Jürgen Schmidhuber

---

Traditional RL algorithms maximize rewards by either using value functions (such as DQN) or by using a policy search (such as A2C). This paper flips the idea on its head by using the reward as part of the input and predicting the action (actually a probability over actions) that the agent should take. They call this *upside-down reinforcement learning* (UDRL). 

UDRL can be framed as a supervised learning (SL) problem unlike standard RL. URDL is trained using SL on past experiences, i.e. with a replay buffer. This helps combat the difficulty with RL which arises due to the use of function approximation, bootstrapping and off-policy training. 

Standard notation used as in RL papers: s, a, r, pi. Trajectory tau is a (s_t, a_t, r_t, s_t+1) tuple. A subsequence of a trajectory is known as a *segment* or *behaviour*. The *return* is the cumulative reward over a segement. UDRL does not use discount factors.

Standard RL algorithms are either value-based or policy-based. Value-based algorithms, such as DQN, predict the expected discounted future return for taking an action in a state, aka temporal difference (TD) learning. Policy-based algorithms, such as A2C, directly search for policies that maximize returns by learning a mapping of state to actions, where the actions selected should maximize returns.

UDRL instead is given a *command* it defines a *behaviour function*, B, that contains knowledge about the behaviours observed so far that are compatible with the known commands. The example they use is an agent to throw a dart at a board. You would give the agent the command of hitting a certain spot on the board and it would produce the action in order to do that.

An optimal behaviour function for a set of trajectories is given by:

B = min sum_(t1, t2) L(B(s_t1, d^r, d^h), a_t1)

0 < t1 < t2 < len(tau)

d^r = sum_(t1, t2) r_t

d^h = t2 - t1

In other words, B takes in the current state at t1, the sum of rewards between t1 and t2 and the difference of rewards between t2 and t1. B should then output the desired action. They then train B to predict a_t1 via cross entropy in a SL setting.

This theory, they can use a completely random policy to generate all potential trajectories and then learn them to give any behaviour they want, e.g. get +5 reward in 10 time-steps, get -5 reward in 1000 time-steps, etc. However, the goal of RL is to learn to achieve the maximium return - not to learn any behaviour. Thus, they train B on the set of trajectories so far and let the algorithm collect trajectories with higher returns. These returns are stored in a replay buffer - which is initially empty but is filled with random actions during a warm-up stage. B is then trained in a supervised manner on the replay buffer.

UDRL is not aiming to explicitly maximize returns, but to explore in order to discover high return trajectories so the behaviour function can be trained on them. The replay buffer is filled with the highest return trajectories seen so far, sorted by return.

B is a function of s_t and c_t, where c_t = (d^r_t, d^h_t). d^r_t is the *desired return* and d^h_t is the *desired time horizon* - both are scalars. The action distribution output by B is supposed to lead to successful execution of the *command*, c_t, which can be interpreted as "achieve a return of d^r_t during the next d^h_t steps". Starting from c_0, B will generate a trajectory by sampling actions for the current command and then updating the command according to the obtained rewards and elapsed time.

Note that d^h_t is always set to at least 1 so there is always a valid time horizon. d^r_t is clipped to the maximum return achievable in the environment. 

B is trained via SL on the replay buffer, with the goal to produce outputs consistent with previously recorded trajectories. The samples drawn must have t1 < t2. They only train with t_2 = T - 1, which they call "trailing segments".

After training, the agent can attempt to generate new behaviour, potentially with higher returns. To do this, we must get a batch of new initial command, c_0. First, select a number of episodes from the replay buffer with the highest returns. Set d^h_0, the desired horizon, to the mean lengths of the selected episodes. d^r_0, the desired returns, are sampled from U(M, M+S) where M is the mean and S is the standard deviation of the selected episodic returns.

We then generate exploratory episodes by sampling from the action distribution output by the exploratory commands detailed above. The agent can be evaluated from the exploratory commands - with desired return set to the lower bound of the desired returns from the most recent exploratory commands and the initial desired horizon from the most recent exploratory command.

They test their model against DQN and A2C on LunarLander-v2 (using scalar state values, not image) and the TakeCover-v0 VizDoom environment. For TakeCover, the reward is +1 at every time-step, so the desired horizon is always the same as the desired reward. LunarLander was implemented with a MLP, TakeCover with a CNN. Hyperparameters searched over **but not the ones they actually used** are in the appendix. Command inputs are scaled, passed through a sigmoid layer and then elementwise multiplied with an embedding of the observed inputs.

They find that URDL does worse in LunarLander, but better in TakeCover. However, when they introduce LunarLanderSparse, where the reward is not provided until the terminal state, they find UDRL trains faster and more reliably than A2C and DQN. 

They make an interesting note about how the task in LunarLander and LunarLanderSpare is exactly the same, with the total episode return being the exact same for each. Yet they give totally different results for the three algorithms based soley on the design of the reward function. They state that reward functions for environments are often developed in tangent with the environments themselves, and thus may be biased toward certain learning algorithms than others, e.g. ones that favour dense rewards rather than sparse.

They find there is a strong correlation between the desired episode return and the obtained mean episode return, indicating that UDRL is actually trying to learn to output actions to achieve the desired behaviour. However, some seeds provide agents that do not have this property and this property only existed in LunarLander and LunarLanderSparse.

They postulate that new environments and benchmarks may be created that fit UDRL better than standard RL methods. TD-based RL algorithms are sensitive to how often an action is taken, whereas UDRL is less so.

Further work involves: expanding to RNNs for partially observable environments, research into new command inputs, SL techniques, replay buffer sampling methods, combinations of UDRL and traditional RL.