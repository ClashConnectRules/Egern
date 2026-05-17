# Egern 配置文件

<p align="center">
  <img src="https://img.shields.io/badge/Egern-1-blue?style=flat-square" alt="Egern">
  <img src="https://img.shields.io/badge/License-MIT-green?style=flat-square" alt="License">
</p>

<p align="center">
  <b>高级 Egern 代理配置</b><br>
  <i>智能分流、广告拦截、流媒体解锁、AI 服务优化</i>
</p>

<p align="center">
  <a href="#-基础设置">基础</a> &bull;
  <a href="#-dns-配置">DNS</a> &bull;
  <a href="#-策略分组">分组</a> &bull;
  <a href="#-规则优先级">规则</a> &bull;
  <a href="#-安装">安装</a>
</p>

<p align="center">
  <b>Language / 语言切换</b><br>
  <a href="README_zh.md">简体中文</a> | <a href="README.md">English</a>
</p>

---

## 下载

| 文件 | 链接 |
|:----:|:-----|
| **egern.yaml** | [下载](https://raw.githubusercontent.com/ClashConnectRules/Egern/refs/heads/main/egern.yaml) |

---

## 基础设置

| 设置项 | 值 | 说明 |
|:-------:|:-----:|:-----------|
| HTTP 端口 | `6152` | HTTP 代理端口 |
| SOCKS5 端口 | `6153` | SOCKS5 代理端口 |
| IPv6 | `false` | 默认禁用 |
| QUIC 拦截 | `false` | 未启用 |
| GeoIP | [Hackl0us GeoIP2-CN](https://github.com/Hackl0us/GeoIP2-CN) | 中国 GeoIP 数据库 |

---

## DNS 配置

### Bootstrap DNS

| 服务器 | 提供商 |
|:------:|:--------:|
| `223.5.5.5` | 阿里云 |
| `223.6.6.6` | 阿里云 |
| `119.29.29.29` | 腾讯 |

### DoH 上游

| 名称 | URL |
|:----:|:-----|
| `alidns` | `https://dns.alidns.com/dns-query` |
| `dohpub` | `https://doh.pub/dns-query` |

### DNS 转发规则

| 匹配 | 目标 |
|:-----:|:------:|
| `*.cn` | Bootstrap（国内 DNS） |
| `*` | DoH Public |

### DNS 劫持

劫持目标：`8.8.8.8:53`、`8.8.4.4.4:53`（Google DNS，防止泄露）

### Host 映射

| 服务 | DNS 服务器 | 说明 |
|:-------:|:----------:|:-----------:|
| 淘宝 / 天猫 / 支付宝 | `223.5.5.5` | 阿里服务 |
| 京东 / QQ / 微信 | `119.28.28.28` | 腾讯服务 |
| 哔哩哔哩 / 网易 | `119.29.29.29` | 娱乐服务 |
| 小米 | `119.29.29.29` | 小米服务 |
| TestFlight | `8.8.4.4` | Apple TestFlight |
| Google（FCM / 下载） | 特殊 | Google 服务 |
| 路由器管理 | 系统 DNS | 本地路由器 |

---

## 策略分组

### 核心分组

| 分组 | 类型 | 描述 |
|:-----:|:----:|:-----------|
| `Mainland` | `select` | 中国大陆直连 |
| `NoAuto` | `select` | 主入口 |
| `Automatic` | `auto_test` | 地区自动选择 |
| `AllServer` | `select` | 全部订阅节点 |
| `Proxy` | `select` | 代理策略 |

### 地区分组（自动测速）

| 分组 | 过滤 | 间隔 | 容差 |
|:-----:|:------:|:--------:|:---------:|
| `Hong Kong` | `HK\|Hong\|香港\|港` | 300s | 50ms |
| `Taiwan` | `TW\|Tai\|台湾\|台` | 300s | 50ms |
| `Japan` | `JP\|Japan\|日本\|日` | 300s | 50ms |
| `Singapore` | `SG\|Singapore\|新加坡\|狮城` | 300s | 50ms |
| `United States` | `US\|States\|美国\|美` | 300s | 50ms |
| `United Kingdom` | `UK\|United Kingdom\|英国` | 300s | 50ms |
| `Korea` | `KR\|Korea\|韩国\|韩` | 300s | 50ms |
| `Other` | 排除以上 | 300s | 50ms |

### 服务分组

| 分组 | 默认 | 用途 |
|:-----:|:-------:|:-------|
| `AI` | Automatic | ChatGPT、Claude、Gemini、Bing |
| `Apple` | Mainland - HK - US | 苹果服务 |
| `Microsoft` | Mainland - HK - SG - US | 微软服务 |
| `OneDrive` | Mainland - HK - SG - US | 云存储 |
| `Telegram` | Automatic - SG - US - HK | 电报 |
| `X` | Automatic - HK - TW - SG - JP - US | Twitter / X |
| `WeChat` | Mainland - HK - SG - US | 微信 |
| `Netflix` | HK - TW - SG - JP - US | Netflix 流媒体 |
| `Disney+` | HK - SG | Disney+ 流媒体 |
| `YouTube` | Automatic - HK - TW - SG - JP - US | YouTube 流媒体 |
| `TikTok` | TW - SG - JP - US | TikTok 解锁 |
| `Bilibili` | Mainland - HK - TW | 哔哩哔哩（港台解锁） |
| `Speedtest` | Mainland - Auto - AllServer | 网速测试 |

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
14. 兜底规则        default > NoAuto
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

## 特殊功能

### URL 重写

| 原地址 | 目标 | 状态码 |
|:--------:|:------:|:------:|
| `google.cn` | `google.com` | 302 |
| `maps.google.cn` | `maps.google.com` | 302 |
| `taobao.com` | HTTPS | 302 |
| `jd.com` | HTTPS | 302 |
| `mi.com` | HTTPS | 302 |
| `you.163.com` | HTTPS | 302 |
| `suning.com` | HTTPS | 302 |
| `yhd.com` | HTTPS | 302 |
| `api.abema.io` | 拒绝 | -1 |

### 请求头重写

| 目标 | 头部 | 值 | 类型 |
|:------:|:------:|:-----:|:----:|
| `github.com` | Accept-Language | en-us | request |
| `*.githubusercontent.com` | Accept-Language | en-us | request |

修复 GitHub 429 速率限制问题。

### MITM 主机名

- `www.google.cn`
- `api.abema.io`
- `*.zhihu.com`
- `sub.store`

---

## 安装

### 方法一：应用内导入

```
1. 下载 egern.yaml
2. Egern > 配置文件 > 导入
3. 选择下载的文件
```

### 方法二：iCloud 同步

```
1. 将 egern.yaml 保存到 iCloud 云盘
2. Egern > 配置文件 > 从 iCloud 导入
3. 选择文件
```

### 配置订阅

替换所有 `external` 策略组中的占位 URL：

```yaml
policy_groups:
  - external:
      name: AllServer
      type: select
      urls:
        - "https://your-subscription-url"
```

需要更新每个 `external` 分组（Hong Kong、Taiwan、Japan、Singapore、United States、United Kingdom、Korea、Other）的 `urls` 字段。

---

## 配置

### 添加代理服务器

编辑 `proxies` 部分：

```yaml
proxies:
  - shadowsocks:
      name: MySS
      method: aes-256-gcm
      password: your_password
      server: 1.2.3.4
      port: 8388

  - trojan:
      name: MyTrojan
      server: example.com
      port: 443
      password: your_password

  - vmess:
      name: MyVMess
      server: 1.2.3.4
      port: 443
      user_id: uuid
      security: auto

  - hysteria2:
      name: MyHysteria2
      server: 1.2.3.4
      port: 443
      auth: your_password
```

### 启用 MITM

```yaml
mitm:
  ca_p12: "your_base64_cert"
  ca_passphrase: "123456"
  hostnames:
    includes:
      - "*.example.com"
```

---

## 规则来源

| 来源 | 描述 |
|:------:|:-----------|
| [blackmatrix7](https://github.com/blackmatrix7/ios_rule_script) | 跨平台规则 |
| [Skk.moe](https://ruleset.skk.moe) | SKK 规则集 |
| [VirgilClyne](https://github.com/VirgilClyne/GetSomeFries) | ASN 规则 |
| [Semporia](https://github.com/Semporia/TikTok-Unlock) | TikTok 解锁 |
| [zxfccmm4](https://github.com/zxfccmm4) | Unbreak 规则 |

---

## 注意事项

| 项目 | 描述 |
|:----:|:-----------|
| 订阅链接 | 需替换为您的订阅地址 |
| 规则更新 | 规则从在线源自动更新 |
| 测速设置 | 300s 间隔、3s 超时、50ms 容差 |
| MITM 证书 | URL / 请求头重写需要安装证书 |
| 节点过滤 | 自动过滤包含"流量/重置/过期"关键词的节点 |

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

### 图标包推荐

| 图标包 | 链接 |
|:------:|:-----|
| Qure（彩色） | [QureColor-All.json](https://raw.githubusercontent.com/Koolson/Qure/master/Other/QureColor-All.json) |
| Orz-3（彩色） | [miniColor.json](https://raw.githubusercontent.com/Orz-3/mini/master/miniColor.json) |
| tugepaopao | [Cute.json](https://raw.githubusercontent.com/tugepaopao/Image-Storage/master/other/Cute.json) |
| Semporia | [Semporia.json](https://raw.githubusercontent.com/Semporia/Hand-Painted-icon/master/Semporia.json) |

---

## 致谢

- [Egern](https://egernapp.com)
- [blackmatrix7](https://github.com/blackmatrix7)
- [Skk.moe](https://github.com/Skk.moe)
- [lige47/QuanX-icon-rule](https://github.com/lige47/QuanX-icon-rule)

---

## 许可证

**MIT**

---

<p align="center">
  <sub>用心打造更好的网络体验</sub>
</p>
