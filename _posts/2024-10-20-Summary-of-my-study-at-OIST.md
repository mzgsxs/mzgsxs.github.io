## Summary of my study at OIST
[Unfinished OIST thesis](https://drive.google.com/file/d/1jariPMU7iGL-CjnGvYbMNb_OPeQ67wKm/view?usp=sharing)


#### Inference systems and category theory
During my graudate study, I was studying the theory of the mind at first. Mostly by investigating the duality of formal system and probablistic system. More specifically, boolean algebra with statement evaluation (to bool true or false) is dual to probablistic system with probablistic measure(to real numbers between 0 and 1), this correspondence can be formalised via category theory. Causually speaking, seeing probablistic inference as a "soft" or "relaxed" version of the formal logic. We then can use category theory to study the relation/morphism between them and amomng the systems in the category of inference systems. Neural network is well known for it's probablistic or statistical modeling characteristic (for example, early single neuron models is assembled by logistic regession or attractor network and ergodic systems) what is the link between neural networks and inference systems? Much like the halting problem, we ask the problem of determing from a description of a inference system, weather it can "construct" a new inference system that is a "superset" or "more capable" of the first. More concretly, a not so good example could be a LLM that takes a list of string blobs, we ask it to give us a new LLM that is more "capable" than itself (my study had nothing to do with LLM, I'm just using it as a example). It is a inference system in the sense the statement evaluator is now a probability meausre over a ordered marginal distribution of N joint variables, which each variable is a word. In addition, it's different from training a LLM from another LLM, our question is more related to artificial creativity. Of course, the definitions of those words such as "construct", "more capable" needs further study. I'm sure people have studied it before, but since my main interest is neural interface and with my limited mathematical knowledge and pandemic, I've decided to give up, but I do hope more capable individuals can continue this investigation.

#### Neuromodulation and metalearning
The second part of my study was on neuromodulatory system in the brain, mostly the theory and neuromechanism of a.) exploration and exploitation problem or sometimes refered to as the bayesian RL and b.) model-based and model-free in the brain. 

[First and second Part](https://docs.google.com/presentation/d/1jPPnCfTKtLa-FB4haYS3q-Nw1tRfzGPv/edit?usp=sharing&ouid=112627724459090659537&rtpof=true&sd=true)


#### Neuroevolution
The last part of my study was on neuro-evolution. The basic idea is to evolve network archtecture from autoencoder-like genetic algorithms (inspired by Variational Graph Auto-Encoders Kipf & Wellings 2016), a grow model (decoder) is used to generate network size and connectivity based on the genetic code and an encoder is used to train the expressiveness of the grow model. The motivation is clear, as back propagation is not a global optimization method and it can not prune or grow the network. Altough there are many network pruning&growing algorithms exist, we want to add noise in the infomation compact space and hope it can leads to faster evolution/optimization. Inaddion, we can use existing brain-connectom to train the grow model to model the neurodevelopmental process. Furthermore, the neurodevelopmental process is a physcial process and follows physics, which may be easier to learn than a dynamic enviroment. There are many existing work related to this topic such as compositional pattern-producing network (CPPN) and neuroevolution of augmenting topologies (NEAT).

[Third Part](https://docs.google.com/presentation/d/1sCNji51QUQItBJBHxFXrNFq6mAAnAGI1/edit?usp=sharing&ouid=112627724459090659537&rtpof=true&sd=true)


My opinion is intelligence is meaningless without the environment, just like words are meaningless without a context, intelligence is a signifier that can only be signified in an enviroment.   

