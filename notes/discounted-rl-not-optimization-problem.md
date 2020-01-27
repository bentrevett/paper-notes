# Discounted Reinforcement Learning is Not an Optimization Problem (2019)

Abhishek Naik, Roshan Shariff, Niko Yasui, Hengshuai Yao, Richard S. Sutton

---

Discounted RL should not be used with function approximation, i.e. neural networks, for continuing tasks, i.e. tasks that cannot be split into discrete episodes. This is because it does not have an objective function.

RL is a paradigm in which an agent learns to interact with an environment in order to maximize reward and can be formulated as a markov decision process (MDP). An MDP consists of a finite set of states, S, and a finite set of actions, A, that can be performed in those states. When an agent performs action in a state it transitions to another state and receives a scalar reward. MDPs can be represented by a 4-tuple of (S, A, P, r). When performing action, a, in state, s, the agent receives reward r(s, a) and transitions to state s' with probability Pa(s, s'). This MDP follows the Markov assumption, where the transition probabilities and rewards are independent of previous states.

A policy, pi : S x A -> [0,1] gives, for every state in the MDP, a probability distribution over actions to be taken in that state. 

Many interesting RL problems like controlling data centers and managing warehouse inventory are "continuing" tasks. The agent lives and learns over a single lifetime. In continuing tasks rewards are temporally discounted with immediate rewards being valued higher than rewards in the future. By discounting, the sum of future rewards is bounded even when the length of the agent's life is not.

RL is often phrased as an optimization problem with the task of finding the policy that maximizes reward. Optimization problems have a set of solutions and an objective function that describes how good each solution is with a real number. In RL these solutions are policies and for episodic tasks the objective function is the sum of rewards over an episode. **For continuing tasks, they argue that discounted reinforcement learning is not an optimization problem - it does not maximize any objective function**.

A policy is optimal in the discounted formula if it achieves a higher discounted sum of future rewards in each state than any other policy. 

v_pi*(s) >= v_pi(s) for all states, s, and policies, pi

Rather than an objective function, these inequalities produce a *partial order* on the set of policies. It is partial as some policies are incomparable as each achives a higher value in some states but a lower value in others. They argue that this partial order fails to identify an optimal policy when using function approximation.

**There is no optimal representable policy when using function approximation with discounting**

For most RL problems the set of policies is too large to represent. Most of the time it is too large so we use a compact state representation that cannot uniquely represent every single state. This means representable policies must behave identically in states with the same representation. This means we will most probably not be able to represent an optimal policy and have to settle on a "best" policy.

However, there may not be any policies that are "better" than all of the other representable policies - they will have higher values in some states and lower values in others so the partial order will not see any policy as optimal and will be difficult to find a "best" policy. This is not an issue for episodic tasks as sum of rewards is an objective function that can be used to compare two policies.

**Continuing control with function approximation is the problem setting that matters**

RL with tabular representations has done a good job of helping us build intuition and algorithms, but the real world is too large to be represented by tables of values. We need to build systems that generalize across similar states. Function approximation solves both of these problems.

Continuing tasks are the ones RL should be focusing on, along with tasks containing sparse rewards and credit assignment over long time horizons.

Function approximation is needed for large scale RL and as discounting is incompatible with functional approximation, then it is also incompatible with large scale RL.

**Average Reward**

The average reward of a policy is the reward the policy receives, on average, every time-step as it interacts with an environment. They define a *stationary distribution over states*, d_pi(s).

In continuing tasks, d(s) measures the faction of time the agent spends in state s. States visited more frequently have a higher value of d(s). If d(s) = 0, this means that s is a *transient state*, which is only visited a finite number of times and never again.

The average reward of a policy is the average one-step reward, weighted by the stationary distribution.

\bar{r}(\pi) = \sum_{s \in S} d_\pi(s)r_\pi(s)

Thus the average reward for a policy is the sum of the reward for each state multiplied by how often you were in that state.

**An Optimization Objective for Continuing Tasks**

We want an objective for continuing tasks similar to sum of rewards in episodic tasks. We could then use this to compare policies with each other to easily find the best policy. 

The key principle is that the agent maximizes the rewards it receives over its lifetime. We could calculate using their discounted value from the start state, but this gives importance to the states encounted early in the agent's lifetime. The start state may never be visited ever again.

We want to weight rewards independently of when they are receives but must weight them on where they are received, i.e. which state. The policy must have the form \sum_{s,a} w(s,a)r(s,a), with w(s, a) that weights each reward. For policy optimization to matter, w(s, a) must depend on the policy, pi. Thus, we can say w(s, a) = d_\pi(s) \pi(s, a). This weights each state-action pair by how frequently it is visited. Thus, to find the best policy, we find and policy that maximizes:

\bar{r}(\pi) = \sum_{s \in S} d_\pi(s)r_\pi(s)

This is just the average reward. It captures the core concept of reinforcement learning - the agent's actions should cause it to visit states where it can earn large rewards. In continuing tasks, the agent should find policies that cause it to frequently visit these highly rewarding states.