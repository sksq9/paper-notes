## [World Models](https://worldmodels.github.io/)

TLDR; The authors trains a novel model based Reinforcement Learning agent. They use a VAE (V) model to encode state represenation, a MDN-RNN (M) model to predict the future and an CMA-ES (C) controller to take action in the environment. The three models are trained **independently**, without feedback from another. The VAE learns to encode 2D pixel representation of the game pixels into a latent vector _z_. This latent vector, along with corresponding action, is used to train the RNN, whose primary job is to predict the future state of the game based on it's hidden representation _h_. Finally, both the representation of current spatial representaion, _z_ and temporal representaion, _h_, are used by C, to take action in the environment.

### Key Points

#### Data Generation

- Random rollouts
	- also iterative training procedure, if required by complex envs
- Stored observation and corresponding action

#### VAE (V)

- VAE is essentailly unsupervided way to learn your input domain
- CNN VAE uses environment observation frame as input
- Output is compressed latent representation _z_, which stores spatial representation of the current time

<img src="images/world-models/vae.png?raw=true" width="512">

#### MDN-RNN (M)

- Use previous action and _z_, to predict the future _z_
	- action is required because only current _z_ might not be sufficient to predict the future
- Mixture Density Network (MDN) LSTM
	- mixture of gaussians output from RNN
	- new _z_ is sampled for the gaussian
	- since prior of VAE is gaussian too, therefore MDN be useful
- RNN represents temporal representations
	- an alternative to stacking past frames, as in other RL menthods

<img src="images/world-models/rnn.png?raw=true" width="512">

#### Contoller C

- A very compact C is trained
	- a single layer neural network
	- only 867 papemeters
- Uses both _z_ from V and _h_ from M to predict actions
- The hardwork of understanding the environment is already done by V and M
	- only needs to predict the action
	- _seperation of duties to different components_


#### Experiments

- 10,000 random rollouts
- VAE 
	- 1 epochs
	- 32 _z_ dim 
- RNN
	- 20 epochs
	- 256 hidden units


### Thoughts


- RNN learn temporal information, should be used in other RL too.
- Independent training of all 3
	- therefore it's PO-MDP
	- therefore Markov property do not hold, since the agent (C), sees only the compressed representation
- Independent training might not be the best way to move forward in RL
	- see discussion [here](https://coherencedrivers.wordpress.com/2018/04/04/comment-on-world-models/) and [here](https://twitter.com/shimon8282/status/979344417961250817)
- Random exploration might not be able to collect good observation for complex games.
- The authors provide _z_, the current representation, and _h_, the hidden state which predicts _z_
	- so inturn, isn't both _z_ and _h_ stores the same information?
	- see discussion [here](https://github.com/worldmodels/worldmodels.github.io/issues/2), along with lots of other ideas by the author

