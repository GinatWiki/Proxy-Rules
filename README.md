# Proxy-Rules

自定义分流规则集仓库。供 Open-Box（sing-box 内核）「规则集链接」导入使用。

## ⚠️ 面板导入格式（重要）

Open-Box 面板「规则集链接」按**内容魔数**识别格式：

| 内容 | 面板行为 |
|---|---|
| **Clash 规则行 / 一行一个域名**（纯文本） | ✅ 直接解析，域名/IP 自动拆分编译 |
| **mihomo 的 .mrs**（zstd 压缩二进制） | ✅ zstd 解压后解析 |
| sing-box binary rule-set（.srs 格式） | ❌ 被当 UTF-8 文本解析 → **匹配条目为空** |

**⇒ 导入面板请用 `.list` 文件（Clash 规则行纯文本）。**

## Browndust2 规则集（v3，2026-09-16）

| 文件 | 内容 | 用途 |
|---|---|---|
| `browndust2/browndust2.list` | Clash 规则行（**面板导入用这个**） | 规则集链接 URL |
| `browndust2/browndust2.json` | sing-box 源格式 | 本地编译输入 |
| `browndust2/browndust2.mrs` | sing-box binary（存档，**别导入面板**） | 备用 |

### 名单内容（v3，不含谷歌域名）

```
DOMAIN-SUFFIX,browndust2.global      # 游戏本体/官网/登录（含 signin.）
DOMAIN-SUFFIX,souseha.com            # browndust2-db.souseha.com（数据库）
DOMAIN-SUFFIX,akamaized.net          # 原 Browndust2 站点集既有条目
DOMAIN-SUFFIX,pmang.cloud            # bd2./msk./live-www.neon. 全部子域
DOMAIN,signin.browndust2.global      # 精确匹配（冗余保险，后缀已覆盖）
```

### OxiDNS proxy_domains 对应（已生效）

```
- domain:browndust2.global
- domain:browndust2-db.souseha.com
```

⚠️ 名单扩新后缀时 OxiDNS 侧要同步加 `domain:` 条目，否则 ProxyNet 不写、流量到不了 Open-Box。

## 导入步骤（面板）

1. 分流/站点集 → Browndust2 → 规则集 URL 填 `browndust2.list` 的 raw 地址
2. 保存部署后核对「匹配条目数」> 0
3. 从 Browndust2 站点集手动规则里删除旧条目（已被新名单覆盖）

## 编译 .mrs（更新流程）

```sh
sing-box rule-set compile browndust2/browndust2.json -o browndust2/browndust2.mrs
```

（OpenWrt 上用 `/opt/open-box/bin/sing-box rule-set compile ...`）
