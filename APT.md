---
Paper title: "Behavior From the Void: Unsupervised Active Pre-Training"
Authors: Hao Liu, Pieter Abbeel
date: 24-05-2022
tags: ["paper"]
---
similar papers: [[CIC.md]]
## Main Contribution
A new unsupervised RL algorithm that maximizes the entropy (estimated via a non-parametric estimator) of states in an abstract representation space, which allow it to scale to high dimensional observation spaces such as images, it achieve SOTA performance on 12 Atari games and in DMC environment in terms of data efficieny and asymptotic performance.

## Basic Methodogy
- The algorithm projects the recieved state to an abstract representation space, after that it estimate the state entropy in the abstract space and uses this estimate as an intrinsic reward and optimized with a deep RL algorithm.
- The representation space is learned with contrastive learning+data augmentation in a similar way to CURL and SimCLR papers.

![](apt.png =100x20)

## Algorithm and some implementation details
- The paper uses DrQ as its based deep RL algorithm.
- Its [[entropy esimator.md]] is based on KNN, the specific form they use is to average over all k and add constact c for numerical stability.
	$\mathcal{H}_{particle}(z) = \sum_{i=1}^{N} \log \Big (c + \frac{1}{k} \sum_{z_{i}^{(j)} \in N_{k}(z_i)} \Vert z_i - z_i^{(j)} \Vert \Big )$
	where n is the number of points, $N_{k}(\cdot)$ are the set of k nearest neighbors around the particle.
- The previous equation can be seed as an expectation and we can optimize for this expectation using any RL algorithm with the following reward.
$r(s) = \log \Big (c + \frac{1}{k} \sum_{s_{i}^{(j)} \in N_{k}(s_i)} \Vert s_i - s_i^{(j)} \Vert \Big )$
- In practice $s$ is encoded to the abstract representation space by the mapping $f_{\theta}(s)$.
- The representation space is learned using a similar approach to SimClr and CURL, a random batch of images are sampled from a replay buffer, for each image a two randomly selected data augmentation are applied, then using SimCLR method an abstract representation is learned where the two augmented images have a similar representation while being different from other images.
- The following picture shows the algoithm pseudo-code.
![](apt_alg.png =100x20)

## Results
- On DMC the pre-training was for 5M steps, while for atari it was 250M steps, the test phase in atari was for 100K env. steps or 400K frames (same setup as VISR)
- APT outperform SOTA RL methods on DMC (espically in sparse reward tasks), the baselines are DrQ from scratch, and count-based exploration.
- For atari it achieved super-human perfomance on 15 games compared to 12 games by the SOTA methds.
- Some insights from the ablation studies
	- Using KNN to estimate the entorpy is important and using randomly selected points greatly affected the performance.
	- Although Contrastive representation learning has the '[[uniformity on the hypersphere.md]]' property, without entropy maximization there is no effective exploration.
	- Using random projection as the state encoder affected the performance but it was still better than DrQ, which show that entropy maximization has some robustness properties.

## Insights and questions from the discussion section
- Does the difference between the extrinsic and intrinsic reward scales affect the downstream adaptations?
- Remeber that the reward is non-stationary.
- How to impove data-efficieny of the unsupervised phase (model-based RL?)?
- Deadling with cotastrophic forgetting, and thinking about other ways to use the pre-trained models. 