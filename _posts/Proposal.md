# Possible Application Field for Online Learning on Edge

> Training on Edge

### 1. Domain Adaptation Related
* Motivation: 当目标域的数据(不可得)与目前已有的数据差异大，需要在端上在线训练做Adaption
* Example: 
  * 自动驾驶中的感知环节，在白天黑夜，雨雾等不同环境中完成同一任务 

### 2. Semi-supervised Related
* Motivation: 当已有的Label的数据量相对较少，而终端设备能接受到大量的无标注数据，利用大量无标注数据在线训练提升模型性能
* Example: 
  * 一个终端摄像头，在端上进行推断的同时利用采集到的数据优化模型

### 3. Transfer Learning Related

#### 3.1 Task-Adaption (Multi-Task)
* Motivation: 从一个大的pretrain model开始，随着训练逐渐让模型更贴合某个具体任务同时精简模型(端上无Label，是否能通过弱或者是无监督的方式完成任务)，如果对于同一pre-train model可以做到Multitask的调整
* Example：
  * 多个视觉感知任务共享同一(部分)Backbone(比如车道线检测，目标检测)

#### 3.2 User-Adaption
* Motivation: 训练过程通过finetune让模型更加贴合用户的使用习惯和体验
* Example:
  * 用户的笔迹/语音识别
