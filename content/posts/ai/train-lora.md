---
title: LoRA微调模型训练中需要注意的关键点和参数梳理
description: null
author: 李留白
weight: 0
date: 2023-05-16T15:35:15.317Z
lastmod: 2023-05-22T16:32:56.190Z
tags: []
categories:
  - AI
featuredImage: https://hicoldcat.oss-cn-hangzhou.aliyuncs.com/img/20230516233004.png
draft: true
---

## 学习中使用的术语简单解释

由于省略了细节并且我自己也没有完全理解，因此请自行查阅详细信息。

### 微调（fine tuning）

指训练模型并微调其性能。具体含义因用法而异，但在 Stable Diffusion 中，狭义的微调是指使用图像和标题进行训练模型。DreamBooth 可视为狭义微调的一种特殊方法。广义的微调包括 LoRA、Textual Inversion、Hypernetworks 等，包括训练模型的所有内容。

### 步骤（step）

粗略地说，每次在训练数据上进行一次计算即为一步。具体来说，“将训练数据的标题传递给当前模型，将生成的图像与训练数据的图像进行比较，稍微更改模型，以使其更接近训练数据”即为一步。

### 批次大小（batch size）

批次大小指定每个步骤要计算多少数据。批量计算可以提高速度。一般来说，批次大小越大，精度也越高。

“批次大小×步数”是用于训练的数据数量。因此，建议减少步数以增加批次大小。

（但是，例如，“批次大小为 1，步数为 1600”和“批次大小为 4，步数为 400”将不会产生相同的结果。如果使用相同的学习速率，通常后者会导致模型欠拟合。请尝试增加学习率（例如 `2e-6`），将步数设置为 500 等。）

批次大小越大，GPU 内存消耗就越大。如果内存不足，将导致错误，或者在边缘时将导致训练速度降低。建议在任务管理器或 `nvidia-smi` 命令中检查使用的内存量进行调整。

另外，批次是指“一块数据”的意思。

### 学习率

学习率指的是每个步骤中改变的程度。如果指定一个大的值，学习速度就会加快，但是可能会出现变化太大导致模型崩溃或无法达到最佳状态的情况。如果指定一个小的值，学习速度会变慢，也可能无法达到最佳状态。

在fine tuning、DreamBooth、LoRA等过程中，学习率会有很大的差异，并且也会受到训练数据、所需训练的模型、批量大小和步骤数等因素的影响。建议从一般的值开始，观察训练状态并逐渐调整。

默认情况下，整个训练过程中学习率是固定的。但是可以通过调度程序指定学习率如何变化，因此结果也会有所不同。

### 轮数（epoch）

Epoch指的是训练数据被完整训练一遍（即数据一周）的情况。如果指定了重复次数，则在重复后的数据一周后，就是1个epoch。

1个epoch的步骤数通常为“数据量÷批量大小”，但如果使用Aspect Ratio Bucketing，则略微增加（由于不同bucket的数据不能在同一个批次中，因此步骤数会增加）。

### 纵横比分桶（Aspect Ratio Bucketing)

Stable Diffusion 的 v1 是以 512\*512 的分辨率进行训练的，但同时也可以在其他分辨率下进行训练，例如 256\*1024 和 384\*640。这样可以减少裁剪的部分，期望更准确地学习图像和标题之间的关系。

此外，由于可以在任意分辨率下进行训练，因此不再需要事先统一图像数据的纵横比。

该设置在配置中有效，可以切换，但在此之前的配置文件示例中已启用（设置为 `true`）。

学习分辨率将根据参数所提供的分辨率面积（即内存使用量）进行调整，以64像素为单位（默认值，可更改）在纵横方向上进行调整和创建。

在机器学习中，通常需要将所有输入大小统一，但实际上只要在同一批次中统一即可。 NovelAI 所说的分桶(bucketing) 指的是，预先将训练数据按照纵横比分类到每个学习分辨率下，并通过使用每个 bucket 内的图像创建批次来统一批次图像大小。

## 数据集

支持的图片格式：`.png`, `.jpg`, `.jpeg`, `.webp`, `.bmp` 。

图片素材不能太小也不能太大。如果太小，可以使用一些超分辨率的修复工具进行高清修复。如果太大，可能会导致训练失败，建议提前适当缩小或者裁剪一下。

## 训练方式

### class+identifier方法（可使用正则化图像）

数据集图片文件夹一般命名为`epoch_identifier class`方式，如`20_shs dog`。

`identifier`是用于识别学习目标并进行学习的单词。可以使用任何单词，但是根据原始论文，**“Tokenizer生成的3个或更少字符的罕见单词”是最好的选择**。也就是说，`identifier`最好是3个或者更少的字母，并且是一个独特的单词，而不是常见的单词。

`class`是学习目标的一般类别。例如，如果要学习特定品种的狗，则`class`将是`dog`。对于动漫角色，根据模型不同，可能是`boy`或`girl`，也可能是`1boy`或`1girl`。

在图像生成时，使用`shs dog`将生成所学习狗种的图像。

至于正则化图像，是为防止语言漂移，即整个类别被拉扯成为学习目标而生成的图像。如果不使用正则化图像，例如在 `shs 1girl` 中学习特定角色时，即使在简单的 `1girl` 提示下生成，也会越来越像该角色。这是因为 `1girl` 在训练时的标题中包含了该角色的信息。

通过同时学习目标图像和正则化图像，类别仍然保持不变，仅在将标识符附加到提示中时才生成目标图像。

如果您只想在LoRA或DreamBooth中使用特定的角色，则可以不使用正则化图像。

这种方式基本上只需要设置以下几种参数：

1. 学习分辨率
2. 批量大小
3. 图片源文件夹
4. identifier 和 class 的设置
5. 迭代次数

### Caption方式（可使用正则化图像）
> 俗称对图片打标签或者自然语言描述

需要准备与图像具有相同文件名且扩展名为 `.caption`（可以在设置中更改）的文件放置在用于训练图像的文件夹中(或者`.txt`)。每个文件应该只有一行。编码为 `UTF-8`。

这种方式与class+identifier格式相同。可以在正则化图像上附加标题，但通常不需要。

这种方式基本上只需要设置以下几种参数：

1. 学习分辨率
2. 批量大小
3. 图片源文件夹
4. 标题文件的扩展名，一般为`.caption`或者`.txt`
5. 重复次数

### 微调方式

就是将标题和标签整合到管理文件中，这个文件称为元数据。它的扩展名为 `.json`，格式为json。这里暂时不细讲，可以文末查看资料文档。

## 常用参数解析

> 这个参数没有很全的文档，有兴趣的可以去看[https://github.com/kohya-ss/sd-scripts](https://github.com/kohya-ss/sd-scripts)源码详细查询。

**这里主要时训练LoRA的参数。**，入口：[train_network.py](https://github.com/kohya-ss/sd-scripts/blob/main/train_network.py)

这里分为2种方式:

1. 从命令行传参，如

```
python -m accelerate.commands.launch --num_cpu_threads_per_process=8 "./sd-scripts/train_network.py" --output_name=test
```

2. 从配置文件传参，就是将参数放到`.toml`文件中，并使用`--config_file`将配置文件传给脚本的方式

```
python -m accelerate.commands.launch --num_cpu_threads_per_process=8 "./sd-scripts/train_network.py" --config_file="E:\test.toml"
```




------------------------------------------------------------



| 参数（--） | 类型 | 默认值 | 说明 |
| ---------- | ---- | ------ | ---- |
|            |      |        |      |
|            |      |        |      |
|            |      |        |      |
|            |      |        |      |



### 底模设置

| 参数（--）    | 类型 | 默认值 | 说明 |
|----------------| ----- |----------------|----------------|
| **pretrained_model_name_or_path** | str |  | 底模文件夹路径 |
| **v2** |  | | 加载Stable Diffusion v2.0模型|
| **v_parameterization** | | | 开启 v-parameterization 训练|
| tokenizer_cache_dir | str |  | 缓存Tokenizer的目录（用于离线训练） |

### 数据集设置

| 参数（--）                | 类型 | 默认值   | 说明                                                         |
| ------------------------- | ---- | -------- | ------------------------------------------------------------ |
| **train_data_dir**        | str  |          | 训练数据集目录                                               |
| **shuffle_caption**       |      |          | 洗牌打乱由逗号分隔的caption的每个元素                        |
| **caption_extension**     |      | .caption | caption文件后缀                                              |
| **keep_tokens**           | int  | 0        | 当洗牌打乱caption token时，保留前N个token（令牌表示逗号分隔出来的字符串） |
| color_aug                 |      |          | 启用弱色增强                                                 |
| flip_aug                  |      |          | 启用水平翻转增强                                             |
| face_crop_aug_range       | str  |          | 启用以面部为中心的作物及其范围（例如2.0,4.0）                |
| random_crop               |      |          | 启用随机裁剪（用于以面部为中心的作物增强的样式训练）         |
| debug_dataset             |      |          | 显示用于调试的图像（请勿训练）                               |
| **resolution**            |      |          | 训练的分辨率（“大小”或“宽度，高度”）                         |
| **cache_latents**         |      |          | 缓存latents到主内存以减少VRAM使用（必须禁用augmentations）   |
| vae_batch_size            | int  | 1        | 缓存latents的pi                                              |
| **cache_latents_to_disk** |      |          | 缓存latents到磁盘可减少VRAM使用情况（必须禁用增强）          |
| **enable_bucket**         |      |          | 是否使用Aspect Ratio Bucketing                               |
| **min_bucket_reso**       |      |          | 桶的最低分辨率                                               |
| **max_bucket_reso**       |      |          | 水桶的最高分辨率                                             |
| bucket_reso_steps         | int  | 64       | 桶的分辨率步骤，推荐设置为8                                  |
| bucket_no_upscale         |      |          | 在不放大图像的情况下为每个图像创建一个桶                     |
| token_warmup_min          |      |          | 在n标签后开始学习（令牌是指逗号分隔的strinfloatgs）          |
| token_warmup_step         |      |          | 标签长度在n步骤上达到最大值（或n*max_train_steps，如果n <1） |

如果support_caption_dropout，Dropout是一种在神经网络中用于防止过拟合的正则化方法

| 参数（--）                     | 类型  | 默认值 | 说明                                             |
| ------------------------------ | ----- | ------ | ------------------------------------------------ |
| caption_dropout_rate           | float | 0.0    | Rate out dropout caption(0.0~1.0)                |
| caption_dropout_every_n_epochs | int   | 0      | Dropout all captions every N epochs              |
| caption_tag_dropout_rate       | float | 0.0    | Rate out dropout comma separated tokens(0.0~1.0) |

如果support_dreambooth

| 参数（--）       | 类型 | 默认值 | 说明         |
| ---------------- | ---- | ------ | ------------ |
| **reg_data_dir** | str  |        | 正则图像目录 |

如果support_caption

| 参数（--）      | 类型 | 默认值 | 说明                       |
| --------------- | ---- | ------ | -------------------------- |
| in_json         | str  |        | 数据集的JSON元数据         |
| dataset_repeats | int  | 1      | 用字幕训练时重复数据集次数 |

### 训练设置

| 参数（--）                         | 类型                                                         | 默认值 | 说明                                                         |
| :--------------------------------- | :----------------------------------------------------------- | ------ | ------------------------------------------------------------ |
| **output_dir**                     | str                                                          |        | 模型输出文件夹                                               |
| **output_name**                    | str                                                          |        | 模型保存名称                                                 |
| huggingface_repo_id                | str                                                          |        | huggingface repo name to upload                              |
| huggingface_repo_type              | *str*                                                        |        | huggingface repo type to upload                              |
| huggingface_path_in_repo           | str                                                          |        | huggingface model path to upload files                       |
| huggingface_token                  | str                                                          |        | huggingface token                                            |
| huggingface_repo_visibility        | str                                                          |        | huggingface repository visibility ('public' for public, 'private' or None for private) |
| save_state_to_huggingface          |                                                              |        | save state to huggingface                                    |
| resume_from_huggingface            |                                                              |        | resume from huggingface (ex: --resume {repo_id}/{path_in_repo}:{revision}:{repo_type}) |
| async_upload                       |                                                              |        | upload to huggingface asynchronously                         |
| **save_precision**                 |                                                              |        | 保存精度                                                     |
| **save_every_n_epochs**            | *int*                                                        |        | 每N个轮数保存一次checkpoint                                  |
| **save_every_n_steps**             | int                                                          |        | 每N个步数保存一次checkpoint                                  |
| save_n_epoch_ratio                 | int                                                          |        | save checkpoint N epoch ratio (for example 5 means save at least 5 files total) |
| **save_last_n_epochs**             | *int*                                                        |        | save last N checkpoints when saving every N epochs (remove older checkpoints) |
| **save_last_n_epochs_state**       | int                                                          |        | save last N checkpoints of state (overrides the value of --save_last_n_epochs) |
| **save_last_n_steps**              | int                                                          |        | save checkpoints until N steps elapsed (remove older checkpoints if N steps elapsed) |
| **save_last_n_steps_state**        | int                                                          |        | save states until N steps elapsed (remove older states if N steps elapsed, overrides --save_last_n_steps) |
| **save_state**                     |                                                              |        | save training state additionally (including optimizer states etc.) |
| resume                             | *str*                                                        |        | saved state to resume training                               |
| **train_batch_size**               | *int*                                                        | 1      | 批量训练数量                                                 |
| **max_token_length**               | *int*，可选项[None, 150, 225]                                |        | max token length of text encoder (default for 75, 150 or 225) |
| mem_eff_attn                       |                                                              |        | use memory efficient attention for CrossAttention            |
| **xformers**                       |                                                              |        | use xformers for CrossAttention                              |
| vae                                | str                                                          |        | path to checkpoint of vae to replace                         |
| **max_train_steps**                | *int*                                                        | 1600   | 最大训练步数                                                 |
| **max_train_epochs**               | *int*                                                        |        | training epochs (overrides max_train_steps)                  |
| max_data_loader_n_workers          | *int*                                                        | 8      | max num workers for DataLoader (lower is less main RAM usage, faster epoch start and slower data loading) |
| **persistent_data_loader_workers** |                                                              |        | persistent DataLoader workers (useful for reduce time gap between epoch, but may use more memory) |
| **seed**                           | *int*                                                        |        | random seed for training                                     |
| **gradient_checkpointing**         |                                                              |        | enable gradient checkpointing                                |
| **gradient_accumulation_steps**    | *int*                                                        | 1      | Number of updates steps to accumulate before performing a backward/update pass |
| **mixed_precision**                | *str*，可选项["no", "fp16", "bf16"]                          |        | 混合训练精度                                                 |
| full_fp16                          |                                                              |        | fp16 training including gradients                            |
| **clip_skip**                      | *int*                                                        |        | use output of nth layer from back of text encoder (n>=1)     |
| **logging_dir**                    | *str*                                                        |        | enable logging and output TensorBoard log to this directory  |
| **log_with**                       | *str*，["tensorboard", "wandb", "all"]                       |        | what logging tool(s) to use (if 'all', TensorBoard and WandB are both used) |
| **log_prefix**                     | *str*                                                        |        | add prefix for each log directory                            |
| **log_tracker_name**               | str                                                          |        | name of tracker to use for logging, default is script-specific default name |
| **wandb_api_key**                  | *str*                                                        |        | specify WandB API key to log in before starting training (optional) |
| **noise_offset**                   | *float*                                                      |        | enable noise offset with this value (if enabled, around 0.1 is recommended) |
| **multires_noise_iterations**      | *int*                                                        |        | enable multires noise with this number of iterations (if enabled, around 6-10 is recommended) |
| **multires_noise_discount**        | *float*                                                      | 0.3    | set discount value for multires noise (has no effect without --multires_noise_iterations) |
| adaptive_noise_scale               | *float*                                                      |        | add `latent mean absolute value * this value` to noise_offset (disabled if None, default) |
| **lowram**                         |                                                              |        | enable low RAM optimization. e.g. load models to VRAM instead of RAM (for machines which have bigger VRAM than RAM such as Colab and Kaggle) |
| **sample_every_n_steps**           | *int*                                                        |        | generate sample images every N steps                         |
| **sample_every_n_epochs**          | *int*                                                        |        | generate sample images every N epochs (overwrites n_steps)   |
| **sample_prompts**                 | *str*                                                        |        | file for prompts to generate sample images                   |
| **sample_sampler**                 | *str*，["ddim","pndm","lms",<br />"euler","euler_a","heun",<br />"dpm_2","dpm_2_a","dpmsolver",<br />"dpmsolver++","dpmsingle",<br />"k_lms","k_euler","k_euler_a", "k_dpm_2","k_dpm_2_a",] |        | sampler (scheduler) type for sample images                   |
| **config_file**                    | *str*                                                        |        | using .toml instead of args to pass hyperparameter           |
| **output_config**                  |                                                              |        | output command line args to given .toml file                 |

support_dreambooth

| 参数（--）            | 类型    | 默认值 | 说明                                  |
| --------------------- | ------- | ------ | ------------------------------------- |
| **prior_loss_weight** | *float* | 1.0    | loss weight for regularization images |

### 优化器设置

| 参数（--）                  | 类型                                                         | 默认值                                                       | 说明                                                         |
| --------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **optimizer_type**          | *str*,[AdamW (default), AdamW8bit,<br /> Lion8bit, Lion, SGDNesterov, <br />SGDNesterov8bit, DAdaptation(DAdaptAdam), <br />DAdaptAdaGrad, DAdaptAdan, DAdaptSGD, AdaFactor] | “”                                                           | Optimizer to use                                             |
| use_8bit_adam[不需要]       |                                                              |                                                              | use 8bit AdamW optimizer (requires bitsandbytes)             |
| use_lion_optimizer[不需要]  |                                                              |                                                              | use Lion optimizer (requires lion-pytorch)                   |
| **learning_rate**           | *float*                                                      | 2.0e-6                                                       | 学习率                                                       |
| max_grad_norm               | *float*                                                      | 1.0,                                                         | Max gradient norm, 0 for no clipping                         |
| optimizer_args              | *str*                                                        | *nargs*="*"                                                  | additional arguments for optimizer (like "weight_decay=0.01 betas=0.9,0.999 ...") |
| lr_scheduler_type           | *str*                                                        |                                                              | custom scheduler module                                      |
| lr_scheduler_args           | *str*                                                        | *nargs*="*"                                                  | additional arguments for scheduler (like "T_max=100")        |
| **lr_scheduler**            | *str*                                                        | constant，[linear, cosine, cosine_with_restarts, polynomial, constant (default), constant_with_warmup, adafactor] | scheduler to use for learning rate                           |
| **lr_warmup_steps**         | *int*                                                        | 0                                                            | Number of steps for the warmup in the lr scheduler (default is 0) |
| **lr_scheduler_num_cycles** | int                                                          | 1                                                            | Number of restarts for cosine scheduler with restarts        |
| lr_scheduler_power          | *float*                                                      | 1                                                            | Polynomial power for polynomial scheduler                    |

### 配置设置

| 参数（--）     | 类型 | 默认值 | 说明                            |
| -------------- | ---- | ------ | ------------------------------- |
| dataset_config | Path |        | config file for detail settings |

### 自定义设置

| 参数（--）        | 类型    | 默认值 | 说明                                                         |
| ----------------- | ------- | ------ | ------------------------------------------------------------ |
| **min_snr_gamma** | *float* |        | gamma for reducing the weight of high loss timesteps. Lower numbers have stronger effect. 5 is recommended by paper. |

support_weighted_captions

| 参数（--）            | 类型 | 默认值 | 说明                                                         |
| --------------------- | ---- | ------ | ------------------------------------------------------------ |
| **weighted_captions** |      | False  | Enable weighted captions in the standard style (token:1.3). No commas inside parens, or shuffle/dropout may break the decoder. |

### 其他参数

| 参数（--）                          | 类型    | 默认值                                           | 说明                                                         |
| ----------------------------------- | ------- | ------------------------------------------------ | ------------------------------------------------------------ |
| **no_metadata**                     |         |                                                  | do not save metadata in output model                         |
| **save_model_as**                   | *str*   | safetensors，[None, "ckpt", "pt", "safetensors"] | format to save the model (default is .safetensors)           |
| **unet_lr**                         | *float* |                                                  | learning rate for U-Net                                      |
| **text_encoder_lr**                 | *float* |                                                  | learning rate for Text Encoder                               |
| **network_weights**                 | *str*   |                                                  | pretrained weights for network                               |
| **network_module**                  | *str*   |                                                  | network module to train                                      |
| **network_dim**                     | int     |                                                  | network dimensions (depends on each network)                 |
| **network_alpha**                   | *float* | 1                                                | alpha for LoRA weight scaling, default 1 (same as network_dim for same behavior as old version) |
| **network_args**                    | *str*   | *nargs*="*"                                      | additional argmuments for network (key=value)                |
| **network_train_unet_only**         |         |                                                  | only training U-Net part                                     |
| **network_train_text_encoder_only** |         |                                                  | only training Text Encoder part                              |
| training_comment                    | *str*   |                                                  | arbitrary comment string stored in metadata                  |
| dim_from_weights                    |         |                                                  | automatically determine dim (rank) from network_weights      |



