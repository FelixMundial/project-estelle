# AGENTS(通用模板)

> 面向「游戏文本自带中日双语,制作对照显示 Mod」的项目。新项目将本文件复制为其根目录 `AGENTS.md` 并补全〈项目档案〉;方法论与已知坑见《中日双语文本模组制作经验.md》(引擎无关主干 + 富文本/振假名专项;UE 专属细节另见 FFRS 项目目录《双语文本Mod制作经验(UE通用).md》)。

## Mission

Finish a practical Japanese + Simplified Chinese bilingual text mod (日文原文 + 中文对照) for one game whose text uses standard localization, with low time and token cost. Deliver a usable v1 before pursuing optional enhancements. Avoid over-design, speculative edge cases, and research that does not change the next implementation decision.

## Community research first(动手前先查社区方案;优先级:高)

**本规则优先级高,先于其他工作习惯执行。** 在实际动手尝试新方案(本项目尚未验证过的解包/修改/部署手段、新工具链、新技术路线)之前,必须**先在模组社区**(GameBanana、Nexus Mods、GitHub、相关论坛/贴吧等)**搜索是否已有现成的模组方案、工具或教程**:

- **有** → 以社区先例为起点复用/改造,不重复造轮子,并把出处记入进度记录.md。
- **没有** → **明确告知用户"社区无现成方案"**,告知之后再开始"计划-执行";不得静默自行开干。

判定基准:是否属于"本项目已验证过"的既定路线。同一路线的重跑与既定管线内的常规操作不触发本规则。

## 项目档案(开新项目必填;游戏更新后同步)

- 游戏名 / 平台与 AppID / 安装路径 / 打包时版本。
- 引擎与打包形态(.pak、IoStore `.utoc/.ucas`、是否加密);文本路径与源/目标语言目录名。
- 官方原包 SHA-256 基线(识别游戏更新的依据);`tools/` 既有工具先复用再新增。

## Sources and recovery

For substantial work, follow `工作计划.md` and keep `CHECKPOINT.md` current. Use `进度记录.md` for confirmed project state and the 经验 doc for reusable, verified modding knowledge. Prefer actual files under `workspace/` and `tools/` over old chat summaries. Reuse existing artifacts; do not repeat extraction, downloads, or analysis without a concrete need. On cold start, run the applicability check (经验文档 §3) before any pipeline work.

## Token discipline and research triage

Use deterministic scripts (PowerShell, `rg`, diffing, hashing, CSV processing) for deterministic work. Keep large CSV/JSON/binary output out of the main context; write evidence to files and return concise summaries. Before starting **or resuming** exploratory work, briefly classify its scope and likely cost. An interrupted task is not automatically the next priority. Delegate bounded read-heavy/noisy scans when useful, preferably to cheaper workers; keep ambiguous decisions and final integration with the parent agent. A subagent brief must be **self-contained** (it starts with fresh context and cannot see this conversation): reconnaissance and bounded sweeps fit this bar, mid-task judgment does not. A subagent may write only into its **own sandboxed output area with mechanically verifiable products**, which the parent then reviews; deterministic writes belong in scripts because scripts are re-runnable, idempotent, and byte-verifiable. If remaining work is open-ended, costly, and not a v1 blocker, record it in `CHECKPOINT.md` and defer it.

## Checkpoints and documentation

`CHECKPOINT.md` is the resumable state, not a console transcript: it answers "if we stop now, where do we resume?". Update it after meaningful milestones and before costly/risky branches when practical. At stable milestones, update `进度记录.md` with confirmed project-specific state. Update the 经验 doc only for verified, reusable lessons. Never promote unverified hypotheses into canonical docs, and never carry a game-specific conclusion into a new game without re-verification.

## Execution and safety

Split substantial work into independent, verifiable work packages. Prefer scripts over agents for bulk processing and avoid trivial subagent fan-out. The parent owns authoritative state, destructive writes, deployment, and final conclusions. Treat shared canonical documents (`AGENTS.md`, `CHECKPOINT.md`, 经验 doc) as **concurrent-edit hazards**: re-read immediately before editing, keep each edit small, and never fan multiple agents or sessions onto the same file at once. Pipeline steps that consume exact byte-level intermediate state must not cross an agent boundary — hand off by re-running scripts, never by natural-language summaries. Project-local tool downloads are acceptable from trusted sources; reuse them and avoid noisy progress output. Ask the user when credentials, licenses, administrator/system changes, or repeated network failures are involved. When a tool download stalls or fails repeatedly (2–3 attempts), stop retrying in loops: record the exact URL, expected file name, target path and current partial-file state in `CHECKPOINT.md` and `进度记录.md`, hand the download to the user (suggest manual download or a mirror/proxy), continue with work that does not depend on the tool, and verify the user-fetched file (size/hash when known) before use. Keep an original game-file backup (outside the game directory) before any replacement; platform integrity checks and game updates will restore official files and invalidate the mod — rerun the pipeline rather than improvising. Release requires a small-footprint distribution: try patch-pak sidecar first, then loader, then a local patcher/installer, decided by cheap discriminating experiments; a full main-file rebuild is a development fallback, not the release form.

## Shelving rule: backup-then-clean the local tools(项目搁置时的"备份-清理")

用户宣布"项目搁置"时,无需再次确认,自动执行:

1. **工具增量备份**:把本项目 `tools/` 下所有工具文件备份到上级公共工具箱 `../tools/`(保持相对路径结构)。公共工具箱中已存在**同名工具文件**即视为已有该工具:跳过、绝不覆盖。备份核查未完成前,不得进行下一步。
2. **清理本地工具副本**:逐文件确认每个本地工具文件在公共工具箱已有同名对应(必要时核对大小)后,删除本项目 `tools/` 整个目录。公共工具箱是工具的唯一长期存放处,项目内不留副本。
3. **记录**:在 CHECKPOINT.md 记录备份/清理结果,并把"从公共工具箱 `../tools/` 复制所需工具回 `tools/`"写进恢复清单第一步;在进度记录.md 记一笔。

恢复项目时,第一步即从公共工具箱复制所需工具;项目内不要长期存放公共工具箱已有的工具。
