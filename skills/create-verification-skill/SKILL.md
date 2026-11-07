---
name: create-verification-skill
description: "生成项目本地的 verification skill，像用户一样驱动应用——任意语言、框架或平台。用于 /create-verification-skill、「给这个仓库做控制 skill」，或项目没有脚本化方式证明 UI/CLI/服务行为时。"
disable-model-invocation: true
---

# Create a verification skill

严肃的项目都需要脚本化方式驱动真实应用并证明行为：启动它、像用户那样操练特性、并捕获证据。本 skill 在仓库中生成项目本地 skill（`.cursor/skills/verify-<app>/`）。你写的是给下一个 agent 看的输出，不是给人看的：它会在任务中途被 agent 冷不防地打开阅读，由从未见过该应用的 agent 使用。

## 1. Interview the repo, not the user

从代码库回答下列问题，只有观察不到的才问用户：

- **Surface：** 用户实际接触什么？Web UI、CLI/TUI、桌面应用、API、移动应用、库？仓库可能有多类；选主要的一个并注明其余。
- **Run：** 应用如何在本地启动？优先用仓库自文档化的 dev 命令（package scripts、Makefile、README quickstart）。记下端口、环境变量、种子数据、auth。
- **Drive：** agent 如何以编程方式与之交互？先看现有 harness——Playwright/Cypress spec、expect 脚本、PTY 辅助、curl 可达端点、debug 端口。再选通用配方：Web/Electron 用浏览器/CDP，CLI/TUI 用 tmux/PTY harness，服务用纯 HTTP。
- **Observe：** 能捕获什么证据？截图、终端 transcript、响应体、日志、退出码、DB 状态。
- **Isolate：** 能否并排跑两个实例（端口、数据目录、profile）？若不能，在生成的 skill 中说明：拒绝双开共享实例好过搞坏用户会话。

若 checkout 原样无法构建或启动，先生成前修好（或精确报告）；对着坏底座写的 skill 会教错步骤。当无关缺失资源阻塞启动（API 从不服务的静态目录、示例配置）时，生成的 skill 可创建它，明确标为 verification scaffolding，并在 cleanup 中移除。

## 2. Generate the skill

编写 `.cursor/skills/verify-<app>/SKILL.md`，含 YAML frontmatter（`name: verify-<app>` 以及点名应用、surface、何时动用的 `description`——没有 frontmatter 则 skill 不会注册），以及下列章节，每节都基于访谈实际发现（不留占位符）：

- **Launch：** 为验证启动应用的精确命令，以及如何判定 ready（日志行、prompt）。启动须从空数据目录达到可用状态，含迁移与种子数据。ready 指可用，不只是进程有响应。DB 健康检查可通过而 init 脚本仍在跑。包含 teardown。短生命周期 CLI 或 TUI 没有要保活的服务：launch 指构建二进制（或装依赖）一次，每次 drive 在独立 PTY 或 tmux 会话中启动。
- **Doctor：** 一次只读检查，回答「这个实例值得 drive 吗？」——进程在跑、版本/build 对、端口归我们、auth 有效。一旦情况不对，agent 先跑它。
- **Drive：** 本仓库真实 selector/命令的 harness 配方，不要示例。优先稳定句柄（ARIA label、data attribute、prompt 字符串、路由路径），而非坐标和 tab 顺序。
- **Evidence：** 证明要捕获什么、存到哪里。写明证明标准：走真实用户路径，而非内部 setter 或仅测试端点；捕获动作与结果状态，而非仅最终画面；与可见结果一并验证副作用（写文件、插行、发消息）；仅在生产边界已隔离外部系统处用 mock。当安全路径是 dry-run 或 test mode 时，通过观察（文件、网络、git ref）核实它实际跳过了什么，而非信其名：有些 dry-run 仍会触网或开浏览器。
- **Cleanup：** 如何拆掉本次 run 创建的实例。不要按进程名杀；只杀你启动的。cleanup 移除实例与 scratch 状态，不移除证据：证明 artifact 在 teardown 后仍在 skill 指明的位置。
- **Helpers：** skill 附带的脚本须可执行，调用方式写在 skill 正文。读者得反推的 helper 不算 helper。

## 3. Seed the feature map

创建 `.cursor/skills/verify-<app>/features/README.md`，以及每个可识别的用户向特性各一文件（起步先覆盖 3–5 个，来自路由、命令、菜单或文档）。遵循 [`references/feature-map-example/`](references/feature-map-example/) 的形态：README 索引、每特性一文件。每文件从用户视角回答：特性是什么、如何到达、如何用 harness drive、何种可观察终态证明有效。四个 H2 为 `Sub-features`、`How to get to it (user POV)`、`Driving it with <harness>`、`Gotchas`。若种子数据缺少某旅程所需前置条件，旅程报告失败，原因写入该特性的 `Gotchas`。不要为通过而削弱旅程。地图是仓库维护的验证源；地图列出多个入口而证明只走一个方便入口，则不完整。

## 4. Prove the generated skill before handing it over

按自身说明端到端跑一遍：launch、doctor、drive 一个已映射特性（一个就够；地图供后续 run 覆盖其余）、捕获证据、cleanup。cleanup 后确认证据仍在命名位置——吃掉证明的 cleanup 会让本步失败。修失败处，每次失败迭代后也跑生成的 cleanup，以免坏尝试遗留进程与端口。从未执行过的生成 skill 是草稿，不是交付物。若在真实目标的替身上演练（容器代替新 VM、本地栈代替远程栈），列出替身做了而目标不会做的每一步 setup。在真实目标上跑通之前，这些步骤仍属未验证。

## 5. Offer the maintenance loop

指向 `/maintain-verification-skill`，随应用变化保持地图诚实。仅当用户问起时再建议节奏。
