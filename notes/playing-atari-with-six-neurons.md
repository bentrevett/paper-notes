# Playing Atari with Six Neurons (2019)

Giuseppe Cuccu, Julian Togelius, Philippe Cudre-Mauroux

---

Deep reinforcement learning (DRL) applied to vision-based problems, like playing Atari games, maps pixels to actions. Internally, the deep neural network (DNN) has to both extract useful information from the image and select actions from it. If you could separate the two - information extraction and action selection - you could understand the complexity of each task (by the size of the network required for it?) as well as find policy representations that are smaller/interpretable/more general.

They propose a method of learning policies and state representations separately but also simultaneously for policy approximation. They use two novel algorithms for state representation. The first is called **Increasing Dictionary Vector Quantization**, which makes  the state encoder capable of growing its dictionary size over time to address new observations as they appear. The second is called **Direct Residuals Sparse Coding** and encoders observations by disregarding reconstruction error minimization and instead aims for highest information exclusion. The encoder selects which observations to train on, which maximizes code sparsity (what does that mean?). As the dictionary size increases, the encoder produces large inputs for the neural network. They address this problem by using a variant of the **Exponential Natural Evolution Strategies** algorithm, which adapts its probability distribution dimensionality along the run.

They test their system on Atari games using neural networks with only 6-18 neurons (depending on the game's controls) and achieve comparable - and occasionally superior - to state-of-the-art techniques.

---

https://www.reddit.com/r/MachineLearning/comments/8p1o8d/r_playing_atari_with_six_neurons/
https://github.com/giuse/DNE
https://news.ycombinator.com/item?id=17250315