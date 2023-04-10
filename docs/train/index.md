# 炼丹

这节对显卡的要求较高。

在法律上，**不应该** 无视画师约定的版权许可，采集作品数据进行训练。在道德上，** 不应该 ** 用训练结果贬低原画师作品价值，俗谚有
“吃水不忘挖井人”，训练数据并不能作为你的贡献。

目前个人认为效果认为效果最好的是 Lora，比 DreamBooth 体量小，又可拆卸，方便传播迁移。

```shell
fine tune = hn/TI/DreamArtist (APT)/DB/native training etc.
fine tune directly = DB/native training
```

## 如何选择

LoCon/Lora 是目前最好的 ` 概念 ` 训练方法，它训练少量的图片就可以很好地微调大模型的训练效果，但是训练时对显存要求较高（>
8GB）。如果你的显存不足，可以尝试租用云服务。

Textual Inversion 和 Hypernetwork 适用于 ` 整体靠近 `，前者教 AI 用模型中的标签组成一个 ` 人物 `，后者也是类似的。区别在于
Hypernetwork 以调节模型权重为手段，而 Textual Inversion 告诉 AI 特定标签应该如何组成。

而 DreamBooth 适用于 ` 细节 ` 的模仿，它的训练过程 “重新整改” 了模型，新模型之中含有了新的样本特征（加了新东西）， DreamBooth
技术本身用于 “复刻” ，所以可以认识冷门元素。

至于 Aesthetic Gradients ，也就是给 AI 认识一组 ` 优秀的数据 `
。结果就是这个东西会增加细节，训练很简单，但是会拖慢生成图片的速度（每次生成都要重新计算），并不适合应用，效果也不准确。

### 关于模型格式的解释

#TODO

## Tagger 工具

Tagger 是我们训练时使用的工具，它可以帮助你快速的为数据集打标签。 它的实质就是一个 VQA 模型，它可以根据图片来回答问题，比如
“这是什么？” “这是谁？” “这是什么颜色？” “这是什么动物？” 等等。

目前为图片生成 Prompt 的方案有 WD taggers, deepdanbooru 还有 blip+clip，根据数据集来源，二次元推荐使用 deepdanbooru 和 wd
tagger 进行标注，推荐后者。

WebUi 在预处理标签页已经内置了 deepdanbooru 标注器。

![preprocess.png](https://github.com/sudoskys/StableDiffusionBook/blob/723e4638bfa0f39235463928e8f441d5ebe35b4c/resource/preprocess.png?raw=true)

你也可以采用 WebUi 的 的 [wd tagger 插件](https://github.com/toriato/stable-diffusion-webui-wd14-tagger).

如果你学习能力比较强，可以直接采用 [Augmented-DDTagger](https://github.com/AdjointOperator/Augmented-DDTagger)
进行训练，它支持多种标注方式。

下面是一些相关的项目

[tagging with DeepDanbooru and BLIP](https://github.com/crosstyan/blip_helper)

| Repository                           | GitHub Link                                                                                                                                                |
|--------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| DeepDanbooru                         | https://github.com/KichangKim/DeepDanbooru <br> https://github.com/AUTOMATIC1111/TorchDeepDanbooru                                                         |
| clip+blip                            | https://github.com/pharmapsychotic/clip-interrogator                                                                                                       |
| FaceDetector                         | https://github.com/hysts/anime-face-detector <br> https://github.com/deepinsight/insightface <br> https://github.com/HRNet/HRNet-Facial-Landmark-Detection |
| Search                               | https://github.com/kitUIN/PicImageSearch                                                                                                                   |
| Remove text from AI-generated images | https://github.com/iuliaturc/detextify                                                                                                                     |
| WatermarkDetection                   | https://github.com/LAION-AI/LAION-5B-WatermarkDetection                                                                                                    |
| sd-tagging-helper                    | https://github.com/arenatemp/sd-tagging-helper                                                                                                             |

### SCAL-SDT 训练套件

[SCAL-SDT](https://github.com/CCRcmcpe/scal-sdt/wiki)

此工具套件包含了训练 Stable Diffusion (SD) 相关模型的各种方案，是海外中文社区通用的训练套件工作包，是一种高级的训练方法，可以让你在不同的训练方案中自由切换。

## 认知炼丹

如果你在 `--medvram` 参数下开始训练，可能会出现 `RuntimeError: Expected all tensors to be on the same device` 错误，无法创建训练。
这是优化机制导致的 [问题](https://github.com/AUTOMATIC1111/stable-diffusion-webui/issues/2399)，WebUi
在 [这次提交](https://github.com/AUTOMATIC1111/stable-diffusion-webui/commit/cbb857b675cf0f169b21515c29da492b513cc8c4)
中允许了在 `--medvram` 下创建 embedding 的情况。请更新版本到这个版本之后。

**关于 batch size**

更大的 batch size 可能稍微加快训练并稍微提升训练效果，但也需要更大的显存。

### LoCon/Lora 训练

Lora 是一种轻量级的微调方法，可以通过少量的图片训练出一个小模型，然后和大模型结合使用，干涉大模型产生的结果。

Lora 模型可以在 t2i 和 i2i 模式下使用，可以和任何主模型一起使用。

Lora 的模型文件后缀一般是 `.pt` 或者 `.safetensors`.

## 使用 Lora 模型

在扩展标签页安装 [Lora 模型加载插件](https://github.com/kohya-ss/sd-webui-additional-networks)，然后将你的 Lora 模型放到
stable-diffusion-webui/models/lora 路径下。比如 [幻星集塔罗牌 LoRA](https://civitai.com/models/11177).

重启 WebUi，然后在 SD（Sketch Design）的文生图或图生图界面内，点击生成按钮下的粉色图标，即“additional
networks”选项卡。在弹出的面板中选择“Lora”选项卡。

然后点击想要应用的 Lora 模型，它将被添加到提示语中，其格式为 `<lora: 数字 >`，数字代表 Lora 模型的权重，默认为 1。

接下来点击“生成”按钮开始生成图片。

当生成完成后，将鼠标移动到 Lora 卡片的左下角或标题上方，会出现“替换预览”的红色文字。点击此处即可将刚生成的图片设置为此 Lora
模型的预览图。

### Textual Inversion (TI)

从一些具有共同语义 [v] 的图片中，提取 [v] 的一个方法。提取出的 [v] 张量称之为 "Embedding"。将 Embedding 保存为文件，之后生成图片时就可以在
prompt 中以文件名引用。

#### 特征

训练产物大小较小，webui 自带训练支持。

可以解决新出的角色画不出的问题，或者模仿特定的可以用语言精确描述的艺术风格。

因为 TI 是在 Text Encoder 的输出做处理，所以并不能让模型学习到它不知道的概念。

** 不同模型的 embeddings 不通用 **

#### 使用

使用时，将 embedding（一个 .pt 或一个 .bin 文件）放入 webui 的 `embeddings` 目录并在 prompt 中写要用的 embedding
的文件名（不包括扩展名）即可，不必重启 webui。可以同时使用多个 embedding。

如果你使用 DreamArtist ，则将 `*-neg.pt` 一并放入 `embeddings` 目录，在积极和消极提示词中同时使用它们即可。

#### 相关

[webui 给的英文说明和效果图](https://github.com/AUTOMATIC1111/stable-diffusion-webui/wiki/Textual-Inversion)

[相关 embeddings](https://gitlab.com/16777216c/stable-diffusion-embeddings)，里面有相关效果预览。

[list of Textual Inversion embeddings for webui(SD)](https://rentry.org/embeddings)

[HuggingFace 的 embedding 库](https://cyberes.github.io/stable-diffusion-textual-inversion-models/)

### Hypernetwork (HN)

一类给模型生成权重的网络，在这里是给 LDM（潜在扩散模型） 生成权重。是一个较为实验性的方法，NAI 率先探索了在 LDM 上使用。

#### 特征

与 TI 不同，Hypernetwork 会改动 LDM 本身的权重，所以可以训练出无法用语言精确表述的细节，也更适用于画风的训练。

训练产物大小中等，webui 自带训练支持。

#### 使用

使用时，将 Pt 放入 `/models/hypernetworks` 并在设置选项勾选启用它。

NAI Leaks 的 `novelaileak\stableckpt\modules\modules` 中有 NAI 训练的一些 Hypernetwork。

!!! tip
`.pt` 文件，一般情况下小的是 embedding 大的是 hypernetwork。

### DreamBooth (DB)

直接微调 LDM 和 Text Encoder 以适应用户特定的图像生成需求的一个方法。

> 你能想象你自己的狗环游世界，或者你最喜欢的包在巴黎最独特的展厅里展示吗？你的鹦鹉成为一本插图故事书的主角呢？

#### 特征

与 TI 和 HN 不同，DreamBooth 可以做到出图和训练集高度相似但是却不失泛化能力，用于训练特定具象概念（比如一个角色穿着特定衣服）效果特别好。但是不像
TI 和 HN 像完整权重的 “插件” 一样即插即用，强度也不可调。

这个模型并非为学习画风（抽象概念）而设计。但似乎可以一定程度上适应“画风”。具体效果交由读者你实验。

#### 使用

把 DreamBooth 训练出的 .ckpt 文件放进 webui 的 `models\Stable-diffusion` 目录里，在 webui 的左上角切换到即可使用。

![SAMPLE](https://dreambooth.github.io/DreamBooth_files/high_level.png)

官网 https://dreambooth.github.io/

论文 https://arxiv.org/abs/2208.12242

### Advanced Prompt Tuning (APT)

> "Can super dramatically improve the image quality and diversity"

[DreamArtist-sd-webui-extension](https://github.com/7eu7d7/DreamArtist-sd-webui-extension)

添加对否定词的即时嵌入学习，以显着提高生成图像的质量。 高质量的概念可以从单个图像中学习。

添加重建损失以提高生成图像的细节质量和丰富度。

添加通过人工注释训练的鉴别器（使用 convnext 实现）允许嵌入基于模型进行学习。

使用方法与 `Textual Inversion` 相同。

### Aesthetic Gradients

微调 CLIP 以适应某个特定生成需求的方法，可以和 TI 一样起到缩短 prompt 的作用。可能略微提升出图的质量。

这项技术通过在生成时计算每个图片的权重，提供了一个 ` 我不说你应该懂往哪里训练 ` 的功能。使 AI 更聪明地调整并增加细节。

此项功能来自这个 [存储库](https://github.com/vicgalle/stable-diffusion-aesthetic-gradients)
，在 [这次提交](https://github.com/AUTOMATIC1111/stable-diffusion-webui/commit/2b91251637078e04472c91a06a8d9c4db9c1dcf0)
中，此功能被剥离为插件。

#### 特征

通过这项技术，你不需要通过 过多提示词 来提升图片的质量，而是保持作品原始的总体构图，并提高美观度。在少量提示词情况下也可以生成效果不错的作品。

据暗影·夜光所言 [^11]，添加 25% 以内的权重，就可以稍微改善画面的美观度而不影响内容。美学 与 Hypernetworks 让 Ai
作品更接近原画师风格，但是美学权重本身效果并不好。需要配合 Hypernetworks 超网络。

训练这项模型很快，但是在每一次生产时都会重新为图片计算一次，所以出图很慢。

注意：当种子改变时，训练结果也会改变。

#### 使用

你可以使用下面的 Git 命令来安装这个东西。

```commandline
git clone https://github.com/AUTOMATIC1111/stable-diffusion-webui-aesthetic-gradients extensions/aesthetic-gradients
```

安装后，在 webui 的 `extensions` 文件夹下面创建 `aesthetic-gradients` 文件夹。

使用时，把 Pt 放在 `models/aesthetic_embeddings/`

然后重启程序，你就可以在 Img2Img 中使用此项功能。

![Aesthetic_other](https://user-images.githubusercontent.com/75739606/197824140-50dca98e-856f-44ca-99e6-da2fee5bb23c.png)
<!--
![Aesthetic_other](https://raw.githubusercontent.com/sudoskys/StableDiffusionBook/main/resource/Aesthetic_other.png)
-->

## 附录

- 很好的 [naifu-diffusion](https://github.com/Mikubill/naifu-diffusion)

- [WD 1.4 tagger for Automatic1111's Web UI](https://github.com/toriato/stable-diffusion-webui-wd14-tagger)

- [Resize Tool](https://www.birme.net/)

[^7]:[风格模型训练](https://www.bilibili.com/video/BV1ae4y1S7v9/)

[^8]:[迭代草图](https://github.com/AUTOMATIC1111/stable-diffusion-webui/discussions/2473)

[^9]:[交替单词](https://github.com/AUTOMATIC1111/stable-diffusion-webui/pull/1733)

[^10]:[角色与画风 tag 训练十问](https://www.bilibili.com/video/BV1xt4y1F7Y2/)

[^11]:[WebUI 即将引入重磅更新，大幅提升图像品质](https://www.bilibili.com/read/cv19102552)

[^12]:[超网络训练](https://github.com/AUTOMATIC1111/stable-diffusion-webui/discussions/2284)

[^14]:[dreambooth- 小鬼峰](https://www.bilibili.com/read/cv18935031?from=search)

[^15]:[dreambooth- 论文](https://arxiv.org/abs/2208.12242)

[^17]:[Installing Stable Diffusion & Dreambooth for Windows](https://pastebin.com/xcFpp9Mr)

[^18]:[Installing Dreambooth for Windows](https://gist.github.com/Summersoff/70861d757a40c153c5802dc8c4ed68c0)
