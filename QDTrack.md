# Quasi-Dense Similarity Learning for Multiple Object Tracking

- Paper - https://arxiv.org/abs/2006.06664
- Official Repo - https://github.com/SysCV/qdtrack
- Paper with Codes Page - https://paperswithcode.com/paper/quasi-dense-instance-similarity-learning
- MOT Dataset - https://motchallenge.net/
- BDD100K Dataset - https://bair.berkeley.edu/blog/2018/05/30/bdd/
- Waymo Dataset - https://waymo.com/open/
- TAO Dataset - https://taodataset.org/

## Abstract 

- Similarity Learning: It is closely related to regression and classification, but the goal is to learn a similarity function that measures how similar or related two objects are.

- Currently models are using Sparse Ground Truth Matching as their training objective which ignores a lot of info. regions in the image

- Aim is to involve similarity learning

- Quasi-Dense Similarity Learning densely samples hundreds of region proposals for contrastive learning which aims to learn representations such that similar samples stay close to each other, while dissimilar ones are far apart.

- Observations made include that at inference time the feature space admits a simple nearest neighbout search

- It outperforms all existing methods on MOT, BDD100K, Waymo and TAO tracking benchmarks

## 1. Introduction

- MOT is a fundamental problem as is evident by rising self driving cars etc.

- Older MOT methods used tracking-by-detection where they detect objects frame by frame and associate them to a class based on instance similarity.

- Newer works have shown we can use spatial proximity between objects in consecutive frames (using IoUs or center distances) if objects are accurately detected in the first place which can help us associate objects.

- However problems arise when the object is occluded or crowded, to curb this methods like motion estimation where we determine motion vectors by looking at adjacent frames and displacement regression.

- We constrain our search regions to be local neighbourhoods to avoid distractions due to surrounding images.

- We also use object appearence similarity as a secondary result to restrenghten our beliefs about particular associations or track in case some object vanishes.

- So why are humans able to indentify identical objects immediately while computers struggle? Well the paper **conjectures** that this might be because while training image and object info. might not be fully used.

--- 

### Figure 1

- Re-identification(reID) is the process of associating images or videos of the same person taken from different angles and cameras

- All 4 approaches indicate different ways used. The first 3 regard using similarity learning as a later activity or use sparse ground truth bounding boxes as training samples.

---

- In the real world it's highly unlikely that you'll find indentical looking objects in an image so the model if trained properly should be able to distinguish objects which are similar and have or lack some features from older frames.

- A trick employed is using many bounding boxes, the ones closer to the actual bounding box give positive examples while the ones far apart act as negative samples.

- The paper employs quasi dense contrastive learning wherein we take an image, split it into 100s of region of interests and match those between a pair of images. Using this it learns a metric with contrastive loss which essentially means it uses the positive examples feeds it into the network, gets an output, calculates its *distance* from an object of the same class and then contrasts this from a negative example. The exact mathematical formula is as follows - 
![](https://miro.medium.com/max/2700/1*4dQo-60fwy1Cki4nKjeGuQ.png)

- These quasi dense samples are able to cover most info. regions giving more box examples and hard negatives.

- One simple approach was to use a handful of ground truth labels. However the paper uses another approach which enhances similarity learning. Since each sample has many positive samples on the original image we can extend out contrastive learning process to account for those multiple forms which makes quasi-dense learning feasible. So each sample is trained to distinguish all proposals on the other image simultaneously.

- Apart from looking for similarity MOT needs to look for false positives, id switches, new appeared objects and terminated tracks.

---
### How to Tackle Missing Targets - 

- We include backdrops
- We also include the unmatched objects which remain in the last frame to match them
- We also use a bidirectional softmax to ensure bidirectional consistency.

### How to Tackle Multiple Targets - 

- We use duplicate removal to remove multiple targets and filter out matching candidates

---

- Quasi-dense contrastive learning can be used on top of pre-existing detectors such as R-CNNs or YOLO.

- The paper uses it on top of Faster R-CNN while applying a light weight embedding extractor and residual connections in the network.

---
### Benchmarks Attained by the Model - 

- 68.7 MOTA on MOT17 at 20.3 FPS with no external data
- 10 points MOTA increase and significantly reduces the number of ID switches on BDD100K and Waymo datasets

## 2. Related Work

- Recent focus in MOT is on the tracking-by-detection paradigm. The given approaches present different methods to estimate the instance similarity between detected objects and previous tracks, then associate objects as a bipartite matching problem.

### Location and Motion in MOT

- In crowded scenes spatial proximity fails while it works in less crowded frames to associate objects between successive frames.
- Earlier models use to detect objects and track displacement separtely. Methods such as Kalman Filter which uses multiple measurements over time to produce estimates over time in data with noise, Optical Flow which is the distribution of apparent velocities of movement of brightness pattern in an image, and displacement regression.
- Detect & Track was the first work which jointly optimizes both the tasks of detection and tracking. It predicts object displacement in consecutive frames using the [Viterbi Algorithm](https://en.wikipedia.org/wiki/Viterbi_algorithm).
- Tracktor directly uses a detector as a tracker
- CenterTrack & Chained-Tracker predict the object displacements with pair-wise inputs to associate the objects.
- The above mentioned models are good but need to built further to be usable such as the need for an extra re-identification model which will make the frameworks more complex.

### Appearence Similarity in MOT

- Some models use an independent model or add an extra embedding head to the detector for end to end training to leverage the instance appearence similarity which strengthens tracking of the same object from one frame to the other while also giving us the power to reidentify vanished objects.
- Still the basic underlying similarity learning process is the same i.e. they use the cosine distance. So they use a n-classes classification problem where n represents the total number of classes in which we have to classify the objects or use the triplet loss function (visual below).
![Visual Triplet Loss](https://1.bp.blogspot.com/-2a4d-v0SkJo/XSHWs7H5a-I/AAAAAAAAEJ0/Qh4SNnksF9sPZ6L8wf0_ta3s7J05hI3VwCLcBGAs/s640/Screen%2BShot%2B2019-07-07%2Bat%2B1.24.36%2BPM.png)
- Since triplet loss only compares one image to two others it's hard to extend this to larger datasets. Hence MOT doesn't fully leverage Similarity Learning on the other hand QDTrack learns instance similarity from dense-connected contrastive pairs and associate objects present in the feature space with a simple nearest neighbour search (visual below). 

![Nearest Neighbour Search](https://rahvee.gitlab.io/comparison-nearest-neighbor-search/img/fig3.png)

### Constrastive Learning

- It is very successful in self-supervised representation learning which is a way of training a deep learning model with labels inherently obtained from the data itself.
- It is still not very used to get instance similarity in MOT.
- The paper essentially takes dense matched quasi-dense samples and supervises them with multiple positive contrastive learning.
- Unlike image-level constrastive methods out method allows for multiple positive training while in other methods there is only one positive target. H