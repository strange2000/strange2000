# 陌生壬

🌐 落地页：**[strange2000.github.io](https://strange2000.github.io/)**

合肥。日常在 Windows 上用 DSH（DeepSeek Harness）搭工作流，也折腾 macOS（Intel）上的一些国产客户端。

习惯：撞到 bug 就查到底 —— 从日志、数据库、二进制里取证，把「能跑的证据」写清楚，再报给上游。

## 复现材料

报 issue 时，我尽量不只描述现象：给出可重跑的取证、能判定的审计器，以及最小修法建议。

📁 **[strange2000/dsh-repro](https://github.com/strange2000/dsh-repro)** —— 21 条已报问题的索引，按上游项目分组

| 上游项目 | 条数 | 可运行的材料 |
|---|---|---|
| [slow-stack/mneme](https://github.com/slow-stack/mneme/issues?q=is%3Aissue+author%3Astrange2000) | 8 | 保留期审计器 · T1 入口审计器 · 取证脚本 + 三臂对照 |
| [c1overzz/dsh-bili-scroll](https://github.com/c1overzz/dsh-bili-scroll/issues?q=is%3Aissue+author%3Astrange2000) | 5 | 只读审计器（8 项判定：判重表 / 白名单 / 当日上限） |
| dsh-web · dsh-archive-manager | 3 | `settings.section` 共享 id 冲突扫描器 |
| dsh-desktop · dsh-ntr · dsh-our-free-model | 5 | 应用内升级激活审计器 |

材料是**只读取证脚本或审计器**（真实数据库 / 日志 / 安装目录，零依赖、不写盘），自带 `--selftest`：先用「缺陷形状」与「修复后形状」的合成源码验证判定器分得开，再给结论。所以它回答的不是「怎么复现一次」，而是「**我这份版本还会不会这样**」—— 插件降级、或换一台还在旧版本的机器，一条命令就能确认缺陷有没有回来。

另有 6 份报告，其中 0004–0006 讲结构性问题而非单条：共享 `settings.section` id 没有契约、两个「不抛错」的缺陷、三个「静默降级」。
## 协议 / 工具

📁 **[strange2000/mneme-dual-boot](https://github.com/strange2000/mneme-dual-boot)** · MIT

一台笔记本双系统轮流开机，共享盘上只放一份 SQLite 库 —— 不是同步，是**可信交接**：进之前先验，出之前先封，凡是没封的一律不信。

协议规范 + 恢复手册 + 零依赖参考实现 `handoff.mjs`（`open` / `close` / `status` / `recover`），附 Windows 与 macOS 在同一块 exFAT 卷上的实测数据。三个跨端差异都写清了：mtime 粒度（2 s vs 10 ms）、符号链接（不支持 vs 可以建）、`chmod`（两端都无效，macOS 上更是彻底空操作）。还有一个「WAL 凭空消失」的悬案也已查清 —— 根因是 V8 的 GC 回收了数据库句柄，不是文件系统劫持了写入。

## 我报 issue 的三条纪律

1. **只读取证**：先证明事实，再猜原因。审计表、游标、日志逐条对齐，不做「大概是」的推断。
2. **对照实验**：把「环境差异」和「代码缺陷」分开。改一个变量跑一次，用结果说话。
3. **给最小修法**：指出代码位置与判据，而不是把问题原样丢回给维护者。

## 环境

- Windows 11 + DSH Desktop（主）
- macOS（Intel）+ 若干客户端实测