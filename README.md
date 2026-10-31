# dsh-purge 1.1.71 归档

上游 `YuJunZhiXue/dsh-purge` 已删库，公开历史只剩 `v1.1.73` 起。本仓库收录社区保存的 **1.1.71**，按分支区分来源。

## 分支

| 分支 | 来源 | 说明 | 文件数 |
| --- | --- | --- | --- |
| `upstream-bruce` | [bruce123897/dsh-purge](https://github.com/bruce123897/dsh-purge) `v1.1.71` | **最接近原版**。cordis.patch.yml / lib/identity.js / lib/core.js 与 ZXEB 版逐字节相同；`package.json` 的 description、homepage、bugs、publishConfig 均指向原作者仓库 | 108 |
| `upstream-zxeb` | [ZXEB/dsh-purge](https://github.com/ZXEB/dsh-purge) `v1.1.71` | 同源代码，仓库元信息已替换为 ZXEB，README 被重写 | 108 |
| `upstream-neko` | [NekoYume517/dsh-purge](https://github.com/NekoYume517/dsh-purge) `v1.1.71-fork.3` | vendor 版，含额外产物 | 107 |
| `upstream-misaka` | [realMisakaMikoto/dsh-purge](https://github.com/realMisakaMikoto/dsh-purge) `v1.1.71` | 已改为适配 DSH 0.2.1-rc.1 / 0.2.0-rc.2（修 #40 complete 收尾块、去掉 `{{cwd}}`），见 `FORK-NOTES.md` | 110 |
| `fixed-cwd` | 本仓库 | `upstream-bruce` + 移植 1.1.81 的 `{{cwd}}` 清洗逻辑 | 108 |
| `main` | 本仓库 | 本说明 | — |

## fixed-cwd 改了什么

DSH 0.2.1 起提示词模板引擎只注册 `provider` / `model`，段落里残留 `{{cwd}}` 会在组装期抛
`unknown prompt variable "{{cwd}}"`，整轮对话失败。

移植自 1.1.81，共 4 处：

1. `lib/identity.js` — 新增 `CWD_STATIC_SUFFIX`、`CWD_TOKEN`、`CWD_TOKEN_TEST`、`CWD_PLAIN`
2. `lib/identity.js` — 新增导出 `cwdVariableSupported(assembled)`：读 `assembled.variables.cwd` 是否存在，可用环境变量 `DSH_PURGE_CWD=variable|static` 强制
3. `lib/identity.js` — 新增 `sanitizeCwdTokens()`，重写时把不支持的 `{{cwd}}` 替换成 `the current workspace`；`overlayOfficialIdentity(sections, cwdSupported)` 同步改造
4. `lib/index.js` / `cordis.patch.yml` — `rewritePromptAssembly` 透传 `assembled`；`personaSuffix` 置空

验证：

```
[no vars]      cwd left: false | plain: true
[with cwd var] kept: true
[force static] cwd left: false
ASSERTIONS DONE
```

## 版本对比：1.1.71 vs 1.1.81

| 项 | 1.1.71 | 1.1.81 |
| --- | --- | --- |
| 文件数 | 108 | 118 |
| `lib/core.js` | 7984 行 | 8203 行 |
| `lib/index.js` | 2597 行 | 2744 行 |
| `client.js` | 8736 行 | 9109 行 |
| 补丁条数 | 82（编号 1–88） | 82（集合完全相同） |

1.1.72–1.1.81 未增删任何补丁，只改实现。1.1.81 新增模块：

- `lib/redteam/evidence-ledger.mjs`（507 行）红队证据台账
- `lib/profile-insert.js`（233 行）用户层 profile 本体 insert，解 `already queued`
- `lib/preset-default.js`（205 行）停用/卸载时清 `selectedDefault`（#108）
- `lib/apply-progress.js`（66 行）Apply 进度
- `lib/mnemon-settings.js`（55 行）
- `lib/default-prompt-inject.zh.md` / `.en.md`
- `core.js` 新增 `healCopiedLauncher` / `looksLikeCopiedBinJs` / `canResolveAppBoot` / `restoreMode` 等（#119 launcher 与 chmod 修复）

## 已知问题

1.1.71 缺少 1.1.81 的 `profile-insert.js`。在宿主里检测不到自己已应用的补丁时，会把自身条目以 `disabled: true` 写进 `profiles/<name>/cordis.patch.yml`，与 `bundles` 中的条目冲突，导致宿主启动失败、无法对话。重启后需手动清理该文件末尾的 `- id: dsh-purge / disabled: true` 段。

## 来源与许可

原项目 MIT，作者 YuJunZhiXue。本仓库仅存档再分发，署名要求照旧，不得商用或付费转售。
