# Proxy-Rules

自定义分流规则集仓库。供 Open-Box（sing-box 内核）「规则集链接」导入使用。

## ⚠️ 面板导入格式（重要，v2 修正）

Open-Box 面板「规则集链接」按**内容魔数**识别格式：

| 内容 | 面板行为 |
|---|---|
| **Clash 规则行 / 一行一个域名**（纯文本） | ✅ 直接解析，域名/IP 自动拆分编译 |
| **mihomo 的 .mrs**（zstd 压缩二进制） | ✅ zstd 解压后解析 |
| sing-box binary rule-set（.srs 格式） | ❌ 被当 UTF-8 文本解析 → **匹配条目为空** |

> v1 的教训：`sing-box rule-set compile` 产出的二进制虽然扩展名叫 .mrs，但它是 sing-box 格式（魔数 `SRS\x01`），**不是** mihomo zstd .mrs（魔数 `28 B5 2F FD`）——面板解析为 0 条。

**⇒ 导入面板请用 `.list` 文件（Clash 规则行纯文本）。**

## 目录

| 子目录 | 用途 |
|---|---|
| `browndust2/` | Brown Dust 2 游戏分流 |

## Browndust2 规则集（v2，2026-09-16）

**设计决策（用户确认）**：原来 Browndust2 站点集里的全部域名 + 本次的 DIRECT 域名（neonapi/pmang）**统一走代理**，不维护单独 direct 集。

| 文件 | 内容 | 用途 |
|---|---|---|
| `browndust2.list` | Clash 规则行（**面板导入用这个**） | 规则集链接 URL |
| `browndust2.json` | sing-box 源格式 | 本地编译输入 |
| `browndust2.mrs` | sing-box binary（存档，**别导入面板**） | 备用 |

### 名单内容（7 条）

```
DOMAIN-SUFFIX,browndust2.global      # 游戏本体/官网/登录（含 signin.）
DOMAIN-SUFFIX,googleapis.com         # Google 服务 API
DOMAIN-SUFFIX,withgoogle.com         # Google 游戏服务
DOMAIN-SUFFIX,souseha.com            # browndust2-db.souseha.com（数据库，整域后缀）
DOMAIN-SUFFIX,akamaized.net          # 原 Browndust2 站点集既有条目
DOMAIN-SUFFIX,pmang.cloud            # bd2./msk./live-www.neon. 全部子域
DOMAIN,signin.browndust2.global      # 精确匹配（冗余保险，后缀已覆盖）
```

### 与原 Browndust2 站点集的对应

原 route[17]/dns[14]（面板站点集）：`browndust2-db.souseha.com`、`akamaized.net`、`msk.pmang.cloud`、`bd2.pmang.cloud` —— 本名单用 `souseha.com` + `pmang.cloud` 整域后缀**完全覆盖并放宽**（neonapi 的 CNAME live-www.neon.pmang.cloud 也命中）。

### OxiDNS proxy_domains 对应（已生效）

```
- domain:browndust2.global
- domain:googleapis.com
- domain:withgoogle.com
- domain:browndust2-db.souseha.com
```

⚠️ 若名单扩了新后缀（如整个 pmang.cloud / akamaized.net 要走代理），**OxiDNS 侧要同步加**，否则 ProxyNet 不会写入对应 IP，mangle 不打标，流量到不了 Open-Box。

## 导入步骤（面板）

1. 分流/站点集 → Browndust2 → 规则集 URL 填 `browndust2.list` 的 raw 地址
2. 保存部署后核对「匹配条目数」> 0
3. 从 Browndust2 站点集手动规则里删除旧的 4 条（已被新名单覆盖）

## 编译 .mrs（更新流程）

```sh
sing-box rule-set compile browndust2/browndust2.json -o browndust2/browndust2.mrs
```

（OpenWrt 上用 `/opt/open-box/bin/sing-box rule-set compile ...`）
