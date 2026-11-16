# neoskills 中文阅读笔记

> 作者：十三（GitHub: makunhao）· 2026-11-16
> 基于 `main` 分支 v0.4.2 的源码阅读；这是一份阅读笔记，不修改原仓库代码。

## 一句话

neoskills 是一个「Homebrew 式」的 AI Agent 技能包管理器：用 git tap 管理来源、用符号链接把技能部署到 Claude Code / OpenCode / OpenClaw，并用可选的 `ontology.yaml` 属性图管理技能关系与生命周期。

## 结构速记

| 目录 | 作用 |
|---|---|
| `src/neoskills/core/` | Cellar（工作区）、Tap（git 仓库）、Linker（符号链接）、Checksum |
| `src/neoskills/ontology/` | 属性图：models / graph / loader / writer / engine / taxonomy / lifecycle |
| `src/neoskills/adapters/` | 不同 agent 的路径与格式适配 |
| `src/neoskills/cli/` | 18 个 Click 命令，懒加载 |
| `skills/` | 自带 2 个技能：bank-status、skill-dedup |

## 三个值得学的设计

1. **渐进增强**：只有 `SKILL.md`（L0）也能用；`ontology.yaml` 是可选增量。
2. **用文件系统推导状态**：`Linker` 不维护 `state.yaml`，链接状态从文件系统读。
3. **查重先定义"什么算同一个技能"**：校验和排除 `metadata.yaml`、`__pycache__` 等生成文件。

## 三条阅读中发现的改进点

1. `skills/skill-dedup/scripts/dedup_scan.py` 顶部导入 `neoskills.mappings.resolver` 与 `neoskills.bank.store`，这两个模块在 v0.4 已不存在；`ImportError` 被吞掉，集成代码永远不生效。建议删除或改指向 `core/`。
2. `core/linker.py` 直接调用 `symlink_to`，Windows 无开发者模式时会失败；建议增加复制/junction 的降级路径。
3. `pyproject.toml` 是 0.4.2，而 `CLAUDE.md` / `TASKS.md` 写 0.4.1；建议用 `importlib.metadata` 做单一版本源。

## 参考

- 上游仓库：https://github.com/neolaf2/neoskills
- 许可证：MIT（本笔记不包含上游代码，仅事实摘要与建议）

## 复现

```bash
git clone https://github.com/makunhao/neoskills.git
cd neoskills
git checkout c5a-cn-notes
```
