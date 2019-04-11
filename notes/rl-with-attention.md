# Reinforcement Learning with Attention that Works: A Self-Supervised Approach (2019)

Anthony Manchin, Ehsan Abbasnejad, and Anton van den Hengel

---

Usually in DRL models applied to Atari 2600 games, they use a convolutional neural network (CNN) to model the state. Most DRL research focuses on policy optimization and use a standard CNN architecture, whereas here they are focusing on the state encoding via the CNN.

Attention has already proven itself in vision, natural language processing and generative adversarial networks, so why shouldn't it work in reinforcement learning (RL)?

People have tried to incorporate attention in deep reinforcement learning (DRL) models in the past and it hasn't worked. They propose a model that can learn where to focus attention by itself, whereas previous attempts have used hand-written rules for where to focus attenton. As RL follows the Markovian assumption (the next state is only dependant on the current state and none of the previous states) attention in RL should also follow this assumption.

They start with *self-attention*  which calculates attention by relating each element in the input to all other elements in the input, this is from the Transformer model. They state that this model achieves state-of-the-art results whilst also providing a visualization of where the policy focuses, showing it cares about temporal reasoning. Usually, temporal knowledge is provided by stacking multiple Atari frames on top of each other, or via an recurrent neural network (RNN).

The previous attempts are using attention with RL either used a CNN/RNN combination, segment in the input and feed each to an attention layer, adding human gaze information as a hand-crafted feature, and a few more. As for visualizing policies, apparently previous work which showed attention over the non-processed Atari frames which isn't useful for understanding how policies make decisions (but does look nice). Apparently they didn't pay attention to the "right" things either.

They propose six different variations of self-attention:

- Self-Attending Network (SAN): self-attention between the first two convolutional layers
- Strong Self-Attenting Network (SSAN): multiplying the output of the above self-attention layer by 2 in order to increase the influence of attention on the network
- Self-Attending Double Network (SADN): self-attention between the first two and the next two convolutional layers
- Strong Self-Attending Double Network (SADN): multiplying the output of above by 2
- Pure Self-Attending Network (PSAN): using only the output of the self-attention between the first two convolution layers and not applying it to a convolutional layer
- Pure Self-Attending Double Network (PSADN): same as above but between the first two and next two convolutional layers, still have the final convolutional layer

They use Proximal Policy Optimization (PPO) to train their model as it is both faster and better. They process the Atari frame to 84x84 greyscale and use a random amount of NOOPs on environment reset. They train three times across ten environments, training each for 40 million time-steps, which is 160 million frames. Performance is the max score achieved, averaged over all 3 seeds.

To view attention, they average pool over the gradients with respect to the chosen action and then pass it through a ReLU layer? Do not really understand this part.

They achieve state-of-the-art results for Demon Attack. Actually of their methods beat PPO on Demon Attack except PSADN. SAN beats all policy gradient methods on Demon Attack, MsPacman, Bowling, Freeway and Frostbite. None of their methods beat PPO on Tutankham, Atlantis, Bowling or Breakout. All the rest are quite close. The performance of their different algorithms seems to vary wildly. Ones will be better at some games than others.

Looking at the visualization, they see how the agent is able to track and attend to multiple targets. Also, if there is part of the screen where enemies occasionally appear from, it will pay attention to this in preparation for an enemy to appear. Apparently there is a "bug" in MsPacman, where even when using the maximum value per pixel of the last four frames the enemies will sometimes not be visible in a state as they flicker on/off and thus the agent is unable to track them at all times. It gives rise to one reason by RL agents haven't beaten human performance in MsPacman yet. Why has nobody found this bug before?