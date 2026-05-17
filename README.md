# Egern Configuration

<p align="center">
  <img src="https://img.shields.io/badge/Egern-1-blue?style=flat-square" alt="Egern">
  <img src="https://img.shields.io/badge/License-MIT-green?style=flat-square" alt="License">
  <img src="https://img.shields.io/badge/Platform-iOS%20%7C%20macOS-lightgrey?style=flat-square" alt="Platform">
</p>

<p align="center">
  Advanced proxy configuration for <a href="https://egernapp.com">Egern</a><br>
  Smart routing · Ad blocking · Streaming unlock · AI optimization
</p>

<p align="center">
  <a href="README_zh.md">简体中文</a> · <a href="README.md">English</a>
</p>

---

## Highlights

| Feature | Description |
|:--------|:------------|
| Smart DNS | Bootstrap + DoH with domain-based forwarding, DNS hijack on `*:53` |
| Ad Blocking | AdBlack DNS + reject ruleset (double layer) |
| Regional Routing | 8 region groups auto-filtered from subscription |
| Streaming | Netflix, Disney+, YouTube, TikTok, Bilibili |
| AI Services | Dedicated routing for ChatGPT, Claude, Gemini, Bing |
| Protocols | SS, Trojan, Vless, VMess, Hysteria 2, TUIC, SOCKS5, HTTP, WireGuard |

## Quick Start

1. Download [`egern.yaml`](https://raw.githubusercontent.com/ClashConnectRules/Egern/refs/heads/main/egern.yaml)
2. **Egern** > **Profiles** > **Import** > select the file
3. Replace `https://your-subscription-url` in `AllServer` with your subscription link
4. Regional groups auto-pull nodes via `flatten: true` — no extra setup needed

---

## Basic Settings

| Setting | Value |
|:--------|:------|
| IPv6 | `true` |
| VIF Only | `true` |
| DNS Hijack | `*:53` |
| GeoIP | [Masaiki/GeoIP2-CN](https://github.com/Masaiki/GeoIP2-CN) |
| ASN DB | [P3TERX/GeoLite.mmdb](https://github.com/P3TERX/GeoLite.mmdb) |
| Latency Test | `http://wifi.vivo.com.cn/generate_204` |

---

## DNS

### Bootstrap

| Server | Provider |
|:------:|:--------:|
| `223.5.5.5` | Alibaba |
| `119.29.29.29` | Tencent |
| `1.12.12.12` | Alibaba (New) |
| `120.53.53.53` | ByteDance |
| `2400:3200::1` | CNNIC (IPv6) |

### Upstreams

| Name | Servers |
|:----:|:--------|
| `AdBlack` | `quic://dns.adguard-dns.com`, `https://dns.adguard-dns.com/dns-query` |
| `Alibaba` | `223.5.5.5`, `https://dns.alidns.com/dns-query` |
| `Tencent` | `119.29.29.29`, `https://doh.pub/dns-query` |
| `ByteDance` | `180.184.2.2`, `180.184.1.1` |
| `China` | Alibaba + Tencent combined |
| `Global` | Cloudflare, Google DNS |

### Forward Rules

| Match | Target | Description |
|:------|:------:|:------------|
| `proxy_rule_set` (reject) | AdBlack | Ad blocking |
| `proxy_rule_set` (Alibaba) | Alibaba | Alibaba services |
| `proxy_rule_set` (Tencent) | Tencent | Tencent services |
| `proxy_rule_set` (DouYin) | ByteDance | ByteDance services |
| `proxy_rule_set` (Apple) | China | Apple services |
| `proxy_rule_set` (ChinaMax) | China | China domains |
| `proxy_rule_set` (Global) | Global | International domains |
| `*` | Global | Default fallback |

---

## Policy Groups

### Core

| Group | Type | Description |
|:------|:----:|:------------|
| `AllServer` | `external` | Subscription nodes (auto-filtered) |
| `Automatic` | `auto_test` | Auto-select by latency (300s / 50ms) |
| `Proxy` | `select` | Manual proxy selection |
| `NoAuto` | `select` | Main entry point |
| `Mainland` | `select` | China Direct |

### Regions

All groups use `flatten: true` from AllServer, `update_interval: 86400`.

| Group | Filter | Icon |
|:------|:-------|:----:|
| `Hong Kong` | HK, Hong Kong, HKG, MO | :flag_hk: |
| `Taiwan` | TW, Taiwan, TWN | :flag_tw: |
| `Japan` | JP, Japan, JPN | :flag_jp: |
| `Singapore` | SG, Singapore, SGP, MA | :flag_sg: |
| `United States` | US, USA, States, American | :flag_us: |
| `United Kingdom` | UK, England, Britain | :flag_gb: |
| `Korea` | KR, Korea, KOR | :flag_kr: |
| `Other` | Excludes above regions | :earth_africa: |

### Services

All service groups include `AllServer` as a fallback.

| Group | Policies | Purpose |
|:------|:---------|:--------|
| `AI` | Automatic, US, JP, SG, AllServer | ChatGPT, Claude, Gemini, Bing |
| `Apple` | Mainland, HK, US, AllServer | Apple services |
| `Microsoft` | Mainland, HK, SG, US, AllServer | Microsoft services |
| `OneDrive` | Mainland, HK, SG, US, AllServer | Cloud storage |
| `Telegram` | Automatic, SG, US, HK, TW, JP, AllServer | Messaging |
| `X` | Automatic, HK, TW, SG, JP, US, AllServer | Twitter / X |
| `WeChat` | Mainland, HK, SG, US, AllServer | WeChat |
| `Netflix` | HK, TW, SG, JP, US, AllServer | Streaming |
| `Disney+` | HK, SG, AllServer | Streaming |
| `YouTube` | Automatic, HK, TW, SG, JP, US, AllServer | Streaming |
| `TikTok` | TW, SG, JP, US, AllServer | Unlock |
| `Bilibili` | Mainland, HK, TW, AllServer | HK/TW unlock |
| `Speedtest` | Mainland, Automatic, AllServer | Speed test |

### Dependency

```
AllServer (subscription)
 └─ Regions (HK, TW, JP, SG, US, UK, KR, Other)
     ├─ Automatic (auto_test)
     └─ Services (AI, Apple, Netflix, ...)
         └─ All include AllServer
```

---

## Rule Priority

```
 1. Unbreak        DIRECT
 2. Ad Blocking    REJECT
 3. Privacy        REJECT (trackers)
 4. CN Apps        WeChat, NetEase, Bilibili, Weibo, XiaoHongShu
 5. Apple          App Store, Apple News, Apple TV, Apple CDN
 6. AI             OpenAI, Claude, Gemini, Bing
 7. Streaming      Disney+, Netflix, TikTok, YouTube
 8. Regional       US, EU, JP, KR, HK, TW streams
 9. Social         Twitter/X, Telegram, Facebook, Instagram, Discord
10. Other          OneDrive, Microsoft, GitHub, Speedtest
11. CN Rules       SKK + ChinaMax
12. Global Rules   CDN, Global
13. LAN            Local network > DIRECT
14. GeoIP          CN > Mainland
15. Default        NoAuto
```

---

## MITM Hostnames

- `www.google.cn`
- `api.abema.io`
- `*.zhihu.com`
- `sub.store`

Required for URL rewrite and header rewrite features.

---

## Installation

**Method 1 — In-App Import**

1. Download `egern.yaml`
2. **Egern** > **Profiles** > **Import**
3. Select the file

**Method 2 — iCloud**

1. Save `egern.yaml` to iCloud Drive
2. **Egern** > **Profiles** > **Import from iCloud**

### Subscription

Only `AllServer` needs your URL. Regional groups pull nodes automatically.

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

## Custom Icons

Each group supports `icon` (PNG, 120x120 px recommended):

```yaml
- select:
    name: Telegram
    policies:
      - Automatic
      - Singapore
    icon: "https://example.com/icon.png"
```

<details>
<summary>Icon packs</summary>

| Pack | Link |
|:-----|:-----|
| Qure | [QureColor-All.json](https://raw.githubusercontent.com/Koolson/Qure/master/Other/QureColor-All.json) |
| Orz-3 | [miniColor.json](https://raw.githubusercontent.com/Orz-3/mini/master/miniColor.json) |
| tugepaopao | [Cute.json](https://raw.githubusercontent.com/tugepaopao/Image-Storage/master/other/Cute.json) |
| Semporia | [Semporia.json](https://raw.githubusercontent.com/Semporia/Hand-Painted-icon/master/Semporia.json) |

</details>

---

## Credits

- [Egern](https://egernapp.com)
- [blackmatrix7](https://github.com/blackmatrix7/ios_rule_script)
- [Skk.moe](https://ruleset.skk.moe)
- [lige47/QuanX-icon-rule](https://github.com/lige47/QuanX-icon-rule)
- [Loyalsoldier](https://github.com/Loyalsoldier/surge-rules)
- [VirgilClyne](https://github.com/VirgilClyne/GetSomeFries)
- [Semporia](https://github.com/Semporia/TikTok-Unlock)
- [zxfccmm4](https://github.com/zxfccmm4)

## License

[MIT](LICENSE)
