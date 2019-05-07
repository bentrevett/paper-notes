# Gated Path Planning Networks (2018)

Lisa Lee, Emilio Parisotto, Devendra Singh Chaplot, Eric Xing, Ruslan Salakhutdinov

---

Path planning modules are used by agents to navigate through an environment, with the goal of finding the shortest path between two points. The shortest path will be the one that takes the fewest amount of actions. These agents are always given the map of the environment, showing their location and the goal location.

The previous state of the art end-to-end differentiable path planning module is a *Value Iteration Network* (VIN). VINs are made up of recurrent convolutions and max-pools over the feature channels. VINs are similar to standard value iteration in RL for a 2D grid world, but do not need a model (of the environment?) or a reward function. VINs can compute near-optimal paths in 2D mazes and 3D landscapes, both where the transition model has to be learned.

 VINs suffer from several issues, such as training instability and random seed sensitivity. It turns out VINs are a weird form of recurrent-convolutional networks that use: max-pooling as an activation function, a hidden state dimension of 1, sparse weight matrices and a kernel length of 3 (always).

 This work proposes a more general recurrent-convolution architecture using standard recurrent gated updates (LSTMs) which aims to avoid the issues with VIN, which they call *Gated Path Planning Networks* (GPPNs). They describe it as "an LSTM which uses convolution of previous spatially-contiguous hidden states for its input".

 They show that GPPNs outperform VINs on: learning speed, hyperparameter sensitivity, sample efficiency and generalization. The improvements are consistent on different maze navigation types, maze sizes, and 3D environments where the agent only sees the first-person raw pixel images.

They use three maze navigation types: NEWS (moving only north/east/west/south), Moore (can move in any of the 8 neighbouring cells, i.e. can move diagonally) and Differential Drive (either forward one step or turn left/right by 90 degrees). The goal for NEWS and Moore is to just reach the goal state, for Differential Drive the agent must be in the goal state and the goal orientation.

They measure the percentage of optimal (shortest) paths found and the percentage of goals found.

They use 4x smaller hidden dimension for GPPN as it uses 4 gates instead of 1 in the VIN. They test with different kernel sizes (F) and iteration counts (K). A higher iteration count means that information can travel further (think value iteration in grid world). "The maximum path length information travels scales directly with iteration count". Another way to do long range planning is increasing the kernel size, as this means information travels further per iteration, thus requiring less iterations.

First they test on 2D mazes, all 15x15 unless noted. They find that VIN performance starts to degrade after F > 5, whereas GPPN seems to monotonically rise. They also find that, for the same F, GPPN performs better for all values of K, implying that it is superior at propagating information. They also find that VIN does comparable to GPPN on Differential Drive (with F < 9), but not for NEWS/Moore. GPPN trained on 10k mazes performs better than VIN with 100k mazes, showing it is much more sample efficient. They also show GPPN has much lower variance/standard deviation than VPPN over different random seeds and different hyperparameter settings. Looking at the learning curves, VINs performance wildly oscillates between epochs at higher values of F, whereas GPPN is pretty stable throughout. They measure how many epochs it takes to reach a certain optimal path percentage and find that depending on the desired percentage, VIN is 2-5x slower than GPPN. They also do a brief test on 28x28 mazes, showing that GPPN still performs better than VIN.

With the 3D mazes, as well as planning the path the networks also have a loss for predicting the map design (remember the input is the raw pixels). They get a new metric, accuracy of predicting the map design. They find that VIN is more prone to overfitting for all maze navigation types as accuracy is low. GPPN overfits on Differential Drive.

---

- https://github.com/lileee/gated-path-planning-networks
- https://www.youtube.com/watch?v=Pnnpl-Dr5lk