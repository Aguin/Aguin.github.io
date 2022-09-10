---
title: "MEgATrack: Monochrome Egocentric Articulated Hand-Tracking for Virtual Reality"
date: 2022-09-09
categories:
  - Paper Reading
tags:
  - MEgATrack
classes: wide
---

[Meta Blog & PDF](https://research.facebook.com/publications/megatrack-monochrome-egocentric-articulated-hand-tracking-for-virtual-reality/){: .btn .btn--primary}

[知乎的一篇文章](https://zhuanlan.zhihu.com/p/336611326)把本文的前半部分介绍比较透彻了, 总结来说几点:
- camera configuration: 四个640x480鱼眼灰度相机.
- pipeline: DetNet + KeyNet
- DetNet: 出center + radius + cls
  - 推理时有两个tricks: 连续帧外插 + 每次只推一个相机(round-robin)
- KeyNet: 输入手部ROI与前一帧的keypoint信息, 输出2D position heatmap + 1D rel-distance heatmap
  - 这里适配鱼眼相机使用distance而不是depth, rel-distance和hand scale相关$d^{rel}_{i} = \frac{d_i - \bar{d}}{\phi}$
- pose estimation: 实际的关键点优化$E_{2d}$, $E_{dist}$和$E_{temporal}$三项的加权和.
- hand scale: 利用stereo信息优化共享的scale参数$\min_{\theta_t, \phi} \sum_{t = 1}^{n}E_{2D}(\theta_t, \phi)$

但是更加重要的后半部分似乎鸽了, 这里自行补充一下.
- 数据采集用6个鱼眼+1个深度相机, 过程中保证手部在深度相机中是可见的, 其他相机可以遮挡.
- 标注第一帧, 再通过keypoint + tracking得到序列, 结合时序做后处理 + 人工筛选.
- DetNet所需bbox标注需要对齐camera configuration, 仍然用结合tracking的方式, 人工标每个连续段的第一帧.