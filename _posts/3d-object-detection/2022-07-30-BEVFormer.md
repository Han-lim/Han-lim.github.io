---
published: True
title:  "[Paper Review] BEVFormer: Learning Bird's-Eye-View Representation from Multi-Camera Images via Spatiotemporal Transformers"
excerpt: "Li, Z., Wang, W., Li, H., Xie, E., Sima, C., Lu, T., ... & Dai, J. (2022). BEVFormer: Learning Bird's-Eye-View Representation from Multi-Camera Images via Spatiotemporal Transformers. arXiv preprint arXiv:2203.17270."

header:
  overlay_image: ../../assets/images/posts/3DOD/2022-07-30-BEVFormer/3DOD_bevformer_fig1.JPG
  overlay_filter: 0.5
  date: 2022-07-30


categories:
  - 3d_object_detection
tags:
  - [3D_Object_Detection, Computer_Vision, Deep_Learning, Paper_Review]

toc: true
toc_sticky: true
use_math: true
 
date: 2022-07-30
last_modified_at: 2022-07-31
---

논문 정보: Li, Z., Wang, W., Li, H., Xie, E., Sima, C., Lu, T., ... & Dai, J. (2022). BEVFormer: Learning Bird's-Eye-View Representation from Multi-Camera Images via Spatiotemporal Transformers. arXiv preprint arXiv:2203.17270.  

---

이번에 리뷰할 BEVFormer 라는 논문은 2022년도 ECCV에서 발표된 논문입니다. <br>


<center> <img src="../../assets/images/posts/3DOD/2022-07-30-BEVFormer/3DOD_bevformer_fig1.JPG" width="700" alt="{{ include.description }}">
  </center> 

BEVFormer의 key design은 다음과 같습니다. <br>
1) Grid-shaped BEV queries to fuse spatial and temporal features via attention mechanisms flexibly <br>
2) Spatial cross-attention module to aggregate the spatial features from multi-camera images <br>
3) Temporal self-attention module to extract temporal information from history BEV features, which benefits the velocity estimation of moving objects and the detection of heavily occluded objects. <br>

----

### BEVFormer

<center> <img src="../../assets/images/posts/3DOD/2022-07-30-BEVFormer/3DOD_bevformer_fig2.JPG" width="700" alt="{{ include.description }}">
  </center>

----

#### 3.1 Overall Architecture

BEVFormer에 사용된 6개의 encoder layer는 기존 transoformer의 encoder와는 BEV query, spatial cross-attention, temporal self-attention의 3가지 design이 추가되었다는 점이 다릅니다.

BEVFormer의 대략적인 파이프라인은,

1. T 시점에서, multi-camera image를 ResNet-101+FPN(1/16, 1/32, 1/64)로 feed, 6개의 feature를 얻음. <br>
2. 각 encoder에서, BEV queries로 t-1 시점의 BEV feature Bt-1으로부터 temporal self-attention으로 temporal information을 쿼리 <br>
3. BEV queries로 multi-camera feature Ft로부터 spatial information을 Spatial Cross-Attention으로 찾음. <br>
4. 이 과정으로 refined BEV features를 만들고, 다음 encoder layer의 input으로 사용함. (encoder layer 수 : 6) <br>
5. 모든 encoder layer를 통화하면, BEV feature Bt가 만들어 지고, 3D detection과 map segmentation heat로 3D Bbox와 semantic map을 predict. <br>

<br>

#### 3.2 BEV Queries

BEVFormer에서 사용하는 query는 grid 형태이며 learnable parameters로 이루어진다. <br>

이 query를 BEV query라고 부르며, $ Q \in \mathbb{R} ^{H \times W \times C} $ 로 정의된다. <br>

이때, H, W 는 각각 BEV plane의 높이와 너비를 의미한다. <br>

또한, $ Q $ 상에서 $ p = (x,y) $ 에 위치한 쿼리 $ Q_{p} \in \mathbb{R} ^{1 \times C} $ 는 BEV plane 에서 위치 p에 해당하는 grid cell 영역을 의미한다. <br>

BEV plane 상에서 각 grid cell은 real-world size s meter 에 해당한다. <br>

BEV feature의 center는 ego car의 position 에 해당한다. <br>

해당 논문에서는 BEV query Q에 learnable positional embedding을 추가하여 BEVFormer의 input 으로 사용한다. <br>

<br>

#### 3.3 Spatial Cross-Attention

Multi camera view (6장)을 input으로 사용하기 때문에, vanilla multi-head attention을 모델로 사용하기엔 computational cost가 너무 높아서 해당 논문은 deformable attention을 기반으로 spatial cross-attention을 구현하였다. <br>

Deformable attention은 Query가 모든 2D feature 영역이 아닌 임의의 관심 영역과만 interaction 이 있도록 설계된 모듈이다. <br>

이 모듈에 대해서는 따로 포스팅하도록 하겠다. <br>

Deformable attention은 2D perception task에 맞게 설계 되었다 보니, 3D scenes에 적용하기 위해 약간의 수정을 했다. <br>

수정된 부분이 아래와 같다. <br>

<br>
다시 spatial cross-attention 설명으로 넘어가자면,  

BEV place 상의 각 query들을 pillar-like query로 옮긴다.  

그 다음, 그 pillar에서 $ N_{ref} $ 개의 3D reference points를 뽑아 이 points 들을 2D view들에 project 한다.  

하나의 BEV query에서 뽑은 projected 2D points는 일부 view들에만 project가 된 형태가 된다.  

이 때, hit views를 $ V_{hit} $ 이라고 표현한다. 

그 다음, 이 2D points들을 query $ Q_{p} $ 에서 reference points 로 두고, hit views $ V_{hit} $ 에서 이 reference points 들을 중심으로 feature 를 뽑는다.  

마지막으로, 뽑은 feature 들에 대해 weighted sum을 수행한 결과가 spatial cross-attention output 이 된다.  

이 과정을 수식으로 나타내면 다음과 같다.  

<center> <img src="../../assets/images/posts/3DOD/2022-07-30-BEVFormer/3DOD_bevformer_fig3.png" width="700" alt="{{ include.description }}">
  </center>  

$ i $ 는 camera view의 index, $ j $ 는 reference points의 index, $ N_{ref} $ 는 각 BEV query에서의 total reference points 를 나타낸다.  
