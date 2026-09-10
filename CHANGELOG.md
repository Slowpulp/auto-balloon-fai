# Changelog

## [2.0.0] - 2026-09-11

### Changed

- 将每次任务默认定义为相互隔离的 `independent` 新项目。
- 禁止通过跨项目搜索、跨会话记忆、相似文件名或相同哈希自动发现并继承基线。
- 只有用户在当前任务中明确指定基线并要求继承时，才启用 `revision` 模式。
- 在继承历史 ID 前增加当前范围与硬禁区合规审计；未解决冲突保持 `HOLD`。
- 把页面边界/非零引线与硬禁区检查定义为具有独立证据记录的质量门禁。
- 明确独立项目首次对外交付版次从 `V1` 开始，修订版次只依据用户指定基线递增。
- 将 V2 并入原 `Slowpulp/auto-balloon-fai` 仓库；仓库 `main` 指向 V2，原 V1 通过 `v1.0.0` 标签保留。
- 更新 V1、V2 并行安装说明和 GitHub 克隆地址。

### Compatibility

- V2 使用独立 skill 名称 `auto-balloon-fai-v2` 和调用名 `$auto-balloon-fai-v2`。
- 原始 `auto-balloon-fai` skill 保持不变，可与 V2 同时安装。
