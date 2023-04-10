# Prompts Engineering

这里是为进一步教学如何书写提示词所写的一个小指南，让你掌握初步调整画面的能力。

此页内容属于 WebUi 的程序特性。如果你是 NAI 用户，请去阅读 [官网 Docs](https://docs.novelai.net/image/promptmixing.html)。

## 入门

书写 Prompt 的感性感受为：限定数据范围内符合期望的样本越多，结果越符合预期。

### 写什么？

Ai 的理解由代码输入，由 Tokenizer 定义，你可以测试 UI 的相关代码获取实际的分词输入。

- [NAI's prompt Tokenizer](https://novelai.net/tokenizer)
- [WebUiTokenizer](https://github.com/AUTOMATIC1111/stable-diffusion-webui-tokenizer)

**单词**

普通常见的单词，最好是可以在数据集来源站点找到的著名标签（比如 Danbooru)。单词的风格要和图像的风格搭配（比如在推理动漫插画时不应该在提示词加入 `乐山大佛` 的词汇），否则会出现混杂的风格或噪点。

> 对于某些基于 Stable Diffusion 训练的动漫模型来说，最好使用 Danbooru 中可以找到的标签。

**自然语言**

Prompt 也可以直接使用自然语言，语种可以是英文，日文，特殊符号或一些中文，这由数据集决定。

自然语言的准确度取决于 Clip 的分词情况，如果你追求精确的结果，请勿使用。

使用自然语言的时候要避免 `with` 之类的连接词或复杂的语法，它们很多余。

**emoji**

你可以使用 西方颜文字 `(^_^)`，emoji 🌻。emoji 作为只有一个字符的“词语”效果很精确。

- Emoji 在构图上有影响，比如 `💐☺️💐`。
- Emoji 因为只有一个字符，所以在语义准确度上表现良好。
- [表情符号参考](https://unicode.org/emoji/charts/emoji-list.html)

**颜文字**

对于**使用 Danbooru 数据的模型**来说，我们可以使用 颜文字 来控制表情！
```
:-) 微笑 :-( 不悦 ;-) 使眼色 :-D 开心 :-P 吐舌头 :-C 很悲伤 :-O 惊讶 张大口 :-/ 怀疑
```

- 颜文字在构图上有影响。

- 仅支持西方颜文字，详细内容请见 [Danbooru 颜文字部分](https://danbooru.donmai.us/wiki_pages/tag_group%3Aface_tags) 或 [维基百科](https://zh.wikipedia.org/wiki/%E8%A1%A8%E6%83%85%E7%AC%A6%E8%99%9F%E5%88%97%E8%A1%A8?oldformat=true)

**空格**

- 逗号前后的少量空格并不影响实际效果。

### 如何写？

先想一下你要画什么，例如 主题，外表，情绪，衣服，姿势，背景 一类，然后参考数据集标签表（如果有的话，比如 Danbooru, Pixiv 等）。

然后将你想要的相似的提示词组合在一起，请使用英文半角 `,` 做分隔符，并将这些按从最重要到最不重要的顺序排列。

```
(quality), (subject)(style), (action/scene), (artist), (filters)
```
`(quality)` 代表画面的品质，比如 `low res` 结合 `sticker` 使用来 “利用” 更多数据集， `1girl` 结合 `high quality` 使用来获得高质量图像。

`(subject)` 代表画面的主题，锚定画面内容，这是任何提示的基本组成部分。`(style)` 是画面风格，可选。

`(action/scene)` 代表动作/场景，描述了主体在哪里做了什么。

`(artist)` 代表艺术家名字或者出品公司名字。

`(filters)` 代表一些细节，补充。可以使用 艺术家，工作室，摄影术语，角色名字，风格，特效等等。

**艺术风格**

你可以通过指定风格关键词来创作带有特效或指定画风的图片。

[NovelAI 使用教程和魔咒课堂](https://space.bilibili.com/8612008/channel/collectiondetail?sid=787691)

[人偶教室的测试记录](https://www.yuque.com/longyuye/lmgcwy)

[风格化：32 种](https://www.bilibili.com/video/BV1TP411N71t/)

除此之外，你还可以训练风格模型来训练自己的理想风格，详见 `模型训练` 章节。

更多参考可以读 [为文字转图像 Ai 提示编写指南：A Guide to Writing Prompts for Text-to-image AI](https://docs.google.com/document/d/1XUT2G9LmkZataHFzmuOtRXnuWBfhvXDAo8DkS--8tec/edit#)

### 写多长？

**书写长度**

由于 GPT-3 模型限制，prompt 并不是无限的，一般 positive token 在 75-80 之间，75 字符后的内容会被截断。所以提示不要太长，超过 100 就有失败风险。

- WebUi

在 WebUi 中，你 **可以** 写 75 个词汇以上的提示。WebUi 通过对提示词进行分组克服了这种限制。当提示超过 75 个 `token`（比如 150 个 `token`）时，WebUi 将分组提示词，提交多组 75 个 `token`。标记只具有同一集合中其他内容的上下文。

但这导致了一个问题：可能在第一组和第二组之间的边界处有 `bule hair`，标记 `blue` 将在第一组中，`hair` 将在第二组中。这导致结果不准确，因为 `bule hair` 被不合理地分割了！

为了解决这个问题，新版本增加了一个选项 `Increase coherency by padding from the last comma within n tokens when using more than 75 tokens`，这个设置让程序试图通过查找最后 N 个标记中是否有最后一个逗号来缓解这种情况，如果有，则将所有经过该逗号的内容一起移动到下一个集合中。

```
    有词条为 `...,Comma,blue hair,PADDING,...`
    
    第 75 个词为 `blue`
    
    使用该选项前
    
    集合 1:{[74]=Comma，[75]=blue}，集合 2:{[76]=hair, [77]=PADDING}
    
    使用该选项后
    
    集合 1:{[074]=Comma，[75]=PADDING}，集合 2:{[76]=blue，[77]=hair}
    
    如果您的提示小于等于 75 个标记，不会发生分组。
```

!!! tip
    不堆叠 prompt 是一个好习惯，但是如果你确实有很多内容要写，可以适当提高 `step` 数量。

### 顺序

根据图像推理过程，提示词放入的顺序可以视为优先级，在前面的 Prompt 锚定了画面的主体内容。这解释了为什么我会在上文中强调 Prompt 的顺序。

![图像生成的描述](https://jalammar.github.io/images/stable-diffusion/stable-diffusion-image-generation.png)

> thanks https://jalammar.github.io/illustrated-stable-diffusion/

如果你使用 WebUi，WebUi 会对长串的 Prompt 以 75 个 Token 一组分开渲染。

### Prompt 冲突

**分类冲突**

    如果你想要一张 sticker 的话，贴纸在数据集内肯定不会打上 `masterpiece, best quility,` 标签，而是 `normal quality` 这样的标签。

    类似还有像素作品，书写提示词的时候，你应该移除一些冲突的提示。

**风格冲突**

    比如你想得到一张儿童在沙滩玩耍的 `flatcolor` 风格的插画。

    那么使用 `loli` 不是一个好主意，因为它附带了标准统一的画风属性，会很大地影响结果。

    解决办法：降低权重，更换 prompt.

**次元冲突**

    某些动漫模型是基于原始 Stable Diffusion 训练的应该尽量避免出现原始模型的内容。

请务必注意。

## 提示词编辑

通过提示词编辑，我们可以清楚地告诉模型 “我点的菜需要放多少盐”。

[原始实现](https://github.com/bloc97/CrossAttentionControl)

### 人工分割

使用 `/` 或 `+` 来强制分割提示词。[^13]

需要注意的是，相邻重复的符号会被过滤，```+++``` 的效果等同于 `+`，正确的方法是+和/分开来打。

比如同样要达到 9 个 token 长度的分隔：[^13]
```markdown
# 以下三条效果类似，且不怎么干扰后续词语。影响度 7%左右：
red eyes,/,/,/,/, blue dress     (9 格分隔，按键也接近，推荐）
red eyes, /+/+/+/, blue dress (9 格分隔，干扰最低）
red eyes, , , , , , , , ,blue dress (9 格分隔，逗号之间要有空格不然效果减半）

# 以下是不推荐的办法，对后续词语仍然有 25%影响，会干扰画风：
red eyes++++++++++++++++++++blue dress  （同样是 9 格，三个+号判断为一个 token)
red eyes/​/​/​/​/​/​/​/​/​/​/​/​/​/​/​/​/​/​/​/​/​/​/​/​/​/​/​/​/​/​/​/​/​blue dress （同加号）

* Tip-From [^13]
```

### 注意力控制

阅读理论基础我们知道，每个词的基础权重是不同的，我们对不同提示的权重值的期望也不同，这时候就需要使用注意力控制来调节它们之间的关系了。

| Ui | 语法 | 示例 | 权重变化 | 注意 |
|:----:| :----: | :----: |:----: |:----: |
| WebUi | `(prompt:num)` | 强调：`((cat))` `(cat:1.4)`，减弱：`(cat:0.2)` `[cat]` | ()-> 1.1 |`num` 的范围是 `0.1 ~:100`，如果为小数则取步数比例|
| Nai | `{}` | `{{{cat}}}` `[cat]` | {}-> 1.05 | |
| 原始实现 | +/- | `+cat` `-cat` |  |单元格 |

1. WebUi 权重语法为 `(prompt:num)` 或 `((prompt))`，增强的权重为，增强的范围是 `0.1 ~ 100`，如果为小数则取步数比例。比如 强调：`((cat))` `(cat:1.4)`，减弱：`(cat:0.2)` `[cat]`

    ```
    a (word) - 将权重提高 1.1 倍

    a ((word)) - 将权重提高 1.21 倍（= 1.1 * 1.1），乘法的关系。

    a [word] - 将权重减少 1.1 倍

    a (word:1.5) - 将权重提高 1.5 倍

    a (word:0.25) - 将权重减少 4 倍 (= 1 / 0.25)

    a \(word\) - 在提示中使用文字 () 字符
    ```

    !!! tip
        权重增加通常会占一个 token 位。在 token 位紧张的情况下没有必要加特别多括号。

        过多圆括号会导致 字符 被程序吃掉，`a ((((farm))), daytime` 会变成 `a farm daytime` 而没有逗号。

        WebUi 对于带括号提示词比如 `a (word)`  **请使用 `\` 字符转义为 `a \(word\)`**，这适用于带括号的 Tag, 防止出现本来不想要的增强效果。

2. NAI 则使用 `{}` 来做增强，不可以指定权重。比如 `{{{cat}}}` `[cat]`

   NAI 中不允许单独指定权重，但支持混合权重 `cat:1|happy:-0.2|cute:-0:3` 这样的语法，`(cat :2 | dog)` 也就是更像猫的狗。

   **换算关系**

   NAI 的 [word] = WebUi(word: 0.952)(0.952 = 1/1.05)
    
   NAI 的{word} = WeUi 的 (word: 1.05)
    
   NAI 花括号权重为 1.05/个，WebUi 圆权重为 1.1/个

3. 降低权重，单独使用 `[]` 或者 `(word:0.952)`。NAI 仅能使用 `[]`

!!! tip "请勿混淆"

    NAI 与 WebUi 的提示词语法 **不通用**

    因为 NAI 使用的是 WebUi 2022 年 9 月之前的实现，所以权重增强语法是旧的 `{}` ，新的 WebUi 更改为 `()`。
    
    NAI 不支持指定提示词的权重。

    如果你想便捷转换权重，可以使用 [转换服务](https://t.me/M2NM2NBot)

??? tip "How It Work"
    每个单词都有一个 768 个值的相关向量，该向量“指向”概念的方向（在 768 维空间中）。

    如果你缩放这个向量，这个概念会变得更强或更弱。
    
    SEE [Here](https://github.com/AUTOMATIC1111/stable-diffusion-webui/discussions/2905)

![缺少示例图片]

### 提示词交替

提示词交替（alternate prompt）[^7]，即 Prompt 混合机。这项技术由 UI 实现。[^9]

```
[alison brie|emma stone|elizabeth olsen|scarlett johansson|anne hathaway|emma roberts], still film
```

它可以创建动物、人或风格的混合体，每一个 `step` 切换一个提示（一步一换轮环渲染）。

![缺少示例图片]

### Prompt matrix 矩阵

WebUi 使用 `|` 分隔多个关键词以混合多个要素，程序将为它们的每个组合生成一个图像。这项技术由 UI 实现。

例如，如果使用 `a busy city street in a modern city|illustration|cinematic lighting` ，则可能有四种组合（始终保留提示的第一部分）：

- a busy city street in a modern city
- a busy city street in a modern city, illustration
- a busy city street in a modern city, cinematic lighting
- a busy city street in a modern city, illustration, cinematic lighting

![缺少示例图片]

可以进一步在关键词后添加 : x 来指定单个关键词的权重。

- NAI 

在 NAI 中代表平均权重混合（前半部分和后半部分）。

`cat :2 | dog` 也就是更像猫的狗

![缺少示例图片]

### Prompt Schedule

Prompt Schedule 可以在推理图像的过程中更改 Prompt，这项技术由 UI 实现。

语法为 `[from:to:when]`

其中 `from` 和 `to` 为 prompt， when 是一个数字，用于定义应在采样周期多长时间内进行切换。

- `[fantasy:cyberpunk:16]` 代表从第 16 step 后，使用 `cyberpunk` 标签替换 `fantasy`

- `[to:when]` 在固定数量的 step 后添加 `to` 到提示 ( when)

- `[from::when]` 在固定数量的 step 后从提示中删除 `from`( when)

比如：

- `[fantasy:cyberpunk:16]` 代表从第 16 step 后，使用 `cyberpunk` 标签替换 `fantasy`

- `[male: female: 0.0]`, 意味着你开始时就要求画一个女性。

效果：

![sample_Gradient](https://user-images.githubusercontent.com/75739606/197822841-f7323afa-8c6a-46a2-a8e2-a1c457bb31d5.jpg)

<!--
![sample_Gradient](https://raw.githubusercontent.com/sudoskys/StableDiffusionBook/main/resource/sample_Gradient.jpg)
-->

## 成为调参工程师

这些指南可以让你更进一步。

- [emphasis 测试](https://github.com/JohannesGaessler/stable-diffusion-tools/tree/master/emphasis)

- [面向 NAI 的测试](https://github.com/6DammK9/nai-anime-pure-negative-prompt)

- [https://github.com/Maks-s/sd-akashic](https://github.com/Maks-s/sd-akashic)

- [https://github.com/willwulfken/MidJourney-Styles-and-Keywords-Reference](https://github.com/willwulfken/MidJourney-Styles-and-Keywords-Reference)

[^9]:[alternate prompt](https://github.com/AUTOMATIC1111/stable-diffusion-webui/pull/1733)

[^13]:[Nga 我流心得](https://nga.178.com/read.php?pid=654605407&opt=128)