# Claude / Anthropic · Loon 远程分流规则

面向 Loon 的精准域名规则集，覆盖 Claude 与 Anthropic 自有服务域名。每条规则仅包含 `规则类型,域名`，不包含策略组名称；在 Loon 的 `[Remote Rule]` 中绑定策略。

核对日期：2026-10-13。此项目是社区维护的规则清单，不是 Anthropic 或 Loon 官方项目。

## 安装

1. 在 Loon 中准备好名为 `Claude` 的策略组，并为它选择可用节点。此仓库不提供节点或节点订阅。
2. 编辑正在使用的 Loon 配置，将下面的订阅行加入已有的 `[Remote Rule]` 段；若没有此段，再连同段标题一起添加。不要重复创建同名段。

```ini
[Remote Rule]
https://raw.githubusercontent.com/mrtanis/loon-claude-rules/main/Claude.list,policy=Claude,tag=Claude,enabled=true
```

3. 保存配置，在 Loon 中更新远程规则，并确认该订阅已启用、下载成功。若策略组名称不同，只修改订阅行中的 `policy=Claude`，无需修改 `.list`。
4. 访问 Claude，在 Loon 请求日志中检查 `claude.ai`、`api.anthropic.com` 等请求是否命中该规则集并使用预期策略。

**Raw 订阅地址：** [Claude.list](https://raw.githubusercontent.com/mrtanis/loon-claude-rules/main/Claude.list)

也可通过 Loon 的远程规则添加入口粘贴上述 raw 地址，然后将策略选择为 `Claude`。订阅文件本身不应添加 `[Rule]`、`[Remote Rule]`、`policy=` 或第三列策略名称。`main` 地址会随此仓库后续提交更新，客户端需刷新订阅。

## 覆盖范围

`DOMAIN-SUFFIX` 同时匹配根域及其子域，例如 `anthropic.com` 覆盖 `api.anthropic.com`。

| 域名 | 范围与依据 |
| --- | --- |
| `claude.ai` | Claude 服务；官方网络需求清单 |
| `anthropic.com` | Anthropic 网站与 API；官方网络需求清单 |
| `claude.com` | Claude 网站与相关服务；官方网络需求清单 |
| `claude.app` | Claude 应用相关服务；官方网络需求清单 |
| `claudeusercontent.com` | 用户内容；官方网络需求清单 |
| `claudemcpcontent.com` | MCP 内容；官方网络需求清单 |
| `clau.de` | Anthropic 官方插件仓库使用的短链接域名 |
| `claude.dev` | 官方网站链接的开发者博客 |
| `claudemcpclient.com` | Anthropic 注册域名；补充 MCP 相关域名覆盖 |

前六项依据 [Claude Desktop 官方网络访问需求](https://code.claude.com/docs/en/desktop#network-access-requirements)；`clau.de` 的使用见 [Anthropic 官方社区插件仓库](https://github.com/anthropics/claude-plugins-community)。文档仅要求通配子域的内容域，在本规则中连同根域一起覆盖。

`claude.dev` 是 [Claude 官网](https://claude.com/) 页脚中的 Developer blog，博客本身介绍其内容来自 Anthropic 开发者。`claudemcpclient.com` 的 [注册商 RDAP 记录](https://rdap.markmonitor.com/rdap/domain/claudemcpclient.com) 将注册组织列为 `Anthropic PBC`；它因此作为自有域名补入，但域名归属不等于每种客户端当前都必须访问它。

## 精准分流的边界

- 不加入 `DOMAIN-KEYWORD,claude`、`DOMAIN-KEYWORD,anthropic` 等关键词规则，避免匹配无关网站。
- 不对 Google Storage、Datadog、Intercom、Sentry、Statsig、Sift、Google 静态资源或公共 CDN 整体指定代理。
- 不加入 IP 段或 ASN 兜底，以便将范围限制在可核实的服务域名。
- 第三方托管的认证、CMS 或 CDN 主机名，需有当前官方引用或实际请求记录才默认纳入；仅凭名称相似不足以证明当前用途。

这是一份精准的服务域名清单，不保证覆盖 Claude 网页、App、Claude Code 的全部第三方依赖。[Claude Code 官方网络配置文档](https://code.claude.com/docs/en/network-config#network-access-requirements) 仍列出 Google Storage 等用途；为避免影响其他应用，这些共享主机交由你现有的其他规则处理。

如果登录、安装或更新失败，请先从 Loon 请求日志确认实际失败域名，再按需添加尽量窄的规则。域名规则无法按 URL 路径区分共享主机上的某个存储桶或单一服务。

## Net.Coffee 清单核查

2026-10-13 对照 [Net.Coffee 域名规则页](https://ip.net.coffee/claude/site.html)，逐项检查覆盖关系、域名归属和当前用途。本次补入 `claudemcpclient.com`，并从官方网页发现和补入清单之外的 `claude.dev`。

| 清单项目 | 本仓库的处理 |
| --- | --- |
| `cdn.anthropic.com`、`mcp.anthropic.com`、`console.anthropic.com`、`workbench.anthropic.com` | 已被 `DOMAIN-SUFFIX,anthropic.com` 覆盖，无需重复添加 |
| `claudemcpclient.com` | 注册商记录确认归属 Anthropic，已补入后缀规则 |
| `servd-anthropic-website.b-cdn.net`、`anthropic.com.cdn.cloudflare.net` | 未在本次读取的当前官方网页或网络需求中确认使用，暂不默认加入；需要实际请求日志再判断 |
| `anthropic.auth0.com`、`anthropic-com.ghost.io` | 同样缺少当前官方使用依据，暂不默认加入；访问根路径时分别重定向到 Auth0 首页、返回 404，这不能证明所有认证或内容路径都无效 |
| Datadog、Sentry、Statsig、Sift、Intercom、Fathom | 共享服务，即使某个接收主机使用精确 `DOMAIN`，仍可能包含其他应用的请求；保留现有精准分流边界 |
| IP 段、ASN、宽泛关键词 | 未纳入域名清单，不据此扩大范围 |
| `geosite:category-ntp` | 未纳入；也不是此 Loon `.list` 的域名规则格式 |

该页面关于 NTP 返回地区时区的说明不成立：[NTPv4 标准 RFC 5905](https://www.rfc-editor.org/rfc/rfc5905.html) 定义的是相对于 UTC 的时间同步，标准报文字段不包含地区时区。将 NTP 走代理不能改变系统时区。也没有从本次查阅的官方网络需求中确认 Claude Code 必须通过 NTP 判断时区，故不把它当作必须补齐的依赖。

这份对照清单用于检查遗漏，不把第三方页面关于风控或“完整覆盖”的推测当作官方保证。对于尚未确认的专用主机，今后可凭实际请求日志增加精确 `DOMAIN` 规则，避免对整个 CDN 或认证平台使用后缀规则。

## 规则优先级与排查

根据 [Loon 官方规则说明](https://nsloon.app/en/docs/Rule/)，本地规则优先于插件规则，插件规则优先于订阅规则。如果已有本地或插件规则提前匹配 Claude 请求，本订阅可能不会生效；请结合请求日志检查冲突，以及其他远程规则的排列顺序。

遇到订阅更新失败，先确认设备能访问 raw 地址。只有规则成功下载、所选策略可用，分流才有实际效果。

## 格式依据与维护

- [Loon 官方配置示例](https://raw.githubusercontent.com/Loon0x00/LoonExampleConfig/master/example2.lcf)：`[Remote Rule]` 及 `policy`、`tag`、`enabled` 参数。
- [Loon 官方规则集示例](https://raw.githubusercontent.com/Loon0x00/LoonExampleConfig/master/Rule/ExampleRule.list)：远程 `.list` 中的域名规则不带策略列。

新增域名前应核实服务归属和实际用途，优先使用官方资料，避免通过共享服务或宽泛关键词扩大匹配范围。提交后刷新 Loon 订阅，并以实际请求日志验证；静态格式检查不能替代设备上的联网验证。
