#Value Iteration Networks (2016)

Aviv Tamar, Yi Wu, Garrett Thomas, Sergey Levine, Pieter Abbeel

---

Value iteration networks (VINs) are neural networks with a planning module. They can learn to plan, and can be used for predicting outcomes that involve planning, such as reinforcement learning. They are based on a differential approximation to the value-iteration algorithm using a convolutional neural network (CNN).

Value iteration is based off the Markov decision process (MDP) formulation. The goal of the MDP is to find a policy that obtains maximum expected return. Using the value iteration algorithm, Vn (state value function at iteration n) converges to V* (optimal state value function) as n tends to infinity. 

The policy is just a function mapping observations of states into actions. The goal is to learn a policy which maximizes the expected return. 

The sequential nature of decision making in RL is inherently different than the one-step decisions in supervised learning, and usually requires some form of planning. However, most neural networks use similar architectures to those used in supervised learning, and are thus inherently *reactive*, lacking explicit *planning computation*. The success of reactive policies in sequential problems is due to the learning algorithm, which trains these policies to select actions that have good long-term consequences. 

There is a section talking about M' and M, which I don't really see the point of. From what I understand, the real MDP is M, and the MDP approximated by the neural network is M'. However, learning M' is a useful surrogate to give information about M. They even let M' have the state, action, reward and transitions as M, so I'm not sure why they made this distinction. They also talk about attention over the observation, I believe this is because V(s) is calculated from V(s'), so we only want to pay attention to possible next states? 

Figure 2 shows a layout of the architecture.

The VI module in the VIN uses the fact that each iteration of VI can be seen as passing the previous Vn and the reward function through a convolutional and max pooling layer. In channel in the convolution layer corresponds to a Q function for a specific action. Therefore, applying the convolutional layer K times is equal to K iterations of VI. 

They use a gridworld environment to show why planning is essential. The agent must navigate between two points while avoiding dark areas. The entire map is given to the agent, which contains the agent's location, the start and end points, and the obstacles to be avoided. They show that standard CNN algorithms trained on this task do not generalize well to unseen gridworld environments as they do not understand the "goal-directed nature of the behaviour". Their VIN models learn to map an observation to a planning computation relevant for the desired task, generating action predictions based on the resulting plan. This leads to policies that generalize better to new and unseen task instances.

They compare their VIN against a CNN network and a fully convolutional network (FCN) and find that the CNN and FCN performances drop off on large (28x28) gridworlds. 

---

- https://github.com/avivt/VIN
- https://github.com/zuoxingdong/VIN_PyTorch_Visdom
- https://github.com/dmellop/PyTorch-value-iteration-networks
- https://github.com/kentsommer/pytorch-value-iteration-networks
- https://github.com/sufengniu/GVIN