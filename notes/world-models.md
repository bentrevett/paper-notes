# World Models (2018)

David Ha, Jurgen Schmidhuber

---

Authors present work on World Models. World models are trained in an unsupervised manner to learn a compressed spatial and temporal representation of the environment. The output of this world model can then be fed to a controller, which will take actions to maximize the expected cumulative reward. The controller has as little parameters as possible, letting all the complexity be within the world model.

The world model is made up of a variational autoencoder (VAE) and a Mixture Density Network RNN (MDN-RNN). The aim of the VAE is to learn an abstract, compressed representation of each observed input frame. The objective of the MDN-RNN is to compress information about what happens over time. The VAE is trained as usual, via the reconstruction loss of the input and reconstructed image. The MDN-RNN is trained to predict the next VAE latent vector from the previous latent vector, previous action and previous hidden state. Thus, the MDN-RNN's will encode temporal information. The MDN-RNN also has a temperature parameter, used to control model uncertainty.

The controller is a single layer neural network that takes in the concatenation of the MDN-RNN's predicted latent space and it's hidden state. As the controller has a relatively small amount of parameters it is trained via evolutionary strategies, specifically the CMA-ES algorithm.

Each section of the overall model is trained individually on the CarRacing environment. The procedure is:

1. Collect N rollouts from a random policy
1. Train the VAE to encode frames into a latent representation
1. Train the MDN-RNN to produce the predicted next latent representation
1. Use CMAE-ES to solve controller parameters by maximizing expected cumulative reward

They find that without the temporal information from the MDN-RNN the model does OK, but not as good. This is due to it not having temporal information. However, other work has found that you can use a randomly initialized, i.e. untrained, MDN-RNN which does just as well.

One interesting aspect is that you can use the decoder of the VAE to decode these predicted latent states to see what the model is hallucinating/dreaming. They take this a step further by training the model with its own dreams in the VizDoom TakeCover environment.

Now, instead of getting a new observation from the environment at every time-step, they feed the predicted latent space back into the MDN-RNN (with the action and hidden state too, as before). The MDN-RNN also produces a scalar to tell if the episode has finished, as they won't be getting that signal from the environment.

They find that a controller trained only in hallucinations is able to generalize well to the real environment. By increasing the temperature parameter on the MDN-RNN they can make the hallucinated environment more difficult and find that this causes the controller to do better on the real environment up to a point. If the hallucinated environment is too difficult then the controller fails to ever learn anything useful for the real environment.

They have some discussions on the disadvantages of using a random policy to collect rollouts, such as the random policy not ever seeing some parts of the environment, causing the world model to be unable to represent them. They suggest solving this by training C until convergence, use your current parameters to get more rollouts to train your MDN-RNN, then training C until convergence again, and repeating. They didn't actually try this though.

Appendix has details of all parameters and model architectures used.

---

http://worldmodels.github.io
https://www.youtube.com/watch?v=HzA8LRqhujk
https://www.youtube.com/watch?v=dPsXxLyqpfs
https://github.com/ctallec/world-models