# 视频生成模型菜单

## ⚠️ 使用本文件的硬规则

1. 用户想生视频时,**必须**先把下面菜单**原样**展示,等用户选。
2. 生视频之前,先 `message` 发"开始生成啦,视频一般要 1-5 分钟,请稍等～🎬",再 `exec` 脚本。
3. `--num` 对视频**必须**为 1。
4. 不要擅自发明菜单顺序或名字。

## 通用菜单(文生视频 / 带起始帧的图生视频都用这份)

> 视频来啦~ 挑个喜欢的模型:
>
> 1. 🚀 **Wan 2.5** — 性价比之王,1080p 又快又稳,默认推荐
> 2. 🎬 **Veo 3.1** — Google 出品,电影感拉满,价高质优
> 3. 🎯 **Kling AI v3** — 可灵,人物运动最自然,拍人物首选
> 4. 🌱 **Seedance 2.0** — 字节最新,最长 15 秒,细节超棒
> 5. 🌊 **Hailuo 2** — Minimax 海螺,速度快画面细腻
> 6. ✨ **Sora 2** — OpenAI Sora,想象力天花板(贵)
>
> 选几号?(默认 1,或者发"1/2/...")

选中后对应的 `--model` / 必填参数 / 限制:

| # | 菜单名 | --model | 常用 --param | 备注 |
|---|---|---|---|---|
| 1 | Wan 2.5 | `wan25` | `resolution=1080p` / `480p`、`duration=5`/`10`、`aspectRatio=16:9` | 最平衡 |
| 2 | Veo 3.1 | `veo31` | `resolution=1080p`,时长通常 5-8s | 价格高,先预览成本 |
| 3 | Kling AI v3 | `klingAiV3` | `duration=5`/`10`、`aspectRatio=16:9` | 支持起始帧图生视频 |
| 4 | Seedance 2.0 | `seedance20` | `resolution=1080p`、`duration=5`/`10`/`15`、`aspectRatio=16:9` | 最长 15s + 支持真人 |
| 5 | Hailuo 2 | `minimax` | `resolution=1080p`、`duration=6`/`10` | 速度快 |
| 6 | Sora 2 | `sora` | `resolution=1280x720` 或 `720x1280`、`duration=4`/`8`/`12` | 很贵,**务必先预览成本** |

## 其他可调用的视频模型

| model_key | 名字 | 场景 |
|---|---|---|
| `grokImagineVideo` | Grok Imagine Video | xAI,便宜,想象力强 |
| `wan` | Wan 2.2 | 旧版 Wan,2.5 不够时备选 |
| `klingAiO3` | Kling O3 | 可灵 O3 |
| `klingAiV26` | Kling 2.6 | Kling 2.6 标准 |
| `klingAiV26Motion` | Kling 2.6 Motion Control | 运动控制专用 |
| `seedance15Pro` | Seedance 1.5 Pro | 旧版 |
| `seedancePro` | Seedance Pro | |
| `lumaLabs` | Luma Labs | Luma Dream Machine |
| `runway` | Runway | Runway Gen |
| `topaz` | Topaz Upscaler | **视频 4K 升级专用**,不是生视频 |

## 图生视频提示

如果用户传了图片想做成视频:

- **纯起始帧** → 任意模型 + `--start-frame /path/to/frame.png`
- **首尾帧** → 优先 Kling 家族,加 `--start-frame` 和 `--end-frame`
- **真人 / 人物动作** → 首选 `seedance20` 或 `klingAiV3`(运动自然)

## 调用示例

```bash
# 步骤 1: 告诉用户要等
# (通过 message 工具发: "开始生成啦,视频一般 1-5 分钟,稍等～🎬")

# 步骤 2: 先预览成本(可选,贵的模型强烈建议)
python3 {baseDir}/scripts/catsapi.py --cost \
  --type video --model wan25 \
  --resolution 1080p --duration 5 --num 1

# 步骤 3: 执行
python3 {baseDir}/scripts/catsapi.py --generate --type video \
  --model wan25 --prompt "a ginger cat walking through a sunflower field, cinematic" \
  --param resolution=1080p --param duration=5 --param aspectRatio=16:9 \
  -o /tmp/openclaw/catsapi-output/cat_$(date +%s).mp4
```

脚本完成后输出:

```
COST:6
OUTPUT_FILE:/tmp/openclaw/catsapi-output/cat_xxxxxxxx.mp4
```

走 `message` 工具交付视频文件,然后:"视频出炉啦~ 花了 6 猫币,要不要剪个长一点的版本或者升到 4K?"

## 慢任务兜底

如果 `--generate` 因为超时(默认 15 分钟)而报错退出,会打印出 TASK_ID。用 `--status TASK_ID` 继续查:

```bash
python3 {baseDir}/scripts/catsapi.py --status TASK_ID
```

返回的 JSON 里 `status=="completed"` 就拿 `result_video.url` 手动 `curl -o` 下载。
