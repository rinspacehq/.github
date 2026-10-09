# Rinspace

[简体中文](README.md) | [English](README_EN.md)

Rinspace is a platform for long-form publishing, knowledge, and community, organized around Tags. Articles and books retain traceable source and revision histories, with authoring, rendering, and reading workflows for Markdown, LaTeX, and Typst.

- **Website:** [rinspace.com](https://rinspace.com)
- **Open source:** [github.com/rinspacehq](https://github.com/rinspacehq)

## Projects

| Repository | Role | Current version | License |
| --- | --- | --- | --- |
| [`rinspace-web`](https://github.com/rinspacehq/rinspace-web) | Web frontend for reading, authoring, Tags, profiles, and community features | [`v0.2.3`](https://github.com/rinspacehq/rinspace-web/releases/tag/v0.2.3) | AGPL-3.0-only |
| [`rinspace-renderer`](https://github.com/rinspacehq/rinspace-renderer) | Rendering protocol and runtime for Markdown, LaTeX, Typst, diagrams, and PDF | [`v0.1.0-rc.2`](https://github.com/rinspacehq/rinspace-renderer/releases/tag/v0.1.0-rc.2) | AGPL-3.0-only |
| [`rinspace-editor-markdown`](https://github.com/rinspacehq/rinspace-editor-markdown) | Markdown editor built with Milkdown and Crepe | [`v0.3.4`](https://github.com/rinspacehq/rinspace-editor-markdown/releases/tag/v0.3.4) | MIT |
| [`gitea`](https://github.com/rinspacehq/gitea) | Rinspace's Gitea fork for content source and collaboration workflows | [`v1.27.2-rinspace.1`](https://github.com/rinspacehq/gitea/releases/tag/v1.27.2-rinspace.1) | MIT |
| [`mastodon`](https://github.com/rinspacehq/mastodon) | Mastodon fork used by the Rinspace inner world | [`rinspace-2026.09.26.2`](https://github.com/rinspacehq/mastodon/releases/tag/rinspace-2026.09.26.2) | AGPL-3.0 |

## Architecture

```mermaid
flowchart TB
    USER["Readers and authors"]

    subgraph EXPERIENCE["Two-world experience and authoring"]
        OUTER["Outer-world Web<br/>React · TypeScript · Vite"]
        EDITOR["Markdown editor<br/>Milkdown · Crepe · CodeMirror"]
        INNER["Inner-world community<br/>Mastodon · Rails · React"]
    end

    subgraph PRODUCT["Product services"]
        API["Business API / Runtime<br/>Go · Gin"]
        IDENTITY["Unified identity<br/>CloudBase Auth · OIDC"]
        CONTROL["Control Plane<br/>Publication · bindings · reconciliation"]
        GORSE["Gorse<br/>Recommendation ranking"]
    end

    subgraph SOURCE["Source and collaboration"]
        GITEA["Gitea<br/>Git history · Pull Requests · Issues"]
    end

    subgraph RENDERING["Rendering and publication"]
        RENDERER["Rinspace Renderer<br/>Go API · PostgreSQL · durable jobs"]
        ENGINES["Rendering engines<br/>Markdown · KaTeX · MathJax · Shiki<br/>LaTeXML · TeX SVG · Typst · PDF"]
    end

    subgraph DATA["Data and artifacts"]
        DATABASE["CloudBase PostgreSQL<br/>Product state · publication index"]
        STORAGE["CloudBase Storage<br/>Content-addressed public assets"]
        SOCIAL["Inner-world data layer<br/>PostgreSQL · Redis · object storage"]
    end

    USER --> OUTER
    USER --> INNER
    OUTER --> EDITOR
    OUTER <--> API
    OUTER --> IDENTITY
    INNER -->|OIDC| IDENTITY
    EDITOR -->|Save and publish| API
    API -->|Exact Git commit| GITEA
    GITEA -->|Durable publication event| CONTROL
    CONTROL -->|Source identity and job| RENDERER
    RENDERER <--> ENGINES
    RENDERER -->|Content-addressed assets| STORAGE
    RENDERER -->|Signed completion event| CONTROL
    CONTROL -->|Activate verified version| DATABASE
    API <--> DATABASE
    OUTER -->|Read public assets| STORAGE
    CONTROL -->|Identity, profile, and Tag bindings| INNER
    INNER <--> SOCIAL
    INNER -->|Visible candidates and feedback| GORSE
    GORSE -->|Ranking only| INNER
```

| Design principle | Implementation |
| --- | --- |
| Traceable source | Long-form content uses Git commits in Gitea as its authoritative source, retaining Pull Requests, Issues, and complete history. |
| Immutable publication | A publication is bound to an exact commit and artifact digest. A failed candidate leaves the previous successful version active. |
| Separation of responsibilities | Product services own business and identity concerns, the Control Plane orchestrates work, and the Renderer produces results through isolated engines. |
| Two worlds, one identity | The outer world presents knowledge, while the inner world hosts the local community; stable subjects and OIDC connect them. |
| Explicit sources of truth | Gitea owns long-form source, Mastodon owns social relationships and posts, and Gorse ranks candidates without owning them. |

## Technical Direction

Rinspace treats a Tag as a knowledge node with a stable identity, context, and lifecycle. Git stores long-form sources and their history, and each publication is bound to an exact commit. The Control Plane verifies source and task identity, the Renderer produces an immutable result, and the Web serves the verified publication.

Public components enter the Rinspace product at pinned versions and integrity digests. The Renderer supports both standalone local operation and Control Plane integration, keeping the public implementation and production consumption on the same version chain.

Current work focuses on:

- Developing the knowledge network formed by Tags, articles, books, and community relationships.
- Unifying Markdown, LaTeX, and Typst authoring and publication workflows.
- Faithfully presenting mathematics, references, diagrams, SVG, and long-form document structure.
- Improving reproducible builds, stable releases, and standalone deployment of public components.
- Opening more Rinspace components along clearly defined module boundaries.

## Current Capabilities and Boundaries

| Area | Current status |
| --- | --- |
| LaTeX | LaTeXML generates structured HTML. Documents that cannot be converted reliably are available as the original PDF. |
| Typst | HTML and PDF rendering are supported. HTML output is limited by the official `typst2html` implementation; PDF remains the reference for complex diagrams. |
| Markdown | Editor `v0.3.4` supports pasting source from VS Code as a code block and preserves a leading `#` as code. Compatibility with other source applications continues to improve. |
| Self-hosting | The Renderer and Markdown editor can be used independently. The backend, Control Plane, and production deployment system required for the complete site are not yet fully public. |

## Community Contributions

This table records community work that has been merged and included in a release. Dates are merge dates.

| Date | Contributor | Project | Adopted contribution | Released in |
| --- | --- | --- | --- | --- |
| 2026-10-03 | [`@xjn2005`](https://github.com/xjn2005) | [`rinspace-web` #25](https://github.com/rinspacehq/rinspace-web/pull/25) | Improved the profile layout and dark-mode biography display, added personal website links, and added tests | `v0.2.2`, retained in `v0.2.3` |
| 2026-10-09 | [`@xjn2005`](https://github.com/xjn2005) | [`rinspace-editor-markdown` #13](https://github.com/rinspacehq/rinspace-editor-markdown/pull/13) | Added VS Code source paste handling, corrected demo image URLs, and updated dependencies | `v0.3.4` |

We thank everyone who contributes code, tests, reproducible reports, translations, and design feedback. Project release notes will continue to record the exact version and scope in which accepted work appears.

## Contributing

| Area | Entry point |
| --- | --- |
| Web interface and browser behavior | [`rinspace-web/issues`](https://github.com/rinspacehq/rinspace-web/issues) |
| Rendering protocols, engines, and deployment | [`rinspace-renderer/issues`](https://github.com/rinspacehq/rinspace-renderer/issues) |
| Markdown editor | [`rinspace-editor-markdown/issues`](https://github.com/rinspacehq/rinspace-editor-markdown/issues) |
| Cross-project planning | [`.github/issues`](https://github.com/rinspacehq/.github/issues) |

Please use the private vulnerability reporting channel provided by the relevant repository for security reports.

## Acknowledgements

Rinspace is supported by many open-source projects. We thank their maintainers and contributors.

| Area | Open-source projects | Role in Rinspace |
| --- | --- | --- |
| Community and collaboration | [`Git`](https://git-scm.com/) · [`Gitea`](https://github.com/go-gitea/gitea) · [`Mastodon`](https://github.com/mastodon/mastodon) · [`Gorse`](https://github.com/gorse-io/gorse) | Source history, content collaboration, the inner-world community, and recommendation ranking |
| Web foundations | [`React`](https://github.com/facebook/react) · [`React Router`](https://github.com/remix-run/react-router) · [`TypeScript`](https://github.com/microsoft/TypeScript) · [`Vite`](https://github.com/vitejs/vite) · [`Tailwind CSS`](https://github.com/tailwindlabs/tailwindcss) · [`i18next`](https://github.com/i18next/i18next) · [`SWR`](https://github.com/vercel/swr) · [`Zustand`](https://github.com/pmndrs/zustand) | Outer-world UI, routing, styling, internationalization, and client state |
| Interface and interaction | [`Radix Primitives`](https://github.com/radix-ui/primitives) · [`Motion`](https://github.com/motiondivision/motion) · [`Lucide`](https://github.com/lucide-icons/lucide) · [`Bootstrap Icons`](https://github.com/twbs/icons) · [`Phosphor Icons`](https://github.com/phosphor-icons/core) | Accessible components, motion, and icon systems |
| Authoring and editing | [`Milkdown`](https://github.com/Milkdown/milkdown) / Crepe · [`ProseMirror`](https://github.com/ProseMirror/prosemirror) · [`CodeMirror`](https://github.com/codemirror/dev) | Markdown authoring, rich document structure, and code editing |
| Content and rendering | [`unified`](https://github.com/unifiedjs/unified) · [`remark`](https://github.com/remarkjs/remark) · [`rehype`](https://github.com/rehypejs/rehype) · [`KaTeX`](https://github.com/KaTeX/KaTeX) · [`MathJax`](https://github.com/mathjax/MathJax-src) · [`Shiki`](https://github.com/shikijs/shiki) | Markdown syntax trees, mathematical typesetting, and syntax highlighting |
| Typesetting and documents | [`Typst`](https://github.com/typst/typst) · [`LaTeXML`](https://github.com/brucemiller/LaTeXML) · [`TeX Live`](https://tug.org/texlive/) · [`dvisvgm`](https://github.com/mgieseki/dvisvgm) · [`PDF.js`](https://github.com/mozilla/pdf.js) | Document compilation, LaTeX conversion, SVG generation, and PDF support |
| Services and data | [`Go`](https://github.com/golang/go) · [`Gin`](https://github.com/gin-gonic/gin) · [`Node.js`](https://github.com/nodejs/node) · [`Ruby on Rails`](https://github.com/rails/rails) · [`Sidekiq`](https://github.com/sidekiq/sidekiq) · [`PostgreSQL`](https://github.com/postgres/postgres) · [`Redis`](https://github.com/redis/redis) · [`CloudBase JavaScript SDK`](https://github.com/TencentCloudBase/cloudbase-js-sdk) | Product services, rendering jobs, the community runtime, persistence, and session infrastructure |
| Engineering quality and documentation | [`Playwright`](https://github.com/microsoft/playwright) · [`Vitest`](https://github.com/vitest-dev/vitest) · [`Testing Library`](https://github.com/testing-library) · [`axe-core`](https://github.com/dequelabs/axe-core) · [`Mermaid`](https://github.com/mermaid-js/mermaid) | Browser validation, unit testing, accessibility checks, and technical diagrams |
| Typefaces | [`Noto Sans SC`](https://github.com/google/fonts/tree/main/ofl/notosanssc) · [`IBM Plex`](https://github.com/IBM/plex) · [`Newsreader`](https://github.com/productiontype/Newsreader) · [`Fira Code`](https://github.com/tonsky/FiraCode) · [`JetBrains Mono`](https://github.com/JetBrains/JetBrainsMono) · [`WenQuanYi Zen Hei`](https://sourceforge.net/projects/wqy/files/wqy-zenhei/) | Chinese text, interface, code, and document typography |

This table covers the principal upstream projects in the product architecture. Each repository's third-party notices, lockfiles, and release SBOMs record exact versions and the complete dependency set.

---

Last updated: 2026-10-10
