---
published: false
title:  "[Paper Review] BEVFormer: Learning Bird's-Eye-View Representation from Multi-Camera Images via Spatiotemporal Transformers"
excerpt: "Li, Z., Wang, W., Li, H., Xie, E., Sima, C., Lu, T., ... & Dai, J. (2022). BEVFormer: Learning Bird's-Eye-View Representation from Multi-Camera Images via Spatiotemporal Transformers. arXiv preprint arXiv:2203.17270."

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

#### 3.1 Overall Architecture

BEVFormer에 사용된 6개의 encoder layer는 기존 transoformer의 encoder와는 BEV query, spatial cross-attention, temporal self-attention의 3가지 design이 추가되었다는 점이 다릅니다.

BEVFormer의 대략적인 파이프라인은,

1. T 시점에서, multi-camera image를 ResNet-101+FPN(1/16, 1/32, 1/64)로 feed, 6개의 feature를 얻음. <br>
2. 각 encoder에서, BEV queries로 t-1 시점의 BEV feature Bt-1으로부터 temporal self-attention으로 temporal information을 쿼리 <br>
3. BEV queries로 multi-camera feature Ft로부터 spatial information을 Spatial Cross-Attention으로 찾음. <br>
4. 이 과정으로 refined BEV features를 만들고, 다음 encoder layer의 input으로 사용함. (encoder layer 수 : 6) <br>
5. 모든 encoder layer를 통화하면, BEV feature Bt가 만들어 지고, 3D detection과 map segmentation heat로 3D Bbox와 semantic map을 predict. <br>
