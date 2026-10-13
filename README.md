# Claude / Anthropic · Loon 远程分流规则

面向 Loon 的精准域名规则集，覆盖 Claude 与 Anthropic 自有服务域名。每条规则仅包含 `规则类型,域名`，不包含策略组名称；在 Loon 的 `[Remote Rule]` 中绑定策略。

核对日期：2026-10-13。此项目是社区维护的规则清单，不是 Anthropic 或 Loon 官方项目。

## 安装

1. 在 Loon 中准备好名为 `AI Suite` 的策略组，并为它选择可用节点。此仓库不提供节点或节点订阅。
2. 编辑正在使用的 Loon 配置，将下面的订阅行加入已有的 `[Remote Rule]` 段；若没有此段，再连同段标题一起添加。不要重复创建同名段。

```ini
[Remote Rule]
https://raw.githubusercontent.com/mrtanis/loon-claude-rules/main/Claude.list,policy=AI Suite,tag=Claude,enabled=true
```

3. 保存配置，在 Loon 中更新远程规则，并确认该订阅已启用、下载成功。若策略组名称不同，只修改订阅行中的 `policy=AI Suite`，无需修改 `.list`。
4. 访问 Claude，在 Loon 请求日志中检查 `claude.ai`、`api.anthropic.com` 等请求是否命中该规则集并使用预期策略。

**Raw 订阅地址：** [Claude.list](https://raw.githubusercontent.com/mrtanis/loon-claude-rules/main/Claude.list)

也可通过 Loon 的远程规则添加入口粘贴上述 raw 地址，然后将策略选择为 `AI Suite`。订阅文件本身不应添加 `[Rule]`、`[Remote Rule]`、`policy=` 或第三列策略名称。`main` 地址会随此仓库后续提交更新，客户端需刷新订阅。

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

前六项依据 [Claude Desktop 官方网络访问需求](https://code.claude.com/docs/en/desktop#network-access-requirements)；`clau.de` 的使用见 [Anthropic 官方社区插件仓库](https://github.com/anthropics/claude-plugins-community)。文档仅要求通配子域的内容域，在本规则中连同根域一起覆盖。

## 精准分流的边界

- 不加入 `DOMAIN-KEYWORD,claude`、`DOMAIN-KEYWORD,anthropic` 等关键词规则，避免匹配无关网站。
- 不对 Google Storage、Datadog、Intercom、Sentry、Statsig、Sift、Google 静态资源或公共 CDN 整体指定代理。
- 不加入 IP 段或 ASN 兜底，以便将范围限制在可核实的服务域名。
- 旧清单中的 `claudemcpclient.com`、`anthropic.auth0.com` 和第三方托管 CDN 主机名，暂未在本次查阅的当前官方网络需求中得到确认，未默认纳入；这不表示它们一定无效。

这是一份精准的服务域名清单，不保证覆盖 Claude 网页、App、Claude Code 的全部第三方依赖。[Claude Code 官方网络配置文档](https://code.claude.com/docs/en/network-config#network-access-requirements) 仍列出 Google Storage 等用途；为避免影响其他应用，这些共享主机交由你现有的其他规则处理。

如果登录、安装或更新失败，请先从 Loon 请求日志确认实际失败域名，再按需添加尽量窄的规则。域名规则无法按 URL 路径区分共享主机上的某个存储桶或单一服务。

## 规则优先级与排查

根据 [Loon 官方规则说明](https://nsloon.app/en/docs/Rule/)，本地规则优先于插件规则，插件规则优先于订阅规则。如果已有本地或插件规则提前匹配 Claude 请求，本订阅可能不会生效；请结合请求日志检查冲突，以及其他远程规则的排列顺序。

遇到订阅更新失败，先确认设备能访问 raw 地址。只有规则成功下载、所选策略可用，分流才有实际效果。

## 格式依据与维护

- [Loon 官方配置示例](https://raw.githubusercontent.com/Loon0x00/LoonExampleConfig/master/example2.lcf)：`[Remote Rule]` 及 `policy`、`tag`、`enabled` 参数。
- [Loon 官方规则集示例](https://raw.githubusercontent.com/Loon0x00/LoonExampleConfig/master/Rule/ExampleRule.list)：远程 `.list` 中的域名规则不带策略列。

新增域名前应核实服务归属和实际用途，优先使用官方资料，避免通过共享服务或宽泛关键词扩大匹配范围。提交后刷新 Loon 订阅，并以实际请求日志验证；静态格式检查不能替代设备上的联网验证。
