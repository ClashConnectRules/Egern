# Egern 配置文件

<p align="center">
  <img src="https://img.shields.io/badge/Egern-1-blue?style=flat-square" alt="Egern">
  <img src="https://img.shields.io/badge/License-MIT-green?style=flat-square" alt="License">
  <img src="https://img.shields.io/badge/Platform-iOS%20%7C%20macOS-lightgrey?style=flat-square" alt="Platform">
</p>

<p align="center">
  <b>高级 Egern 代理配置</b><br>
  <i>智能分流 · 广告拦截 · 流媒体解锁 · AI 服务优化</i>
</p>

<p align="center">
  <a href="README_zh.md">简体中文</a> | <a href="README.md">English</a>
</p>

---

## 功能特性

- **智能 DNS** — Bootstrap + DoH 上游，按域名智能转发
- **广告拦截** — AdBlack DNS + 拒绝规则集双重拦截
- **地区分流** — 8 个地区组，自动过滤订阅节点
- **流媒体解锁** — Netflix、Disney+、YouTube、TikTok、Bilibili
- **AI 优化** — OpenAI、Claude、Gemini、Bing 专用路由
- **协议支持** — SS、Trojan、Vless、VMess、Hysteria 2、TUIC、WireGuard

## 快速开始

1. 下载 [`egern.yaml`](https://raw.githubusercontent.com/ClashConnectRules/Egern/refs/heads/main/egern.yaml)
2. 打开 **Egern** > **配置文件** > **导入**
3. 将 `AllServer` 中的 `https://your-subscription-url` 替换为你的订阅链接
4. 完成 — 地区组通过 `flatten: true` 自动拉取节点

---

## 目录

- [基础设置](#基础设置)
- [DNS 配置](#dns-配置)
- [策略分组](#策略分组)
- [规则优先级](#规则优先级)
- [支持的协议](#支持的协议)
- [MITM 主机名](#mitm-主机名)
- [安装](#安装)
- [自定义图标](#自定义图标)
- [规则来源](#规则来源)
- [致谢](#致谢)
- [许可证](#许可证)

---

## 基础设置

| 设置项 | 值 | 说明 |
|:-------:|:-----:|:-----------|
| IPv6 | `true` | 已启用 |
| VIF Only | `true` | 虚拟接口模式 |
| DNS 劫持 | `*:53` | 劫持所有 DNS 查询 |
| GeoIP | [Masaiki GeoIP2-CN](https://github.com/Masaiki/GeoIP2-CN) | 中国 GeoIP 数据库 |
| ASN DB | [P3TERX GeoLite](https://github.com/P3TERX/GeoLite.mmdb) | ASN 数据库 |
| 延迟测试 | `http://wifi.vivo.com.cn/generate_204` | 直连延迟测试 URL |

---

## DNS 配置

<details>
<summary><b>Bootstrap DNS</b></summary>

| 服务器 | 提供商 |
|:------:|:--------:|
| `223.5.5.5` | 阿里云 |
| `119.29.29.29` | 腾讯 |
| `1.12.12.12` | 阿里云（新） |
| `120.53.53.53` | 字节跳动 |
| `2400:3200::1` | CNNIC (IPv6) |

</details>

<details>
<summary><b>DoH 上游</b></summary>

| 名称 | 服务器 |
|:----:|:--------|
| `AdBlack` | `quic://dns.adguard-dns.com`、`https://dns.adguard-dns.com/dns-query` |
| `Alibaba` | `223.5.5.5`、`https://dns.alidns.com/dns-query` |
| `Tencent` | `119.29.29.29`、`https://doh.pub/dns-query` |
| `ByteDance` | `180.184.2.2`、`180.184.1.1` |
| `China` | 阿里云 + 腾讯组合 |
| `Global` | Cloudflare、Google DNS |

</details>

<details>
<summary><b>DNS 转发规则</b></summary>

| 匹配 | 目标 | 说明 |
|:-----:|:------:|:-----------|
| `proxy_rule_set`（拒绝列表） | AdBlack | 广告过滤 DNS |
| `proxy_rule_set`（阿里云） | Alibaba | 阿里服务 |
| `proxy_rule_set`（腾讯） | Tencent | 腾讯服务 |
| `proxy_rule_set`（抖音） | ByteDance | 字节服务 |
| `proxy_rule_set`（Apple） | China | 苹果服务 |
| `proxy_rule_set`（ChinaMax） | China | 国内域名 |
| `proxy_rule_set`（Global） | Global | 国际域名 |
| `*` | Global | 默认兜底 |

</details>

<details>
<summary><b>Host 映射</b></summary>

| 服务 | DNS 服务器 | 说明 |
|:-------:|:----------:|:-----------:|
| 淘宝 / 天猫 / 支付宝 | `223.5.5.5` | 阿里服务 |
| 京东 / QQ / 微信 | `119.28.28.28` | 腾讯服务 |
| 哔哩哔哩 / 网易 | `119.29.29.29` | 娱乐服务 |
| 小米 | `119.29.29.29` | 小米服务 |
| TestFlight | `8.8.4.4` | Apple TestFlight |
| Google（FCM / 下载） | 特殊 | Google 服务 |
| 路由器管理 | 系统 DNS | 本地路由器 |

</details>

所有 DNS 查询（`*:53`）被劫持以防止泄露。

---

## 策略分组

### 核心分组

| 分组 | 类型 | 描述 |
|:-----:|:----:|:-----------|
| `AllServer` | `external` | 全部订阅节点（自动过滤） |
| `Automatic` | `auto_test` | 地区自动选择（300s 间隔、50ms 容差） |
| `Proxy` | `select` | 代理策略 |
| `NoAuto` | `select` | 主入口 |
| `Mainland` | `select` | 中国大陆直连 |

### 地区分组

所有地区组使用 `flatten: true` + `filter` 从 AllServer 拉取节点，`update_interval: 86400`（每天刷新）。

| 分组 | 过滤关键词 | Emoji |
|:-----:|:---------:|:-----:|
| `Hong Kong` | HK、Hong Kong、HKG、MO | :flag_hk: |
| `Taiwan` | TW、Taiwan、TWN | :flag_tw: |
| `Japan` | JP、Japan、JPN | :flag_jp: |
| `Singapore` | SG、Singapore、SGP、MA | :flag_sg: |
| `United States` | US、USA、States、American | :flag_us: |
| `United Kingdom` | UK、England、Britain | :flag_gb: |
| `Korea` | KR、Korea、KOR | :flag_kr: |
| `Other` | 排除以上地区 | :earth_africa: |

### 服务分组

所有服务组均包含 `AllServer` 作为备选，可手动选择任意订阅节点。

| 分组 | 策略 | 用途 |
|:-----:|:----:|:-------|
| `AI` | Automatic、US、JP、SG、AllServer | ChatGPT、Claude、Gemini、Bing |
| `Apple` | Mainland、HK、US、AllServer | 苹果服务 |
| `Microsoft` | Mainland、HK、SG、US、AllServer | 微软服务 |
| `OneDrive` | Mainland、HK、SG、US、AllServer | 云存储 |
| `Telegram` | Automatic、SG、US、HK、TW、JP、AllServer | 电报 |
| `X` | Automatic、HK、TW、SG、JP、US、AllServer | Twitter / X |
| `WeChat` | Mainland、HK、SG、US、AllServer | 微信 |
| `Netflix` | HK、TW、SG、JP、US、AllServer | Netflix 流媒体 |
| `Disney+` | HK、SG、AllServer | Disney+ 流媒体 |
| `YouTube` | Automatic、HK、TW、SG、JP、US、AllServer | YouTube 流媒体 |
| `TikTok` | TW、SG、JP、US、AllServer | TikTok 解锁 |
| `Bilibili` | Mainland、HK、TW、AllServer | 哔哩哔哩（港台解锁） |
| `Speedtest` | Mainland、Automatic、AllServer | 网速测试 |

### 分组依赖关系

```
AllServer（订阅源）
  └─ 地区组（HK、TW、JP、SG、US、UK、KR、Other）[flatten]
       ├─ Automatic（自动测速）
       └─ 服务组（AI、Apple、Netflix、...）
            └─ 均包含 AllServer 作为备选
```

---

## 规则优先级

```
 1. 规则修正        修复连接 > DIRECT
 2. 广告拦截        SKK 规则集 > REJECT
 3. 隐私保护        阻止追踪器
 4. 国内应用        微信、网易云、B站、微博
 5. Apple 服务      App Store、Apple News、Apple TV
 6. AI 服务         OpenAI、Claude、Gemini、Bing
 7. 流媒体          Disney+、Netflix、TikTok、YouTube
 8. 地区解锁        US、EU、JP、KR、HK、TW 流媒体
 9. 社交媒体        Twitter、Telegram、Facebook、Instagram
10. 其他国外服务    OneDrive、Microsoft、GitHub、Speedtest
11. 国内规则        SKK + ChinaMax 规则集
12. 国外规则        CDN、Global 规则集
13. 本地网络        局域网 > DIRECT
14. GeoIP           CN > Mainland
15. 兜底规则        default > NoAuto
```

---

## 支持的协议

| 协议 | 支持 |
|:---------|:-------:|
| Shadowsocks | 完整 |
| Trojan | 完整 |
| Vless | 完整（支持传输层） |
| VMess | 完整（支持传输层） |
| Hysteria 2 | 完整 |
| TUIC | 完整 |
| SOCKS5 | 完整 |
| HTTP | 完整 |
| WireGuard | 完整 |

---

## MITM 主机名

- `www.google.cn`
- `api.abema.io`
- `*.zhihu.com`
- `sub.store`

MITM 是 URL 重写和请求头重写功能的前提。

---

## 安装

### 方法一：应用内导入

1. 下载 `egern.yaml`
2. **Egern** > **配置文件** > **导入**
3. 选择下载的文件

### 方法二：iCloud 同步

1. 将 `egern.yaml` 保存到 iCloud 云盘
2. **Egern** > **配置文件** > **从 iCloud 导入**
3. 选择文件

### 配置订阅

替换 `AllServer` 外部组中的占位 URL：

```yaml
- external:
    name: AllServer
    type: select
    urls:
      - "https://your-subscription-url"
    filter: '^((?!Remain|Expired|官网|如需|套餐|去除|剩余|距离|Reset|重置|流量).)+$'
    update_interval: 86400
```

只需配置 `AllServer` 的订阅 URL。地区组通过 `flatten: true` 自动从 AllServer 拉取节点。

---

## 自定义图标

每个策略组支持 `icon` 字段：

```yaml
- select:
    name: Telegram
    policies:
      - Automatic
      - Singapore
    icon: "https://example.com/icon.png"
```

图标应为 **PNG 格式**，推荐尺寸 **120x120 px**。

<details>
<summary><b>图标包推荐</b></summary>

| 图标包 | 链接 |
|:------:|:-----|
| Qure（彩色） | [QureColor-All.json](https://raw.githubusercontent.com/Koolson/Qure/master/Other/QureColor-All.json) |
| Orz-3（彩色） | [miniColor.json](https://raw.githubusercontent.com/Orz-3/mini/master/miniColor.json) |
| tugepaopao | [Cute.json](https://raw.githubusercontent.com/tugepaopao/Image-Storage/master/other/Cute.json) |
| Semporia | [Semporia.json](https://raw.githubusercontent.com/Semporia/Hand-Painted-icon/master/Semporia.json) |

</details>

---

## 规则来源

| 来源 | 描述 |
|:------:|:-----------|
| [blackmatrix7](https://github.com/blackmatrix7/ios_rule_script) | 跨平台规则 |
| [Skk.moe](https://ruleset.skk.moe) | SKK 规则集 |
| [VirgilClyne](https://github.com/VirgilClyne/GetSomeFries) | ASN 规则 |
| [Semporia](https://github.com/Semporia/TikTok-Unlock) | TikTok 解锁 |
| [zxfccmm4](https://github.com/zxfccmm4) | Unbreak 规则 |
| [Loyalsoldier](https://github.com/Loyalsoldier/surge-rules) | 拒绝规则集 |

---

## 致谢

- [Egern](https://egernapp.com)
- [blackmatrix7](https://github.com/blackmatrix7)
- [Skk.moe](https://github.com/Skk.moe)
- [lige47/QuanX-icon-rule](https://github.com/lige47/QuanX-icon-rule)

---

## 许可证

[MIT](LICENSE)

---

<p align="center">
  <sub>用心打造更好的网络体验</sub>
</p>
