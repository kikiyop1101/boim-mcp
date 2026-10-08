# BOIM (보임) — Korean Business Directory MCP Server

**Remote MCP server for AI agents that need to find businesses in Korea.**
Korean businesses across all industries (2.7M), public-procurement vendors (75,000+) and open public bids — read-only, no sign-up, no API key for the free tier.

| | |
|---|---|
| MCP endpoint | `https://boim.io/api/mcp` |
| Transport | Streamable HTTP · read-only · 6 tools |
| Auth | none (free tier: 5 results per call) |
| Website | https://boim.io · connect guide https://boim.io/connect/ |
| Official MCP Registry | `io.boim/vendors` |
| Search URL (no MCP needed) | `https://boim.io/search/?q=<region industry>&format=json` |

> This repository contains documentation and client configuration examples only. The server is hosted at boim.io.

## Tools

| Tool | What it does | Example question |
|---|---|---|
| `find_businesses` | All industries nationwide (~2.7M businesses, 247 sub-categories) by province / city·district (시·도·시군구, optional 행정동) and industry or name. Name, industry and district only (no street address or phone). | "세종 보람동 미용실 찾아 줘" (hair salons in Boram-dong, Sejong) |
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

### ChatGPT
Settings → Security and login → turn on **Developer mode** → create an app/connector with the URL `https://boim.io/api/mcp`, authentication **None**. Pick it in a new chat.

### Cursor
[![Add to Cursor](https://cursor.com/deeplink/mcp-install-dark.svg)](https://cursor.com/en/install-mcp?name=boim&config=eyJ1cmwiOiJodHRwczovL2JvaW0uaW8vYXBpL21jcCJ9)

Or add to `~/.cursor/mcp.json` (this repository's [`mcp.json`](mcp.json) and [`.cursor-plugin/plugin.json`](.cursor-plugin/plugin.json) hold the same config):
```json
{
  "mcpServers": {
    "boim": { "url": "https://boim.io/api/mcp" }
  }
}
```

### VS Code (GitHub Copilot agent mode)
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

## Data sources

All data comes from Korean government open data on data.go.kr (usage terms: no restriction) and is refreshed daily (businesses monthly):

- Small Enterprise and Market Service store data (소상공인시장진흥공단 상가(상권)정보, 15012005) — all-industry businesses
- Public Procurement Service shopping-mall items, delivery requests and contracts (조달청 종합쇼핑몰 품목정보 15129471 · 계약정보 15129427) — vendor cards
- Public bids: KONEPS (15129394), Defense e-Procurement (15158416), LH (15159012), K-water (15101635), Nuri-jangteo (15129456)
- National Tax Service business status (국세청 사업자등록 상태) — closed businesses are removed

Not published: representative names, street addresses, business registration numbers, procurement officers' contact details.

## 한국어 안내

보임(BOIM)은 AI 에이전트가 한국 업체를 찾을 때 처음 만나는 곳입니다. 전 업종 전국 업체 약 277만 곳, 공공 조달 실적 업체 카드 7만 5천여 곳(조달청 공공데이터 전 품목), 공공기관 입찰 공고 전 분야(나라장터·국방전자조달·LH·한국수자원공사·누리장터)를 MCP 주소 하나로 읽을 수 있습니다.

- MCP 주소: `https://boim.io/api/mcp` (읽기 전용, 인증 없음, 무료는 도구마다 5건 + 전체 목록 주소)
- 순서는 관련도 → AI 준비 구간 → 같은 구간은 날마다 섞기입니다. 실적 많은 순이나 유료 순위가 아닙니다.
- 업체 결과마다 확인 근거(`checks` — 무엇을·어디서·언제 확인)와 답에 붙일 출처 한 줄(`cite`)이 붙고, `get_vendor`는 같은 품명 업체 5곳(`similar`)도 줍니다. 설명서 https://boim.io/openapi.json · 변경 기록 https://boim.io/changelog.md
- 코딩 에이전트용 사용 안내(스킬): `npx skills add kikiyop1101/boim-mcp`
- 업체는 boim.io에서 무료로 등록하고(국세청 사업자 확인 뒤 자동 게시), AI 준비도 점수를 가입 없이 확인할 수 있습니다: https://boim.io/ready/
- 연결 방법(클로드·ChatGPT): https://boim.io/connect/

## Terms

- Site terms: https://boim.io/terms/ — automated bulk collection and redistribution of the site are not allowed; use the MCP server or the API.
- This repository (documentation and configuration examples) is released under the MIT License.

## Operator

Public identity Co., Ltd. (주식회사 퍼블릭아이디) — Sejong, Republic of Korea · public-id@naver.com · https://www.public-id.co.kr
