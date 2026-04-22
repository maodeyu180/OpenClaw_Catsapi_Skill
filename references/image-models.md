# 图片生成 / 编辑 模型菜单

## ⚠️ 使用本文件的硬规则

1. 用户想生图/改图时,**必须**先把下面的菜单**原样**展示给用户,让他用数字选。
2. **禁止自己发明菜单、重排顺序、换中文名、换 emoji。** 必须逐字复制。
3. 用户没选之前,**不要**开始调 `--generate`。
4. 选完后,按对应行的参数范围构造命令,有必选参数(比如 `--param size=...`)漏了要问。

## 文生图菜单(用户没传图片时用这份)

> 想画点什么?帮你挑了 6 个当前最好用的模型,选个数字就能开始~
>
> 1. 🤖 **Grok Imagine** — xAI 出品,想象力强、风格大胆,适合创意/抽象题材
> 2. ⚡ **Nano Banana 2** — 最快最便宜,适合批量出稿/试水(支持 512px/1K/2K/4K)
> 3. 🍌 **Nano Banana Pro** — 综合最强,人像/场景/风格都扎实(支持 1K/2K/4K)
> 4. 🎨 **Seedream 4.5** — 字节出品,写实和质感是强项
> 5. 🧠 **GPT Image 2** — OpenAI 系,抽象指令理解最好,支持超大尺寸到 3840x2160
> 6. 🌈 **FLUX.2 [Pro]** — 细节和提示词跟随度最好,适合复杂描述
>
> 选几号?(默认 3,或者发"1/2/3/4/5/6")

选中后对应的 `--model` / 建议参数:

| # | 菜单名 | --model | 常用 --param |
|---|---|---|---|
| 1 | Grok Imagine | `grokImagineImage` | `aspectRatio=16:9` 等(默认 `1:1`,可选 `2:1` / `20:9` / `9:16` / `1:2` 等) |
| 2 | Nano Banana 2 | `nanoBanana2` | `resolution=1K`(默认),可 `512px` / `2K` / `4K`;`aspectRatio=16:9` 等 |
| 3 | Nano Banana Pro | `nanoBananaPro` | `resolution=2K`(推荐) 或 `1K` / `4K`;`aspectRatio=16:9` 等 |
| 4 | Seedream 4.5 | `seedream` | `imageSize=landscape_16_9` / `portrait_16_9` / `square` / `landscape_4_3` / `portrait_4_3` |
| 5 | GPT Image 2 | `gptImage2` | `size=1024x1024`(默认),可 `1536x1024` / `2048x2048` / `3840x2160` 等;`quality=auto`/`low`/`medium`/`high` |
| 6 | FLUX.2 [Pro] | `flux2Pro` | `aspectRatio=square_hd`(默认) / `landscape_16_9` / `portrait_16_9` / `landscape_4_3` 等 |

## 图生图 / 图片编辑菜单(用户传了图片时用这份)

> 收到你的图啦~ 想怎么改?挑一个模式:
>
> 1. 🖊️ **Qwen Image Edit** — 万能改图,"把背景换成...""把衣服改成..." 都能懂
> 2. ✏️ **FLUX.1 Kontext [Edit]** — 精细编辑,保持人物一致性最强
> 3. 🎨 **GPT Image 1.5 [Edit]** — GPT 系编辑,抽象指令理解好
> 4. 🔍 **Magnific Upscaler** — 只想放大/提升细节,不改内容
>
> 选几号?

| # | 菜单名 | --model | --type | 说明 |
|---|---|---|---|---|
| 1 | Qwen Image Edit | `qwenImageEdit` | image | 便宜好用,默认推荐 |
| 2 | FLUX.1 Kontext [Edit] | `fluxKontextEdit` | image | 默认 `pro` 模式,要"最强"质量加 `--param mode=max` |
| 3 | GPT Image 1.5 [Edit] | `gptImage15Edit` | image | 支持多张图一次编辑(最多 5 张) |
| 4 | Magnific Upscaler | `magnific` | image | 只放大,用 `--param scaleFactor=2x` 等 |

## 其他可调用但不放入菜单的图片模型

当用户明确点名或者上面的菜单不合适时可以用:

| model_key | 名字 | 场景 |
|---|---|---|
| `gptImage15` | GPT Image 1.5 | GPT 系上一代 |
| `nanoBanana` | Nano Banana | 旧版,Pro 出来后一般不推荐 |
| `midjourney` | Midjourney | 欧美大片质感,**num 固定为 1** |
| `flux2` | FLUX.2 | Pro 的标准版,更便宜 |
| `fluxKontext` | FLUX.1 Kontext | 文生图用 |
| `fluxProUltra` | FLUX 1.1 [pro] Ultra | 高细节 |
| `dreamina` | Dreamina | 字节 Dreamina |
| `imagineArt` | Imagineart 1.5 | |
| `hunyuanImage` | 混元 Image 3.0 | 腾讯混元 |
| `imagen` | Imagen 4 | Google |
| `recraft` / `recraftSvg` | Recraft / Recraft SVG | 矢量风格必选 |
| `ideogram` / `ideogramCharacter` | Ideogram 3.0 / Character | 文字渲染 / 角色一致性 |

## 调用示例

```bash
# 用户选了 3 号 Nano Banana Pro,提示词"星空下的黑猫",默认 2K
python3 {baseDir}/scripts/catsapi.py --generate --type image \
  --model nanoBananaPro --prompt "星空下的黑猫, 电影感, 4K" \
  --param resolution=2K --num 1 \
  -o /tmp/openclaw/catsapi-output/cat_$(date +%s).png
```

成功后脚本会输出:

```
COST:14
OUTPUT_FILE:/tmp/openclaw/catsapi-output/cat_1714xxxxxx.png
```

走 `message` 工具把那个文件发给用户,然后自然地跟一句:"完成啦~花了 14 猫币,要不要再出一张或者做成视频?"
