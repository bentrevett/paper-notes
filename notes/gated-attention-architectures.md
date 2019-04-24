# Gated-Attention Architectures for Task-Oriented Language Grounding (2017)

Devendra Singh Chaplot, Kanthashree Mysore Sathyendra, Rama Kumar Pasumarthi, Dheeraj Rajagopal, Ruslan Salakhutdinov

---

"Task-oriented language grounding" means extracting semantic meaning from text and then mapping this to visual elements and actions. In their example they get a text instruction, e.g. "go to the green pillar", and the agent must look around for the green pillar then walk up to it using only the raw pixels as input. Their experiments are done in the Doom environment. Their work also extends to unseen instructions and maps, i.e. zero-shot learning.

To accomplish this, the agent has to know what "green" and "pillar" mean. It has to recognise objects, explore the environment ground each concept from text to visuals, do some reasoning (as instructions might be "go to the smallest red object") and then navigate to the correct object.

They propose an architecture that has a *state processing module* that jointly represents the text and images, and a *policy learner* that predicts the optimal action at each time-step. The state processing module uses a "gated-attention multi-modal fusion mechanism". They find that this works better than the standard procedure of concatenating a representation of each. They also have a level of interpretability, with the attention weights showing the agent has learned connections between objects mentioned in the text and objects visually seen by the agent.

The state processing module has a GRU for the text and a CNN for the image. They pass the final hidden state of the GRU through a linear layer with a sigmoid activation function. They call this an attention vector. The linear layer has an output dimension equal to the number of convolutional filters from the CNN. They then expand/copy each of the dimensions to match the height and width of the image after it has been passed through the CNN. They then elementwise multiply it with the CNN output. 

The intuition behind this is that the CNN will extract features from the images to detect color and shape of objects and the attention vector will learn to gate feature maps from the CNN conditioned on the input instruction.

They test the state processing module into both an imitation learning head that uses an oracle to determine the correct action, and a reinforcement learning head. The reinforcement learning head uses asynchronous advantage actor-critic (A3C) with entropy regulation and generalized advantage estimator (I don't know what either of these are). The A3C head uses a linear layer to create a hidden state which is then fed through an LSTM with the previous hidden state. The LSTM is used so the agent has some idea of previous states. The LSTM hidden state is then fed to a value function (1 neuron) and a policy function (3 neurons). 

Episodes terminate whenever the agent touches any object. The only available actions are: turn left, turn right and go forward. There is 1 correct object and 4 incorrect objects per environment. In "easy" mode, all objects are lined up in front of the agent. In "medium" mode the objects are all scattered about, some may obstruct others, but all are still in front of the agent. In "hard" mode they are scattered around randomly, with some objects potentially being behind the agent. Their metric is accuracy of agent reaching the correct object. If the agent doesn't reach any objects within the alloted amount of time-steps, this counts as reaching an incorrect object.

A difficulty is learning that sometimes "go to the red object" will mean a red keycard in one episode and a red pillar in another episode. Also, "go to the keycard", where the keycard will be green in one episode and blue in the next. There are 70 attribute/object combinations, they use 55 for training and 15 for testing. One of the 55 is chosen randomly each episode. They test on seen attribute/object combinations but on unseen maps and on unseen attribute/objective combinations on unseen maps.

They use a 3 layer CNN, with 64 filters in the final CNN layer. 

They found that the RL approach (A3C) does a lot better than all other approaches, and that their gated attention does better than concatenated representations in medium tasks (slightly) and hard tasks (significantly).

Figures 7, 8, 11 and 12 do a nice job of showing the attention vector values for different instructions. There is definitely some sort of learning about concepts going on.

---

- https://github.com/devendrachaplot/DeepRL-Grounding