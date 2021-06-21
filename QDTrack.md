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

- 