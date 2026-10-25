# 陌生壬

合肥。日常在 Windows 上用 DSH（DeepSeek Harness）搭工作流，也折腾 macOS（Intel）上的一些国产客户端。

习惯：撞到 bug 就查到底 —— 从日志、数据库、二进制里取证，把「能跑的证据」写清楚，再报给上游。

## 复现材料

报 issue 时，我尽量不只描述现象，而是给出可重跑的取证与对照实验：

📁 **[strange2000/dsh-repro](https://github.com/strange2000/dsh-repro)**

| 编号 | 上游 issue | 一句话 |
|---|---|---|
| 0001 | [slow-stack/mneme #493](https://github.com/slow-stack/mneme/issues/493) | 蒸馏解析失败后确定性无限重试同一窗口；三臂对照实验定位到「转录裸放 user」被模型当成对话续写 |
| 0002 | [Ebony-Vinyl/dsh-our-free-model #427](https://github.com/Ebony-Vinyl/dsh-our-free-model/issues/427) | 应用内升级总是回滚：热重载窗口内无法确认新版本（激活版本不匹配） |
| 0003 | [c1overzz/dsh-bili-scroll #47](https://github.com/c1overzz/dsh-bili-scroll/issues/47) | 桌面版装完即不可用的三处 bug：pythonPath 回落 / 设置互冲 / headless 缺凭证 |

每份材料包含：只读取证脚本（真实数据库 / 日志）+ 可重跑的对照实验 + 最小修法建议。

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