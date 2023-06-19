---
layout:     post                    # 使用的布局（不需要改）
title:      用StableDiffuision自制万智牌卡面
subtitle:   自己应用一下.
date:       2023-05-18            # 时间
author:     tianchen                      # 作者
header-img: img/diffusion/dnd-46.png  #这篇文章标题背景图片  
catalog: true                       # 是否归档
tags:                               #标签
     - survey
---

# 缘起

参加完毕业典礼心血来潮，组合之前尝试过的一些AIGC工具给自己DIY了一张万智牌面：
牌面模板为由 https://github.com/MrTeferi/MTG-Autoproxy 开源的PSD模板
卡面由生成模型Txt2Img实现（with ControlNet Supervison）：
    - 基础模型为开源社区中StableDiffusionV1.5微调的模型（GhostMix: https://civitai.com/models/36520/ghostmix）
    - 进行了自己照片的LORA训练参考了(kohya trainer: https://github.com/Linaqruf/kohya-trainer）


# 步骤


### PS模板

> [github:MTG-AutoProxy](https://github.com/MrTeferi/MTG-Autoproxy)提供了自动化脚本以及手动的PSD模板和相关素材。

- 安装以下字体：（clone项目之后在font文件夹中）
    - ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20230618200039.png)
- 从readme中的google drive链接中下载模板(还挺大的，每个几百M，放在了`~/project/MTG-AutoProxy/templates`)：
    - ikoria.psd: 生物卡
    - pw-tf-front-extended.psd 鹏洛克的模板
- 开了一个新的文件夹，存放候选图片以及psd项目文件等`~/project/MTG-AutoProxy/my_demo`

### 生成卡面插画

> 采用了之前训练好的基于StableDiffusion的LORA模型

- Base Model: [GhostMix](https://civitai.com/models/36520/ghostmix)
    - ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20230618200834.png)

- Lora Model: 参考了[kohya trainer](https://github.com/Linaqruf/kohya-trainer）
    - 用大致30张自己的照片，训练了一个自己的人脸LORA

- Prompts:
    - Positive: ```best quality, dark background, galaxy, an asian man, wizard, graduation, blue bachelor gown, yellow ribbon, magic the gathering, wizard of the coast <lora:my_lora:0.7> ```
    - Negative: ```(low quality, bad hands, worst quailty: 1.3). (lowres), (EasyNegative), (high contrast)```

- Solver:
    - DPM++ 2S Karras, 20 steps, **restore face**（很关键）
    - **CFG_Scale**: ```3.5```
    - ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20230618201012.png)


- ControlNet：用了大台阶上的毕业照，进行了openpose的姿势约束
    - control mode: balanced
    - control weight: 0.8

- UpScaler：
    - click "send to extras"
    - ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20230618202330.png)

---

- 类似的直接采用img2img做监督，也可以获得不错的效果：
    - ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20230618202152.png)


### 法术力符号

> 从[这里](https://www.slightlymagic.net/forum/viewtopic.php?t=4430)下载了一个manas.svg文件，在里面选取拼接出了Mana Cost

![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20230618162951.png)
![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20230618162909.png)


### 卡面信息

> 抄了Urza旅法师版本的卡面说明（后来被告知，0-0的生物在场上站不住。根本拿不到学历，笑死）

- When Terence, Starfield Seeker enters the battlefield, create a 0/0 colorless, indestructable Construct artifact creature token named "Degree:VOID".

- Create a 1/1 Inkling creature token, put a +1/+1 counter on "Degree".

- Transform "Degree:VOID" into "Degree:Bachelor", put Saga "Master Program" onto the battlefield.

- Transform "Degree:Bachelor" into "Degree:Master", put Saga "Phd Program" onto the battlefield.

### Finalize

![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20230618195641.png)