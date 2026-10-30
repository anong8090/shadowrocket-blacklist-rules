# Shadowrocket 黑名单规则（个人自用）

纯域名黑名单版 v6：只有需要走代理的域名/规则集走 PROXY，其余全部直连。

- 规则段：27 条 PROXY 规则 + `FINAL,DIRECT`
- 无策略组：走哪个节点只由 Shadowrocket 首页选中的节点决定
- 无单独 DIRECT 规则：中国域名、微信、B站等靠 FINAL 兜底直连
- 核心名单：blackmatrix7 Global（35,312 条）+ Google / YouTube / Telegram / GitHub / Facebook / Gemini 等规则集，另含 Muse / Meta 域名（muse.ai、meta.ai 等）
- 规则集为远程 RULE-SET，Shadowrocket 内可自动更新

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

已导入过的，在远程文件上更新一次即可升级到 v6。

注：本配置不含任何节点、账号或密钥。
