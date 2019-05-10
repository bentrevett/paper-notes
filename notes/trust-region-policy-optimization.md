# Trust Region Policy Optimization (2015)

John Schulman, Sergey Levine, Philipp Moritz, Michael I. Jordan, Pieter Abbeel

---

Trust region policy optimization (TRPO) is a procedure for optimizing policies in reinforcement learning designed to be effective for parameterized non-linear policies with large amount of parameters, such as neural networks. 

You can classify policy optimization into three main groups:

1. policy iteration methods - alternate between estimating a value function under the current polic and then improving the policy
1. policy gradient methods - use the gradient from the expected return over sample trajectories
1. derivative-free optimization methods - treat the return as a black box function which is to be optimized via the policy parameters

The gradient free methods consistently beat gradient based methods (however gradient free methods scale poorly with the number of parameters), which sucks because gradient based methods have better sample efficiency. Gradient 

The TRPO algorithm is a policy gradient optimization algorithm that optimizes a surrogate objective and guarantees policy improvement, i.e. the policy can only get better, never worse.

First, they describe the Markov decision process (MDP) which is a tuple of (S, A, P, r, p, y) - that is: states, actions, transition (aka dynamics) function, reward function, distribution over initial states and discount factor. They represent the expected return (expected discounted reward) by n(pi). They also have the state (V) and state-action (Q) value functions, as well as the advantage function, A. Q is expected return when taking action a in state s and then following the policy thereafter. V is expected return when being in state s and following the policy thereafter. A = Q(s,a) - V(s). 

You can express the expected return of one policy (pi') over another (pi) in terms of advantage over than policy accumulated over time-steps. This is given by: n(pi') = n(pi) + expected advantage calculated from pi using a trajectories from pi'. Another bit of notation they introduce is using p_pi be the discounted visitation frequencies, i.e. p(s) = 1 means the policy visited that state once (without discounts). They do this so they can rewrite n(pi') in terms of state visits instead of over time-steps, this gives us equation 2. Equation 2 is: n(pi) + the advantage of an action (using pi) multiplied by the probability of taking that action (using pi'), for all actions, over all states, multiplied by their discounted state visitation counts (using pi'). This equation shows that if your expected advantage at every state is non-negative, then pi' is guaranteed to be a better (in terms of expected return) policy than pi. So if we could always find a pi' policy then we'd always be improving our policy!

One issue with this is that finding the state visitation counts for every single possible new policy, pi', is infeasible. So, they approximate n(pi') with L_pi(pi') which is the same as n(pi') but uses the state visitation counts from pi, instead of pi'. However, if using a parameterized function for your policy, which we do with neural networks, then L = n.

Now they've found how we can always improve our policy, but we want to be able to reach an optimal policy in a reasonable amount of time. Too small of a step when improving our policy means it will take forever to train a satisfactory agent. Also, too large of a step might cause the policy to be too different from our old one, causing L =/= n, and thus the theory doesn't hold. One way to ensure decently sized steps is to calculate a lower bound on the policy improvement, i.e. the smallest amount it can change while keeping to the theory(?). Earlier work used a penalty on expectations, however TRPO uses a constaint on the KL divergence (a distance metric between two distributions). That is, the new policy must be within a certain KL divergence value of the old policy. The KL divergence metric they used is expected KL divergence over the distribution of actions for all states.

The full TRPO equation is given in equation 14.

---

- https://medium.com/@jonathan_hui/rl-trust-region-policy-optimization-trpo-explained-a6ee04eeeee9
- https://medium.com/@jonathan_hui/rl-trust-region-policy-optimization-trpo-part-2-f51e3b2e373a
- https://spinningup.openai.com/en/latest/algorithms/trpo.html
- http://www.cs.toronto.edu/~tingwuwang/trpo.pdf
- https://www.depthfirstlearning.com/2018/TRPO
- https://github.com/ikostrikov/pytorch-trpo
- https://github.com/mjacar/pytorch-trpo
- https://github.com/Khrylx/PyTorch-RL