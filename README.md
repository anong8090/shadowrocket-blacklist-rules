# Shadowrocket 黑名单规则（个人自用）

纯域名黑名单版 v6.7：只有需要走代理的域名/规则集走 PROXY，其余全部直连。

- 规则段：36 条 PROXY 规则 + Global DOMAIN-SET（35,132 条域名）+ 46 条 DIRECT 豁免（富途/Moomoo 41 条 + 苹果 5 条） + `FINAL,DIRECT`
- 无策略组：走哪个节点只由 Shadowrocket 首页选中的节点决定
- 无单独 DIRECT 规则：中国域名、微信、B站等靠 FINAL 兜底直连
- 核心名单：blackmatrix7 Global（35,312 条）+ Google / YouTube / Telegram / GitHub / Facebook / Gemini 等规则集，另含 Muse / Meta 域名（muse.ai、meta.ai 等）
- 规则集为远程 RULE-SET，Shadowrocket 内可自动更新

## v6.7 变更（2026-11-05，全表审计清理）

逐条审计了全部规则集与写死规则，删掉 5 处不合理/遗留项：

1. **删 Microsoft 全家桶表**（blackmatrix7 Microsoft.list，671 条）：这是微软全产品线清单而非被墙清单，连 `azure.cn`、`aadrm.cn` 等微软中国云（世纪互联）域名都被送去代理。删除后 `microsoft.com`、`bing.com`、`onedrive.com`、`linkedin.com`、`skype.com`、`xbox` 等仍在 Global 大名单内继续走代理，不受影响；微软中国云与冷门 Azure/开发域名改走直连。
2. **删 HK_Broker 券商整表**：富途已在 v6.5 豁免，该表只剩一堆富途后端 IP 段与未使用的长桥/老虎/嘉信/雪球；盈透（interactivebrokers.com、.com.hk、ibkr.com）经实查在 Global 大名单内已有，另在配置里写死 `interactivebrokers.com`、`ibkr.com` 双保险。长桥/老虎/嘉信/雪球转直连（大陆均可直达）。
3. **删 `IP-CIDR,208.54.0.0/16,PROXY`**：美国 T-Mobile 的 IP 段，原基底遗留，与本配置用途无关。
4. **删 [URL Rewrite] 与 MITM 主机名**：google.cn/g.cn 跳转重写在 MITM 关闭时本就是死配置，一并删除；[MITM] 只留 `enable = false`。
5. **删 [Host] 里 5 条 dns-sd 残留**：带具体网段（10.211.55.x 等）的是原作者局域网的记录，与本机无关；通用项保留。

另外苹果 DIRECT 豁免补上 `icloud.com.cn`（Mail 规则集原本把 mail.icloud.com.cn 送去代理）。

## v6.6 变更（2026-11-05）

苹果改直连：实查苹果被代理有三处来源——Global 大名单收了 `apple.com` 与 `icloud.com`（含 icloud-content.com、apple-cloudkit.com）、LingJing 的 ApplePush 推送表、以及一条把 TCP 5223 端口（苹果推送专用端口）一律送代理的 AND 规则。v6.6 在规则段最前加 4 条苹果 DIRECT 豁免，并删掉 ApplePush 规则集与 5223 强制代理规则。`apple.news`（Apple News）不在豁免内，仍随 Global 走代理。若后续国外 App 推送明显变不及时，多半是推送改走直连所致，可单独恢复推送规则。

## v6.5 变更（2026-11-02）

富途 / Moomoo 改直连：HK_Broker 券商表与 Global 大名单都收录了富途全系域名（futunn.com、futu5.com 等），在规则段最前面加 41 条 DOMAIN-SUFFIX DIRECT 豁免（覆盖 HK_Broker 与 Global 两处来源的富途/Moomoo 域名全集）。同一张 HK_Broker 表里的盈透（Interactive Brokers）、长桥、老虎、嘉信不在豁免内，仍走 PROXY。注：HK_Broker 表里还有一批富途后端 IP-CIDR，纯按 IP 发起的连接仍可能命中代理，富途 App 的行情/交易接口均为域名连接，不受影响。

## v6.4 变更（2026-10-31，修 v6 的误删）

v6 时误把 `Global_Domain.list` 当作与 Global RULE-SET 重复而删除。实查 blackmatrix7 文件结构：Global 的 34,993 条 DOMAIN-SUFFIX 全部在 `Global_Domain.list`（DOMAIN-SET 格式），`Global.list` 本体只有 36 条关键词、119 条 IP-CIDR、46 条 USER-AGENT。删除后大名单主体实际缺失，专项表之外的站点（包括 github.com 的部分覆盖）只能落 FINAL 直连。v6.4 恢复该 DOMAIN-SET，并写死 GitHub 家族四域名（github.com、githubusercontent.com、githubassets.com、github.io），不再单靠远程表加载。

## v6.3 变更（2026-10-31）

一分机场面板/订阅域名走 PROXY（直连打不开）：`xn--4gqx1hgtfdmt.com`（一分机场.com 面板）、`yfjc.my`（订阅转换等）、`yfjc.win`（官方网盘/教程）、`yfjc.sbs`（节点根域，兜住同根域的面板/API；节点连接本身由 App 直连、不受规则影响）。

## v6.2 变更（2026-10-31，对照 Johnshall 31k star 版）

1. `tun-excluded-routes` 补 `198.18.0.0/15`（代理假 IP 段）与 IPv6 私有/保留段（::1/128、2001:db8::/32、fc00::/7、fe80::/10、ff00::/8）
2. `skip-proxy` 补 `fe80::/10`、`fc00::/7`、`*.lan`、`*.internal`
3. Muse/Meta 段写死 Instagram：instagram.com、cdninstagram.com、instagr.am

## v6.1 变更（2026-10-31，对照 LOWERTOP 无策略组版）

1. `ipv6` 改为 true（prefer-ipv6 仍为 false），避免影响机场节点解析/连接
2. `dns-direct-system` 改为 false：直连域名也走配置的国内 DoH，不再用运营商系统 DNS
3. `dns-direct-fallback-proxy` 改为 true：直连解析失败时走代理兜底
4. 新增 `stun-response-ip = 1.1.1.1` / `stun-response-ipv6 = ::1`，防 WebRTC 泄漏真实 IP

## v6 变更（2026-10-31）

1. DNS 改国内 DoH 直连（doh.pub / alidns + 223.5.5.5 / 119.29.29.29），不再全部走代理；hijack-dns 只留 8.8.8.8 / 8.8.4.4，fallback 改 system
2. MITM 关闭（订阅版无 CA 证书，原开启状态不生效）
3. 删除重复的 Global_Domain DOMAIN-SET，只保留 Global RULE-SET
4. 写死 Claude / OpenAI 域名：claude.ai、anthropic.com、openai.com、chatgpt.com

## 订阅链接（Raw）

```
https://raw.githubusercontent.com/anong8090/shadowrocket-blacklist-rules/main/merged-blacklist.conf
```

## 导入方法

1. 复制上面的 Raw 链接
2. Shadowrocket → 配置 → 右上角 `+` → 粘贴链接 → 下载
3. 点已下载的配置，设为使用中
4. 首页添加自己的节点/订阅，选一个可用节点连接，全局路由选「配置」

已导入过的，在远程文件上更新一次即可升级到 v6.7。

注：本配置不含任何节点、账号或密钥。
