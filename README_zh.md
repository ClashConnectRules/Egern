# Egern 配置文件

<p align="center">
  <img src="https://img.shields.io/badge/Egern-1-blue?style=flat-square" alt="Egern">
  <img src="https://img.shields.io/badge/License-MIT-green?style=flat-square" alt="License">
  <img src="https://img.shields.io/badge/Platform-iOS%20%7C%20macOS-lightgrey?style=flat-square" alt="Platform">
</p>

<p align="center">
  <a href="https://egernapp.com">Egern</a> 高级代理配置<br>
  智能分流 · 广告拦截 · 流媒体解锁 · AI 服务优化
</p>

<p align="center">
  <a href="README_zh.md">简体中文</a> · <a href="README.md">English</a>
</p>

> **订阅推荐**：[点击注册](https://hizrj.xyz/#/register?code=BwiZnFLE) 获取订阅链接，填入 `AllServer` 即可使用。

---

## 功能特性

| 特性 | 说明 |
|:-----|:-----|
| 智能 DNS | Bootstrap + DoH 上游，按域名转发，DNS 劫持 `*:53` |
| 广告拦截 | AdBlack DNS + 拒绝规则集双重拦截 |
| 地区分流 | 8 个地区组，自动过滤订阅节点 |
| 流媒体 | Netflix、Disney+、YouTube、TikTok、Bilibili |
| AI 优化 | ChatGPT、Claude、Gemini、Bing 专用路由 |
| 协议支持 | SS、Trojan、Vless、VMess、Hysteria 2、TUIC、SOCKS5、HTTP、WireGuard |

## 快速开始

1. 下载 [`egern.yaml`](https://raw.githubusercontent.com/ClashConnectRules/Egern/refs/heads/main/egern.yaml)
2. **Egern** > **配置文件** > **导入** > 选择文件
3. 将 `AllServer` 中的 `https://your-subscription-url` 替换为你的订阅链接
4. 地区组通过 `flatten: true` 自动拉取节点，无需额外配置

---

## 基础设置

| 设置项 | 值 |
|:-------|:-----|
| IPv6 | `true` |
| VIF Only | `true` |
| DNS 劫持 | `*:53` |
| GeoIP | [Masaiki/GeoIP2-CN](https://github.com/Masaiki/GeoIP2-CN) |
| ASN DB | [P3TERX/GeoLite.mmdb](https://github.com/P3TERX/GeoLite.mmdb) |
| 延迟测试 | `http://wifi.vivo.com.cn/generate_204` |

---

## DNS 配置

### Bootstrap

| 服务器 | 提供商 |
|:------:|:--------:|
| `223.5.5.5` | 阿里云 |
| `119.29.29.29` | 腾讯 |
| `1.12.12.12` | 阿里云（新） |
| `120.53.53.53` | 字节跳动 |
| `2400:3200::1` | CNNIC (IPv6) |

### 上游

| 名称 | 服务器 |
|:----:|:--------|
| `AdBlack` | `quic://dns.adguard-dns.com`、`https://dns.adguard-dns.com/dns-query` |
| `Alibaba` | `223.5.5.5`、`https://dns.alidns.com/dns-query` |
| `Tencent` | `119.29.29.29`、`https://doh.pub/dns-query` |
| `ByteDance` | `180.184.2.2`、`180.184.1.1` |
| `China` | 阿里云 + 腾讯 |
| `Global` | Cloudflare、Google DNS |

### 转发规则

| 匹配 | 目标 | 说明 |
|:-----|:----:|:-----|
| `proxy_rule_set`（拒绝） | AdBlack | 广告拦截 |
| `proxy_rule_set`（阿里云） | Alibaba | 阿里服务 |
| `proxy_rule_set`（腾讯） | Tencent | 腾讯服务 |
| `proxy_rule_set`（抖音） | ByteDance | 字节服务 |
| `proxy_rule_set`（Apple） | China | 苹果服务 |
| `proxy_rule_set`（ChinaMax） | China | 国内域名 |
| `proxy_rule_set`（Global） | Global | 国际域名 |
| `*` | Global | 默认兜底 |

---

## 策略分组

### 核心

| 分组 | 类型 | 说明 |
|:-----|:----:|:-----|
| `AllServer` | `external` | 订阅节点（自动过滤） |
| `Automatic` | `auto_test` | 按延迟自动选择（300s / 50ms） |
| `Proxy` | `select` | 手动选择代理 |
| `NoAuto` | `select` | 主入口 |
| `Mainland` | `select` | 中国大陆直连 |

### 地区

所有地区组使用 `flatten: true` 从 AllServer 拉取，`update_interval: 86400`。

| 分组 | 过滤关键词 | 图标 |
|:-----|:-----------|:----:|
| `Hong Kong` | HK、Hong Kong、HKG、MO | :flag_hk: |
| `Taiwan` | TW、Taiwan、TWN | :flag_tw: |
| `Japan` | JP、Japan、JPN | :flag_jp: |
| `Singapore` | SG、Singapore、SGP、MA | :flag_sg: |
| `United States` | US、USA、States、American | :flag_us: |
| `United Kingdom` | UK、England、Britain | :flag_gb: |
| `Korea` | KR、Korea、KOR | :flag_kr: |
| `Other` | 排除以上地区 | :earth_africa: |

### 服务

所有服务组均包含 `AllServer` 作为备选。

| 分组 | 策略 | 用途 |
|:-----|:-----|:-----|
| `AI` | Automatic、US、JP、SG、AllServer | ChatGPT、Claude、Gemini、Bing |
| `Apple` | Mainland、HK、US、AllServer | 苹果服务 |
| `Microsoft` | Mainland、HK、SG、US、AllServer | 微软服务 |
| `OneDrive` | Mainland、HK、SG、US、AllServer | 云存储 |
| `Telegram` | Automatic、SG、US、HK、TW、JP、AllServer | 电报 |
| `X` | Automatic、HK、TW、SG、JP、US、AllServer | Twitter / X |
| `WeChat` | Mainland、HK、SG、US、AllServer | 微信 |
| `Netflix` | HK、TW、SG、JP、US、AllServer | 流媒体 |
| `Disney+` | HK、SG、AllServer | 流媒体 |
| `YouTube` | Automatic、HK、TW、SG、JP、US、AllServer | 流媒体 |
| `TikTok` | TW、SG、JP、US、AllServer | 解锁 |
| `Bilibili` | Mainland、HK、TW、AllServer | 港台解锁 |
| `Speedtest` | Mainland、Automatic、AllServer | 网速测试 |

### 依赖关系

```
AllServer（订阅源）
 └─ 地区组（HK、TW、JP、SG、US、UK、KR、Other）
     ├─ Automatic（自动测速）
     └─ 服务组（AI、Apple、Netflix、...）
         └─ 均包含 AllServer
```

---

## 规则优先级

```
 1. 规则修正     DIRECT
 2. 广告拦截     REJECT
 3. 隐私保护     REJECT（追踪器）
 4. 国内应用     微信、网易云、B站、微博、小红书
 5. Apple        App Store、Apple News、Apple TV、Apple CDN
 6. AI 服务      OpenAI、Claude、Gemini、Bing
 7. 流媒体       Disney+、Netflix、TikTok、YouTube
 8. 地区解锁     US、EU、JP、KR、HK、TW
 9. 社交媒体     Twitter/X、Telegram、Facebook、Instagram、Discord
10. 其他服务     OneDrive、Microsoft、GitHub、Speedtest
11. 国内规则     SKK + ChinaMax
12. 国外规则     CDN、Global
13. 本地网络     局域网 > DIRECT
14. GeoIP        CN > Mainland
15. 兜底         NoAuto
```

---

## MITM 主机名

- `www.google.cn`
- `api.abema.io`
- `*.zhihu.com`
- `sub.store`

URL 重写和请求头重写的前置条件。

---

## 安装

**方法一：应用内导入**

1. 下载 `egern.yaml`
2. **Egern** > **配置文件** > **导入**
3. 选择文件

**方法二：iCloud**

1. 保存 `egern.yaml` 到 iCloud 云盘
2. **Egern** > **配置文件** > **从 iCloud 导入**

### 订阅配置

只需配置 `AllServer` 的订阅 URL，地区组自动拉取节点。

```yaml
- external:
    name: AllServer
    type: select
    urls:
      - "https://your-subscription-url"
    filter: '^((?!Remain|Expired|官网|如需|套餐|去除|剩余|距离|Reset|重置|流量).)+$'
    update_interval: 86400
```

---

## 自定义图标

每个分组支持 `icon` 字段（PNG，推荐 120x120 px）：

```yaml
- select:
    name: Telegram
    policies:
      - Automatic
      - Singapore
    icon: "https://example.com/icon.png"
```

<details>
<summary>图标包推荐</summary>

| 图标包 | 链接 |
|:------:|:-----|
| Qure | [QureColor-All.json](https://raw.githubusercontent.com/Koolson/Qure/master/Other/QureColor-All.json) |
| Orz-3 | [miniColor.json](https://raw.githubusercontent.com/Orz-3/mini/master/miniColor.json) |
| tugepaopao | [Cute.json](https://raw.githubusercontent.com/tugepaopao/Image-Storage/master/other/Cute.json) |
| Semporia | [Semporia.json](https://raw.githubusercontent.com/Semporia/Hand-Painted-icon/master/Semporia.json) |

</details>

---

## 致谢

- [Egern](https://egernapp.com)
- [blackmatrix7](https://github.com/blackmatrix7/ios_rule_script)
- [Skk.moe](https://ruleset.skk.moe)
- [lige47/QuanX-icon-rule](https://github.com/lige47/QuanX-icon-rule)
- [Loyalsoldier](https://github.com/Loyalsoldier/surge-rules)
- [VirgilClyne](https://github.com/VirgilClyne/GetSomeFries)
- [Semporia](https://github.com/Semporia/TikTok-Unlock)
- [zxfccmm4](https://github.com/zxfccmm4)

## 许可证

[MIT](LICENSE)
