# Rinspace

Rinspace 是一个以 Tag 为核心的长文、知识与社区空间。我们希望让文章、书籍、
讨论和知识关系都能追溯到明确的源码与修订，同时为 Markdown、LaTeX 和 Typst
提供可靠的创作、渲染与阅读体验。

[访问 Rinspace](https://rinspace.com) · [查看公开仓库](https://github.com/orgs/rinspacehq/repositories)

## 公开项目

| 仓库 | 职责 | 当前状态 | 许可 |
| --- | --- | --- | --- |
| [`rinspace-web`](https://github.com/rinspacehq/rinspace-web) | 官网表世界前端：阅读、写作、Tag、资料与社区界面 | 官网固定消费 [`v0.2.3`](https://github.com/rinspacehq/rinspace-web/releases/tag/v0.2.3)；该发行在 GitHub 上仍标记为预发行 | AGPL-3.0-only |
| [`rinspace-renderer`](https://github.com/rinspacehq/rinspace-renderer) | Markdown、LaTeX、Typst、图表及 PDF 渲染协议与运行时 | [`v0.1.0-rc.2`](https://github.com/rinspacehq/rinspace-renderer/releases/tag/v0.1.0-rc.2) 已由 Rinspace 按精确 OCI digest 消费；仍处于 RC 阶段 | AGPL-3.0-only |
| [`rinspace-editor-markdown`](https://github.com/rinspacehq/rinspace-editor-markdown) | Rinspace 实际使用的 Milkdown 写作页和增强插件 | 稳定发行 [`v0.3.4`](https://github.com/rinspacehq/rinspace-editor-markdown/releases/tag/v0.3.4) | MIT |
| [`mastodon`](https://github.com/rinspacehq/mastodon) | Rinspace 里世界使用的 Mastodon fork | 跟踪上游并承载 Rinspace 的本地社区集成 | AGPL-3.0 |

Rinspace 的产品后端、Control Plane、生产集成和部署配置目前仍在私有产品仓库中。
上面的公开仓库是真实产品组件，但它们还不能单独组成一套可完整自托管的
Rinspace 网站。我们会在边界、许可、测试和私仓消费方式明确后逐步开放适合独立
维护的模块。

## 我们如何组织内容

Rinspace 把 Tag 当作有稳定身份、上下文和生命周期的知识节点，而不只是附在文章
上的字符串。长文源码及其历史由 Git 仓库保存；发布过程绑定精确 commit，Renderer
生成不可变候选，Control Plane 核对源码与结果身份后才激活公开版本。

```text
exact Git commit
  → Control Plane validates identity and policy
  → Renderer creates an immutable result
  → Control Plane activates the verified result
  → Web serves the active article or book
```

公开组件不会因为 `main`、`latest` 或一个尚未审查的 PR 改变线上产品。Rinspace
只消费经过公开检查、私仓同包验收并固定版本与摘要的发行物。Renderer 同时保留
无需 Rinspace 账号或 CloudBase 的本地模式，产品模式则通过服务身份和 Control
Plane 合同接入。

## 近期规划

1. **稳定公开组件边界。** 完善可复现发行、精确依赖锁、版本遥测、回退证据和
   公开贡献进入产品的审查流程。
2. **改进长文阅读。** 更忠实地展示 Renderer 产出的结构、数学、参考文献和 SVG；
   对 LaTeXML 无法可靠转换的内容直接提供原 PDF 阅读。
3. **完善创作体验。** 继续统一 Markdown、LaTeX 与 Typst 的创建、编辑、预览、
   发布和诊断体验；持续完善 Markdown 编辑器的代码块复制粘贴兼容性，并让可独立
   复用的编辑组件留在公开仓库。
4. **建设表里世界。** 表世界专注书籍、文章、Tag 和知识关系；里世界基于
   Mastodon 提供本地社交能力。共享身份和关注关系需要保持一致，联邦能力不作为
   当前阶段的承诺。
5. **降低自托管门槛。** 先让 Renderer 和写作组件可独立运行，再逐步整理公开协议、
   示例配置与可安全发布的产品模块。完整站点自托管仍是后续目标。

## 已知问题与限制

- **还不是完整开源站点。** 公开前端、Renderer、写作页和 Mastodon fork 并不包含
  Rinspace 的全部后端、身份、数据迁移和部署系统。
- **前端本地模式连接正式服务。** `rinspace-web` 的一键本地开发用于修改真实前端；
  需要登录时使用自己的 Rinspace 账号，已经接通的写操作可能影响自己的正式数据。
- **Renderer 仍是预发行。** 当前 OCI 来源、manifest 和消费摘要可验证，但
  `v0.1.0-rc.2` 的运行日志版本仍显示 `0.1.0-dev`。判断制品身份应使用 OCI digest；
  后续候选会修正该遥测字符串。
- **LaTeXML 不是任意 TeX 的通用浏览器。** 自定义宏、特殊包或复杂工程可能无法
  无损转换为 HTML。Rinspace 保留诊断，并为无法可靠转换的书籍提供原 PDF 阅读。
- **Typst HTML worker 的限制很大。** 当前 worker 受官方 `typst2html` 能力边界
  约束；它对绘图、复杂图形包和依赖 PDF 后端的视觉内容支持并不好，HTML 结果
  可能缺失、退化或与 PDF 不一致。现阶段不能把 Typst HTML 预览当作 PDF 的等价
  结果；对版式和绘图保真有要求时应提供固定的原 Typst PDF。
- **PDF 与 Typst 编译器是可选的受限工作负载。** Renderer 的默认本地 Compose
  不会自动启动这些镜像；部署方需要显式配置受限 broker、资源上限和固定 digest。
- **Markdown 编辑器的代码粘贴修复仍需更多浏览器覆盖。** `v0.3.4` 已把来自
  VS Code 的源码粘贴为代码块，并覆盖了首行 `#` 不被 H1 规则吞掉的回归；其他编辑器、
  复制方向和复杂代码块组合仍可能存在兼容差异。报告时请附上最小输入、来源编辑器、
  浏览器和实际结果，且不要粘贴私有源码。
- **第三方许可需要逐项阅读。** 各仓库保留第三方 notice；Renderer 镜像 SBOM 中
  仍有包的许可证字段为 `NOASSERTION`，这不表示这些依赖没有许可证要求。
- **公开 issue 数量不代表完成度。** 当前路线中的限制有些尚未拆成 GitHub issue。
  欢迎把可复现的问题提交到实际拥有该代码的仓库。

## 感谢

Rinspace 会明确保留并说明已经采纳的贡献，而不是只在这里笼统地写“感谢社区”。

### 已采纳的社区贡献

- 感谢 [`@xjn2005`](https://github.com/xjn2005) 的
  [`rinspace-web` PR #25](https://github.com/rinspacehq/rinspace-web/pull/25)：
  改进个人资料页布局与深色模式简介展示，增加不额外请求 favicon 的个人网站链接，
  并补充相应测试。这些改动自 `v0.2.2` 起进入公开源码，并继续由官网当前的
  `v0.2.3` 固定消费。
- 同样感谢 [`@xjn2005`](https://github.com/xjn2005) 的
  [`rinspace-editor-markdown` PR #13](https://github.com/rinspacehq/rinspace-editor-markdown/pull/13)：
  我们采纳了 VS Code 源码粘贴为代码块、演示图片 URL 修复和依赖更新，并在
  `v0.3.4` 中发布。关于收紧 KaTeX 信任默认值的部分没有进入产品；Rinspace 的受信
  创作环境继续保留 KaTeX 扩展能力。

我们也感谢所有提交代码、测试、翻译、问题复现和设计反馈的人。新的贡献一旦被
采纳，我们会在相应 PR、发行说明或本节中说明实际进入了什么，而不会把“收到建议”
写成“已经采用”。

### 上游项目

Rinspace 建立在许多长期维护的自由软件之上。特别感谢
[`Mastodon`](https://github.com/mastodon/mastodon)、
[`Milkdown`](https://github.com/Milkdown/milkdown)、
[`Typst`](https://github.com/typst/typst)、
[`LaTeXML`](https://github.com/brucemiller/LaTeXML)、
[`CodeMirror`](https://github.com/codemirror)、
[`KaTeX`](https://github.com/KaTeX/KaTeX) 及其贡献者。每个项目和依赖继续保留
自己的许可证、著作权与署名；各仓库的 notice 文件提供更完整的清单。

## 参与贡献

请先阅读目标仓库的 README、贡献指南、安全策略和许可说明。一个公开 PR 不会自动
进入 `rinspace.com`；维护者会对固定发行物再做产品集成和回退审查。

- UI、样式、翻译和浏览器行为：[`rinspace-web/issues`](https://github.com/rinspacehq/rinspace-web/issues)
- 渲染协议、引擎和本地部署：[`rinspace-renderer/issues`](https://github.com/rinspacehq/rinspace-renderer/issues)
- Markdown 写作页和编辑器行为：[`rinspace-editor-markdown/issues`](https://github.com/rinspacehq/rinspace-editor-markdown/issues)
- 跨仓规划和 Rinspace 特有的 Mastodon 集成：[`rinspacehq/.github/issues`](https://github.com/rinspacehq/.github/issues)
- Mastodon 上游问题请优先向 [`mastodon/mastodon`](https://github.com/mastodon/mastodon) 报告。

请不要在公开 issue、PR、截图或日志中提交密码、验证码、Cookie、token、生产配置、
私有作品或他人的个人数据。安全问题请使用对应仓库的私密漏洞报告渠道或安全策略
中列出的联系方式。

---

状态更新：2026-10-10。项目仍在快速演进；发行说明和各仓库 README 比本页更具体。
