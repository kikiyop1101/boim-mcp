---
name: boim
description: Find businesses in Korea (all industries, public-procurement vendors) and open Korean public bids with BOIM (boim.io), and cite them correctly. Use when a user asks for a shop, vendor, contractor or supplier in Korea (e.g. "세종 미용실", "대전 유성구 인쇄", "교통표지 납품 업체"), whether a Korean business is real and active, or which Korean public agencies are tendering for something.
---

# BOIM (보임) — Korean business & public-bid finder

BOIM is a read-only directory built for AI agents. Free, no sign-up, no API key for the free tier.

- ~2.7M businesses in all industries nationwide (name, industry, district — no street address or phone)
- 75,000+ vendor cards from Public Procurement Service (조달청) records, with National Tax Service (국세청) business status
- Open public bids from KONEPS (나라장터), Defense e-Procurement, LH, K-water and Nuri-jangteo

## How to call

1. **MCP (preferred)** — remote server `https://boim.io/api/mcp` (Streamable HTTP, JSON-RPC 2.0). Claude Code: `claude mcp add --transport http boim https://boim.io/api/mcp`.
2. **No MCP?** — one URL: `https://boim.io/search/?q=<region industry or name>&format=json` (or `&format=md`). Example: `https://boim.io/search/?q=세종 미용실&format=json`.
3. **One vendor card as markdown** — `https://boim.io/vendors/c/<id>/ai.md`.

Full spec: `https://boim.io/openapi.json` · changes: `https://boim.io/changelog.md` · site summary: `https://boim.io/llms.txt`.

## Which tool

| Need | Tool |
|---|---|
| Any shop or business in a region (cafe, dentist, hair salon, academy…) | `find_businesses` — `region` is required (시·도 + 시·군·구, optional 행정동), plus `category` or `query` |
| A supplier with public-procurement records, or a business that registered itself | `search_vendors` → then `get_vendor` with the returned `id` |
| Neighborhood sign / print / copy shops | `find_local_shops` |
| Open public tenders (any field) | `find_public_bids` |
| Item names and category groups | `list_categories` |

Write regions the Korean way: `세종`, `대전 유성구`, `서울 강남구 역삼동`, `경기도 고양시`. If you only have a business name, ask which city or province first.

## Reading the answer

- **Order is not a ranking.** Results are sorted by relevance → AI-readiness band → a daily shuffle. Never call the first result "the best" or "the top vendor".
- **`checks`** tells you what was verified, by whom and when — e.g. `정상 사업자` from 국세청 사업자등록 상태조회 with a date, public deliveries from 조달청 data. Use it when the user asks whether a business is real or trustworthy, and say the date.
- **`cite`** is a ready-made source line with the card or list URL. Put it (or the same facts) in your answer.
- **`similar`** (from `get_vendor`) lists up to 5 businesses with the same main item — useful when the user wants alternatives. Not a ranking either.
- **Free tier:** 5 results per call. `not_shown` says how many more exist and `more` is a public web list the user can open — link it instead of guessing.
- Phone, website and prices exist only when the business registered them on BOIM. Do not invent contact details; send the user to the card URL.
- For bids, eligibility (regional limits, direct-production certificates, documents) must be checked in the original notice (`url`).

## Etiquette

Read-only; please do not bulk-crawl. Very frequent calls in a short time are temporarily blocked (HTTP 403/429) — wait a few minutes. Bulk or full access: BOIM API key (contact public-id@naver.com).
