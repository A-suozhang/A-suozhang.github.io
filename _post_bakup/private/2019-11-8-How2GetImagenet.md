---
layout:     post                    # 使用的布局（不需要改）
title:      ImageNet预处理?!          # 标题 
subtitle:   留个档...   #副标题
date:       2019-11-8           # 时间
author:     tianchen                      # 作者
header-img:  img/bg-nmb5.jpg  #这篇文章标题背景图片  
catalog: true                       # 是否归档
tags:                               #标签
     - DL
---


> Copied from [FaceBookArchive](https://github.com/facebookarchive/fb.resnet.torch/edit/master/INSTALL.md)

# Download the ImageNet dataset
The ImageNet Large Scale Visual Recognition Challenge (ILSVRC) dataset has 1000 categories and 1.2 million images. The images do not need to be preprocessed or packaged in any database, but the validation images need to be moved into appropriate subfolders.

1. Download the images from http://image-net.org/download-images

2. Extract the training data:
  ```bash
  mkdir train && mv ILSVRC2012_img_train.tar train/ && cd train
  tar -xvf ILSVRC2012_img_train.tar && rm -f ILSVRC2012_img_train.tar
  find . -name "*.tar" | while read NAME ; do mkdir -p "${NAME%.tar}"; tar -xvf "${NAME}" -C "${NAME%.tar}"; rm -f "${NAME}"; done
  cd ..
  ```

3. Extract the validation data and move images to subfolders:
  ```bash
  mkdir val && mv ILSVRC2012_img_val.tar val/ && cd val && tar -xvf ILSVRC2012_img_val.tar
  wget -qO- https://raw.githubusercontent.com/soumith/imagenetloader.torch/master/valprep.sh | bash
  ```

## Download Torch ResNet
```bash
git clone https://github.com/facebook/fb.resnet.torch.git
cd fb.resnet.torch
```