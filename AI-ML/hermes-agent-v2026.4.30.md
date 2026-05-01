# Hermes Agent v0.12.0 — The Curator Release

**Date:** April 30, 2026
**Commit:** `73bf3ab`
**Stats:** 1,096 commits, 550 merged PRs, 217,776 insertions, 213 contributors

## Highlights

### Autonomous Curator (新增)
- `hermes curator` 作为后台 agent 运行，默认每 7 天调度一次
- 对 skill 进行 grading、pruning、consolidation
- 报告输出到 `logs/curator/run.json` 和 `REPORT.md`
- 保护机制：bundled/hub skills 不会被 mutation
- `hermes curator status` 按使用量排名 skills
- **你的 curator 已配置:** `minimax-cn` + `MiniMax-M2.7`

### Self-Improvement Loop (升级)
- 背景 review fork 大幅升级：rubric-based、active-update biased
- 现在正确处理 `references/` 和 `templates/` 子文件
- 继承父进程的 runtime（provider、model、credentials）
- 限定在 memory + skills toolsets，防止扩散

### Skill 集成扩展
- **ComfyUI v5:** 从可选变为内置默认，带 CLI + REST + hardware-gated 本地安装
- **TouchDesigner-MCP:** 默认内置，新增 GLSL、post-FX、audio、geometry、9 个新参考文档
- **Humanizer skill:** 去除 AI 写作风格的新 skill
- 其他：claude-design、design-md、Airtable salvage、skill_manage 支持 external_dirs 等

### 新增 Inference Providers
- **LM Studio:** 升级为一级原生 provider，支持 reasoning transport + 实时 `/models` 列表
- **GMI Cloud** (新)
- **Azure AI Foundry** (新，自动检测)
- **MiniMax OAuth** (新，PKCE 浏览器流程)
- **Tencent Tokenhub** (新)

### 新增 Messaging Platforms
- **Microsoft Teams:** 第一个插件形式发布的 platform
- **Tencent 元宝 (Yuanbao):** 第 18 个原生 messaging platform，支持 text + media
- Gateway 现在是 plugin host，支持 drop-in messaging adapters

### Native 集成
- **Spotify:** Native tools (play、search、queue、playlists、devices)，PKCE OAuth，setup wizard，bundled skill
- **Google Meet:** Plugin 可 join calls、transcribe、speak、follow up

### CLI & UX
- **`hermes -z` one-shot mode:** `hermes -z <prompt>` 非交互执行
- **`hermes update --check`:** 更新前预检，opt-in `HERMES_HOME` 备份
- **Models Dashboard:** 新 tab，含 per-model analytics 和 in-browser 模型配置
- **Remote Model Catalog:** OpenRouter + Nous Portal 模型目录从远程 manifest 拉取，新模型无需发版即可出现
- **Native Multimodal Image Routing:** 图片根据模型实际 vision 能力路由
- **TUI 改进:** LaTeX 渲染、`/reload` .env 热重载、opt-in auto-resume 上次 session、light-terminal 自动检测、`/mouse` 开关 ConPTY phantom mouse injection

### Observability & Plugins
- **Langfuse Observability:** bundled plugin
- **Hermes Achievements:** bundled plugin

## 你的配置状态

| Auxiliary Slot | Provider | Model | 状态 |
|---|---|---|---|
| curator | minimax-cn | MiniMax-M2.7 | ✅ 已配置 |
| skills_hub | minimax-cn | MiniMax-M2.7 | ✅ 已配置 |
| mcp | minimax-cn | MiniMax-M2.7 | ✅ 已配置 |
| title_generation | minimax-cn | MiniMax-M2.7 | ✅ 已配置 |
| vision | gemini | gemini-2.5-flash | ✅ |
| web_extract | gemini | gemini-2.5-flash | ✅ |
| compression | kimi-coding-cn | kimi-k2.5 | ✅ |
| session_search | kimi-coding-cn | kimi-k2.6 | ✅ |
| approval | kimi-coding-cn | kimi-k2-thinking | ✅ |
| flush_memories | gemini | gemini-2.5-flash | ✅ |

## 需要关注的事项

1. **Autonomous Curator 首次运行**: curator 已配置为每 7 天运行，你也可以手动触发 `hermes curator run`
2. **Hermes Update Merge Conflict**: 已知模式 — `hermes update` 后 `toolsets.py` 会被覆盖，custom tools 会消失，需手动 rebase
3. **ComfyUI/TouchDesigner-MCP**: 现在默认内置，如不使用可忽略
4. **Spotify/Google Meet**: 新集成，如需用需要额外配置 OAuth
5. **TUI 冷启动优化**: 约 57% 提升，如遇 TUI 问题可反馈

## 参考
- Release: https://github.com/NousResearch/hermes-agent/releases/tag/v2026.4.30
- Docs: https://hermes-agent.nousresearch.com/docs/
