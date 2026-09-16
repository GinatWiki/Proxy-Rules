# Proxy-Rules

自定义分流规则集仓库。供 Open-Box（sing-box 内核）通过「规则集链接(URL)」或手工导入使用。

## 目录

| 子目录 | 用途 |
|---|---|
| `browndust2/` | Brown Dust 2 游戏分流（登录/支付/Google API 走代理，Neon/pmang 直连例外） |

## 文件格式说明

每个规则集目录包含三种格式：

| 文件 | 格式 | 用途 |
|---|---|---|
| `*.list` | `domain:`/`domain-suffix:` 前缀纯文本 | 源清单（人读/编辑用），兼容 OxiDNS `domain_set` 语法 |
| `*.json` | sing-box rule-set 源格式（version 1） | `sing-box rule-set compile` 的输入 |
| `*.mrs` | sing-box 二进制 rule-set | **直接导入目标格式**（Open-Box 面板 / sing-box `rule_set` 的 `format: "binary"`） |

## Browndust2 规则集

### browndust2-proxy（走代理 PROXY）

- `browndust2.global` — 游戏官网/登录/支付（含 signin.browndust2.global）
- `browndust2-db.souseha.com` — 游戏数据库（曾单列）
- `googleapis.com` — Google 登录/服务 API
- `withgoogle.com` — Google 游戏服务

### browndust2-direct（直连例外 DIRECT）

- `neonapi.com` — Neon 登录服务（www.neonapi.com → live-www.neon.pmang.cloud）
- `bd2.pmang.cloud` — 游戏云服务
- `msk.pmang.cloud` — 游戏云服务（mask 相关）

**使用顺序**：direct 规则在 proxy 规则之前匹配（Open-Box 路由规则自上而下）。

## 与 OxiDNS 的对应关系

OxiDNS `/etc/oxidns/config.yaml` 的 `proxy_domains`（决定哪些域名解析结果写进 RouterOS ProxyNet = 代理判定源）已同步加入 proxy 名单的 4 个域：

```yaml
- domain:browndust2.global
- domain:googleapis.com
- domain:withgoogle.com
- domain:browndust2-db.souseha.com
```

direct 名单**不**加进 OxiDNS（不写 ProxyNet = 天然直连）。

## 编译 mrs（更新流程）

改完 `*.json` 后在任意有 sing-box ≥1.8 的环境执行：

```sh
sing-box rule-set compile browndust2/browndust2-proxy.json -o browndust2/browndust2-proxy.mrs
sing-box rule-set compile browndust2/browndust2-direct.json -o browndust2/browndust2-direct.mrs
```

（OpenWrt 上可用 `/opt/open-box/bin/sing-box rule-set compile ...`）

## 原始分流需求（2026-09-16）

```
- DOMAIN,signin.browndust2.global,PROXY
- DOMAIN-SUFFIX,googleapis.com,PROXY
- DOMAIN-SUFFIX,withgoogle.com,PROXY
- DOMAIN,www.neonapi.com,DIRECT
- DOMAIN-SUFFIX,neonapi.com,DIRECT
- DOMAIN-SUFFIX,bd2.pmang.cloud,DIRECT
```
