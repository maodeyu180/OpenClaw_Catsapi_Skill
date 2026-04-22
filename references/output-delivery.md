# 输出交付与错误兜底

## 核心原则

1. **所有媒体文件**(图/视频)**必须通过 `message` 工具送达**。不要在回复正文里:
   - 打印文件路径让用户自己去拷
   - 用 `![](/tmp/...)` 这种 markdown,用户端无法渲染
   - 贴 `https://catsapi.com/uploads/...` 这种内部 URL(鉴权/域名私有,用户打不开)
2. 交付成功后回复 `NO_REPLY`,或者补一句短跟进:"还要来一张吗?"
3. 交付失败(`message` 工具报错)→ 重试 1 次。还是失败就在正文里 `OUTPUT_FILE: /tmp/...` 并说明,让用户手动拷。

## 脚本输出格式约定

`scripts/catsapi.py --generate` 成功时 **stdout** 会打印:

```
COST:<猫币数>
OUTPUT_FILE:<本地路径>
OUTPUT_FILE:<本地路径>   # 多图时会有多行
```

**stderr** 打印进度日志(`[INFO]` / `[POLL]` / `[OK]`),只是给 agent 看的,不要复述给用户。

## 常见错误分支

### `HTTP 400 猫币不足,需要 X 猫币`

友好提示:
> "哎呀,余额不够啦 🥺,这次需要 X 猫币。去 [catsapi.com](https://catsapi.com) 用兑换码补一下吗?"

### `HTTP 400 同时运行任务数已达上限`

> "已经有 N 个任务在跑啦,等前面的完事咱再来?或者要我帮你查一下进度?"

然后可以用 `--list` 最近任务不可用,但可以提示用户去网页端看。

### `HTTP 401 / 403`

API Key 无效,读 `api-key-setup.md` 按流程引导重配。

### 分辨率 / 时长不匹配报错

`400 Model not available` 或参数相关 400 → 用 `--info MODEL_KEY` 查一下这个模型允许的 options,
把合法值列给用户让他改一下。

### 轮询超时

脚本默认 15 分钟超时会退出并打出 TASK_ID。告诉用户:
> "渲染还在排队,我先停下等一等。等几分钟再告诉我 `TASK_ID`,或者直接去网页端看作品库。"

然后后续可以用 `--status TASK_ID` 查。

## 下载失败兜底

`_download` 失败通常是:

- catsapi 域名临时不可达 → 用 `--status` 查到结果 URL 后手动 `curl -L -o` 试一次
- 文件特别大(长视频/4K) → 超过脚本 `download timeout=300`,可以在 shell 里直接 `curl -L -o /tmp/openclaw/catsapi-output/xxx.mp4 <result_url>`

## 多图任务的交付

`--num 4` 时 `-o /tmp/.../cat.png` 会被自动拆成 `cat_1.png` ... `cat_4.png`。

脚本会打出多个 `OUTPUT_FILE:` 行,**每一个都要单独调 `message` 工具**发给用户。
如果 `message` 工具支持一次发多附件,优先打包一起发。

## 什么时候直接用文本回复(不用 `message`)

- `--check`(只返回余额)
- `--list`(模型列表)
- `--cost`(费用预览)
- `--status`(状态查询)
- `--info`(参数查询)

这些都是纯文本结果,用中文自然语言包装一层打给用户就行。
