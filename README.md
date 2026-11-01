# BOIM (보임) — Korean Business Directory MCP Server

**Remote MCP server for AI agents that need to find businesses in Korea.**
Korean businesses across all industries (2.7M+), public-procurement vendors (75,000+), open public bids and traditional markets with market days (1,393) — read-only, no sign-up, no API key for the free tier.

| | |
|---|---|
| MCP endpoint | `https://boim.io/api/mcp` |
| Transport | Streamable HTTP · read-only · 6 tools |
| Auth | none (free tier: 5 results per call) |
| Website | https://boim.io · connect guide https://boim.io/connect/ |
| Official MCP Registry | `io.boim/vendors` |
| Kakao PlayMCP (Korea) | [playmcp.kakao.com/mcp/100977376577565838](https://playmcp.kakao.com/mcp/100977376577565838) — add BOIM to your PlayMCP toolbox, then connect ChatGPT · Claude · OpenClaw once with `https://playmcp.kakao.com/mcp` (Kakao account) |
| Search URL (no MCP needed) | `https://boim.io/search/?q=<region industry>&format=json` |

> This repository contains documentation and client configuration examples only. The server is hosted at boim.io.

## Tools

| Tool | What it does | Example question |
|---|---|---|
| `find_businesses` | All industries nationwide (2.7M+ businesses, 247 sub-categories) by province / city·district (시·도·시군구, optional 행정동) and industry or name. Name, industry and district only (no street address or phone). When the industry is a market word (전통시장 · 오일장 · 장날 · "traditional market"), the answer also carries `markets` — public traditional markets in that area with market days, stores, parking and toilets. | "세종 보람동 미용실 찾아 줘" (hair salons in Boram-dong, Sejong) · "세종 장날 언제야?" |
| `search_vendors` | Vendor cards built from Public Procurement Service (조달청) data — every item category, last 12 months of public-procurement records — plus information registered by the businesses themselves. Filter by item, category group, region, name or certification. | "세종시에서 안내판 납품 실적이 있는 업체 알려 줘" |
| `get_vendor` | Full vendor card: records and amounts by item, main buyer agencies and regions, contract types, unit-price range, certifications, sources, update date and data limits. | "그 업체의 품명별 실적과 주요 납품 기관 보여 줘" |
| `find_public_bids` | Open public bids in all fields from KONEPS (나라장터), Defense e-Procurement (국방전자조달), LH, K-water (한국수자원공사) and Nuri-jangteo (누리장터), refreshed twice a day, closest deadline first, with source links. | "경기도 교통안전시설 입찰 공고 중 마감이 가까운 것 알려 줘" |
| `find_local_shops` | Neighborhood sign, advertising, print and copy shops by city·district·dong. | "대전 유성구에 인쇄물 맡길 가게 있어?" |
| `list_categories` | Procurement category groups and item names with vendor counts. | "보임에서 찾을 수 있는 품명 목록 알려 줘" |

**Ordering is not a ranking.** Results are sorted by relevance (region evidence) → AI-readiness band (how complete the business's own information is) → a daily shuffle within each band. No paid placement; the operator's own listing gets no boost.

**Free vs. full.** Without a key each tool returns up to 5 results, plus `not_shown` (how many more) and `more` (a public web list anyone can open). Full results are available with a BOIM API key (`Authorization: Bearer …` or `?key=`) — contact public-id@naver.com.

**Evidence and citation.** Vendor results carry `checks` (what was verified, by which public source, on which date — e.g. National Tax Service business status, Public Procurement Service deliveries) and `cite` (a ready-made source line with the card URL). `get_vendor` also returns `similar` — up to 5 businesses with the same main item (not a ranking). Tool names and inputs do not change; new response fields are only added. Full spec: [`openapi.json`](https://boim.io/openapi.json) · limits and errors: https://boim.io/connect/ · changes: [`changelog.md`](https://boim.io/changelog.md).

**Agent skill.** Teach a coding agent (Claude Code, Cursor, Codex…) when and how to use BOIM:
```bash
npx skills add kikiyop1101/boim-mcp
```
The skill lives in [`skills/boim/SKILL.md`](skills/boim/SKILL.md).

## Connect

### Claude (claude.ai · Claude Desktop — Pro/Max)
Customize → Connectors → **+ Add** → **Add custom connector** → name `BOIM`, URL `https://boim.io/api/mcp`, no authentication. Turn it on from the tools menu in a chat.

### Claude Code
```bash
claude mcp add --transport http boim https://boim.io/api/mcp
```

### Claude Code plugin (MCP server + skill)
This repository is also a Claude Code plugin marketplace. Add it once, then install the `boim` plugin:
```bash
claude plugin marketplace add kikiyop1101/boim-mcp
claude plugin install boim@boim-mcp
```
Inside a session: `/plugin marketplace add kikiyop1101/boim-mcp`, then `/plugin install boim@boim-mcp`.

The plugin ([`.claude-plugin/plugin.json`](.claude-plugin/plugin.json) + [`.mcp.json`](.mcp.json)) adds two things: the remote MCP server `https://boim.io/api/mcp` (read-only, no authentication) and the [`boim` skill](skills/boim/SKILL.md). It has no hooks, commands or local code. Your search words go only to boim.io, and the plugin keeps no data of its own.

### ChatGPT
Settings → Security and login → turn on **Developer mode** → create an app/connector with the URL `https://boim.io/api/mcp`, authentication **None**. Pick it in a new chat.

### Kakao PlayMCP (Korean users — ChatGPT · Claude · OpenClaw)
Open [BOIM on PlayMCP](https://playmcp.kakao.com/mcp/100977376577565838), sign in with a Kakao account and add BOIM to your toolbox (도구함).
Then connect your AI app once to the PlayMCP toolbox URL `https://playmcp.kakao.com/mcp` (ChatGPT developer mode, Claude custom connector; OpenClaw uses PlayMCP's own connection guide) — if the toolbox screen shows a different URL, use that one.
All tools in your toolbox, BOIM included, work through that one connection; BOIM answers are the same as from `https://boim.io/api/mcp`.

### Cursor
[![Add to Cursor](https://cursor.com/deeplink/mcp-install-dark.svg)](https://cursor.com/install-mcp?name=boim&config=eyJ1cmwiOiJodHRwczovL2JvaW0uaW8vYXBpL21jcCJ9)

Deeplink (format from Cursor's MCP install-link docs — `config` is base64 of the JSON config): `cursor://anysphere.cursor-deeplink/mcp/install?name=boim&config=eyJ1cmwiOiJodHRwczovL2JvaW0uaW8vYXBpL21jcCJ9`

Or add to `~/.cursor/mcp.json` (this repository's [`mcp.json`](mcp.json) and [`.cursor-plugin/plugin.json`](.cursor-plugin/plugin.json) hold the same config):
```json
{
  "mcpServers": {
    "boim": { "url": "https://boim.io/api/mcp" }
  }
}
```

### VS Code (GitHub Copilot agent mode)
One-click: open this link in a browser (VS Code's `vscode:mcp/install` URL handler, per the VS Code MCP developer guide):
`vscode:mcp/install?%7B%22name%22%3A%22boim%22%2C%22type%22%3A%22http%22%2C%22url%22%3A%22https%3A%2F%2Fboim.io%2Fapi%2Fmcp%22%7D`

Or from a terminal:
```bash
code --add-mcp "{\"name\":\"boim\",\"type\":\"http\",\"url\":\"https://boim.io/api/mcp\"}"
```
or `.vscode/mcp.json`:
```json
{
  "servers": {
    "boim": { "type": "http", "url": "https://boim.io/api/mcp" }
  }
}
```

### Antigravity (CLI · app)
```bash
agy plugin install https://github.com/kikiyop1101/boim-mcp
```
The plugin ([`plugin.json`](plugin.json) + [`mcp_config.json`](mcp_config.json)) adds the remote server and the [`boim` skill](skills/boim/SKILL.md). Or add the server to `~/.gemini/config/mcp_config.json` yourself:
```json
{
  "mcpServers": {
    "boim": { "serverUrl": "https://boim.io/api/mcp" }
  }
}
```

### Agent builders and other chat apps
BOIM is a plain remote MCP server (Streamable HTTP, no auth), so most apps only need the URL `https://boim.io/api/mcp`. Menu names below are as of 2026-10 — check each app's help if they move.

- **Dify** — Tools → **MCP** → add an MCP server (HTTP): server URL `https://boim.io/api/mcp`, a name and a server identifier (e.g. `boim`). Dify imports the 6 tools for your apps and workflows.
- **n8n** — attach an **MCP Client Tool** node to your AI Agent node: endpoint `https://boim.io/api/mcp`, server transport **HTTP Streamable** (BOIM does not serve the legacy SSE transport), authentication **None**.
- **Microsoft Copilot Studio** — agent → Tools → **Add a tool** → **New tool** → **Model Context Protocol**: name, description, server URL `https://boim.io/api/mcp`, authentication **None**.
- **Grok (grok.com)** — grok.com/connectors → **New Connector** → **Custom** → MCP server URL `https://boim.io/api/mcp`, no authentication.
- **Le Chat · Perplexity · Manus** — Settings → Connectors (Le Chat: **+ Add Connector** → **Custom MCP Connector**) → add a custom/remote MCP server with the URL `https://boim.io/api/mcp`, no authentication. Some plans or workspace admins limit custom connectors.
- **LangChain (Python)** — `pip install "langchain[mcp]"`, then:
  ```python
  from langchain.mcp import MCPAdapter
  async with MCPAdapter("https://boim.io/api/mcp") as adapter:
      tools = await adapter.list_tools()
  ```
  With the older `langchain-mcp-adapters`: `MultiServerMCPClient({"boim": {"transport": "streamable_http", "url": "https://boim.io/api/mcp"}})`.

### Clients that only speak stdio
```json
{
  "mcpServers": {
    "boim": { "command": "npx", "args": ["-y", "mcp-remote", "https://boim.io/api/mcp"] }
  }
}
```

### No MCP at all
Any agent that can fetch a URL can search:
`https://boim.io/search/?q=세종 미용실&format=json` (also `&format=md`, or `Accept: text/markdown`). Site summary for LLMs: https://boim.io/llms.txt

### Use BOIM as your agent's web-search backend (just change the URL)
BOIM answers the common search-API request shapes, so many agent tools can point a search setting at BOIM without code changes. Answers come from BOIM data only (Korean businesses, vendor cards with public procurement records, live public bid notices, traditional markets) — no general web results. Any API key string works; BOIM neither uses nor stores the keys or auth headers you send.

Most of these settings *replace* an app's one web-search slot. To keep general web search and add Korean business lookup next to it, use the MCP server above, or a second slot where the app has one (marked **side by side**). n8n: use the MCP Client Tool node — its SearXNG tool node has a fixed tool name, so a second SearXNG node for BOIM cannot sit next to a web one.

| Tool / SDK | Setting | Value | Checked |
|---|---|---|---|
| `tavily-python` | `TavilyClient(api_key="any", api_base_url=…)` | `https://boim.io/api/v1` | ✅ 2026-10-14 (search · extract) |
| `exa-py` | `Exa(api_key="any", base_url=…)` | `https://boim.io/api/v1` | ✅ 2026-10-14 (search) |
| `parallel-web` | `PARALLEL_BASE_URL` or `Parallel(api_key="any", base_url=…)` | `https://boim.io` | ✅ 2026-11-01 (search) |
| `perplexityai` | `PERPLEXITY_BASE_URL` or `Perplexity(api_key="any", base_url=…)` | `https://boim.io` | ✅ 2026-11-01 (search) |
| `firecrawl-py` | `Firecrawl(api_key="any", api_url=…)` | `https://boim.io` | ✅ 2026-10-14 (search · scrape) |
| `a2a-sdk` (A2A 1.0) | `create_client("https://boim.io")` | Agent Card `https://boim.io/.well-known/agent-card.json` | ✅ 2026-10-14 (SendMessage) |
| Hermes Agent | `TAVILY_BASE_URL` or `SEARXNG_URL` | `https://boim.io/api/v1` | ✅ 2026-10-14 (our own bots) |
| OpenClaw (SearXNG plugin) | `baseUrl` / `SEARXNG_BASE_URL` | `https://boim.io/api/v1` | metasearch JSON shape |
| Open WebUI | `SEARXNG_QUERY_URL` · `TAVILY_API_BASE_URL` (environment variable only) · `FIRECRAWL_API_BASE_URL` · `PERPLEXITY_SEARCH_API_URL` | `https://boim.io/api/v1/search?q=<query>` · `https://boim.io/api/v1` · `https://boim.io` · `https://boim.io/search` | request shape |
| Open WebUI — External search · External web loader | `EXTERNAL_WEB_SEARCH_URL` · `EXTERNAL_WEB_LOADER_URL` | `https://boim.io/api/v1/search?format=list` · `https://boim.io/api/v1/extract?format=list` | request shape (bare JSON array) |
| GPT Researcher | **side by side** `RETRIEVER=tavily,searx` + `SEARX_URL=…` · or `RETRIEVER=custom` + `RETRIEVER_ENDPOINT=…` + `RETRIEVER_ARG_FORMAT=list` | `https://boim.io/api/v1` · `https://boim.io/api/v1/search` | request shape |
| Dify (marketplace plugins) | **side by side** SearXNG base URL · Brave base URL · Firecrawl base URL | `https://boim.io/api/v1/search` · `https://boim.io/api/res/v1/web/search` · `https://boim.io` | request shape |
| RAGFlow | SearXNG node `searxng_url` (calls `<url>/search`) | `https://boim.io/api/v1` | request shape |
| Cherry Studio | Tavily provider API host · Parallel provider API host | `https://boim.io/api/v1` · `https://boim.io` | response fields (`request_id` · `search_id` · `session_id`) |
| Mastra (`@mastra/perplexity`) | `baseUrl` (any API key string) | `https://boim.io/api/v1` | response fields (`date` omitted when unknown) |
| LangChain | `langchain-tavily` `api_base_url` · `langchain-exa` `exa_base_url` · or the MCP adapters above. Legacy `langchain-community` (archived 2026-06): `SearxSearchWrapper(searx_host=…)` · `FireCrawlLoader(api_url=…)` | `https://boim.io/api/v1` · `https://boim.io/api/v1/search` · `https://boim.io` | request shape |
| LibreChat | `searxngInstanceUrl` · `firecrawlApiUrl` — the default `engines` list it sends is ignored | `https://boim.io/api/v1` · `https://boim.io` | request shape |

Not changeable by URL: Ollama's `web_search`/`web_fetch` in the Python/JS libraries call ollama.com directly, and OpenRouter's web-search engines are a fixed list — use the MCP server there. For tools that let you set the URL, BOIM also answers the same request shapes at `POST /api/web_search {query, max_results}` and `POST /api/web_fetch {url}`.

Endpoints (all CORS `*`, free, read-only):
- `POST /api/v1/search` `{"query" (string or array) | "q" | "objective" | "search_queries", "max_results" | "numResults" | "num" | "limit" | "count"}` → one response carrying `results[]` (title, url, content, score, id, text, snippet, highlights, excerpts), `organic[]` (title, link, snippet), `data[]` and `web.results[]`, plus `request_id` · `search_id` · `session_id`. Every result URL is unique. Also at `POST /search`, `/v1/search`, `/v1beta/search`, `/v1/search/public`, `/api/web_search` and the trailing-slash / base forms `/api/v1/search/` · `/api/v1/`.
- `?format=list` → a bare JSON array instead of an object: search `[{link, url, title, snippet, content, raw_content}]`, read `[{page_content, metadata}]`.
- `GET /api/v1/search?q=…&format=json` (also `?query=`) → metasearch shape `results[]` (title, url, content, engine, score), plus `webPages.value[]` and `organic_results[]` · `GET /api/v1/config` → metasearch engine/category config.
- `POST /api/v2/search` (also `/v2/search`) → `{success, data: {web: [...]}}` · `GET /api/res/v1/web/search?q=` (also `/res/v1/web/search`) → `web.results[]` · `GET /res/v1/llm/context?q=` (also under `/api`) → `grounding.generic[]` (url, title, snippets).
- Read: `POST /api/v1/extract {urls}` (also `/v1/extract`, `/v1beta/extract`) · `/api/v1/contents {urls|ids}` · `/api/v1/scrape`, `/v2/scrape {url}` · `/api/web_fetch {url}` — BOIM URLs only (vendor cards, traditional-market pages, business-list rows and search URLs returned by the search above). In search, ask for full text with `include_raw_content` / `text` and each result carries it as markdown.
- When nothing in BOIM matches (weather, news, other countries…), the answer is empty with a `handoff` hint, so your agent can fall back to a general web tool.
- A2A 1.0 JSON-RPC: `POST https://boim.io/api/a2a` (`SendMessage`; legacy `message/send` too) → markdown answer + JSON results.
- NLWeb-style: `GET https://boim.io/ask?query=세종+미용실&streaming=0` → `results[]` (url, name, site, score, description, schema_object). MCP is also reachable at `https://boim.io/mcp`.
- OpenAPI: https://boim.io/openapi.json · tool-sized spec (3 operations) for OpenAPI tool importers: https://boim.io/openapi-tools.json

## Data sources

Data comes from Korean government open data on data.go.kr (usage terms: no restriction), plus the information businesses register on BOIM themselves. Refreshed daily (businesses and traditional markets monthly):

- Small Enterprise and Market Service store data (소상공인시장진흥공단 상가(상권)정보, 15012005) — all-industry businesses
- Public Procurement Service shopping-mall items, delivery requests and contracts (조달청 종합쇼핑몰 품목정보 15129471 · 계약정보 15129427) — vendor cards
- Public bids: KONEPS (15129394), Defense e-Procurement (15158416), LH (15159012), K-water (15101635), Nuri-jangteo (15129456)
- National Tax Service business status (국세청 사업자등록 상태) — closed businesses are removed
- Traditional markets and market days (전통시장·장날, 1,393 markets — https://boim.io/markets/): Small Enterprise and Market Service national traditional-market standard data (전국전통시장표준데이터 15012894), market facilities (전통시장현황 15052837) and Onnuri gift-certificate merchant counts counted by market name (온누리상품권 가맹점 현황 3060079)

Not published: representative names, street addresses, business registration numbers, procurement officers' contact details. Exceptions: traditional markets are public facilities, so their public road-name address and main landline are shown; a business that registers itself shows the contact details it entered.

## 한국어 안내

보임(BOIM)은 AI 에이전트와 봇이 날마다 들르는 한국 업체 정보 통로입니다. 전 업종 전국 업체 약 276만 곳, 공공 조달 실적 업체 카드 약 7만 6천 곳(조달청 공공데이터 전 품목), 공공기관 입찰 공고 전 분야(나라장터·국방전자조달·LH·한국수자원공사·누리장터), 전국 전통시장 1,393곳의 장날·점포·주차·화장실(https://boim.io/markets/)을 MCP 주소 하나로 읽을 수 있습니다.

- MCP 주소: `https://boim.io/api/mcp` (읽기 전용, 인증 없음, 무료는 도구마다 5건 + 전체 목록 주소)
- 카카오 PlayMCP로 쓰기: [PlayMCP의 보임 쪽](https://playmcp.kakao.com/mcp/100977376577565838)에서 카카오 계정으로 로그인해 도구함에 담고, ChatGPT(개발자 모드)·클로드(커스텀 커넥터)·오픈클로에 도구함 주소 `https://playmcp.kakao.com/mcp`를 한 번 연결합니다(도구함 화면에 다른 주소가 보이면 그 주소). 결과는 위 주소와 같습니다.
- 순서는 관련도 → AI 준비 구간 → 같은 구간은 날마다 섞기입니다. 실적 많은 순이나 유료 순위가 아닙니다.
- 업체 결과마다 확인 근거(`checks` — 무엇을·어디서·언제 확인)와 답에 붙일 출처 한 줄(`cite`)이 붙고, `get_vendor`는 같은 품명 업체 5곳(`similar`)도 줍니다. 설명서 https://boim.io/openapi.json · 변경 기록 https://boim.io/changelog.md
- 코딩 에이전트용 사용 안내(스킬): `npx skills add kikiyop1101/boim-mcp`
- 클로드 코드 플러그인(MCP 서버+스킬): `claude plugin marketplace add kikiyop1101/boim-mcp` 다음 `claude plugin install boim@boim-mcp`
- 업체는 boim.io에서 무료로 등록하고(국세청 사업자 확인 뒤 자동 게시), AI 준비도 점수를 가입 없이 확인할 수 있습니다: https://boim.io/ready/
- 연결 방법(클로드·ChatGPT): https://boim.io/connect/
- 에이전트의 웹 검색 도구를 보임으로: 검색 설정의 주소만 `https://boim.io/api/v1`(읽기·크롤 주소는 `https://boim.io`)로 바꾸면 보임 데이터로 답합니다(위 표). 일반 웹 검색을 그대로 두려면 MCP나 두 번째 검색 칸에 붙이세요. 보낸 키·인증 머리는 쓰지도 저장하지도 않습니다. A2A 에이전트 카드 `https://boim.io/.well-known/agent-card.json` · 질문 창구 `https://boim.io/ask?query=`

## Terms

- Site terms: https://boim.io/terms/ — automated bulk collection and redistribution of the site are not allowed; use the MCP server or the API.
- This repository (documentation and configuration examples) is released under the MIT License.

## Operator

Public identity Co., Ltd. (주식회사 퍼블릭아이디) — Sejong, Republic of Korea · public-id@naver.com · https://www.public-id.co.kr
