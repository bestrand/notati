---
name: lovdata
description: Query Norwegian laws and regulations via the Lovdata API. Use this skill when the user asks about Norwegian laws (lover), regulations (forskrifter), legal text, legal sources (rettskilder), or wants to look up specific paragraphs, search legislation, or find current legal rules in Norway. Also trigger when user mentions Lovdata, norsk lov, gjeldende regelverk, or asks about specific Norwegian legal provisions. NOTE — requires a free API key from api.lovdata.no.
---

# Lovdata — Norwegian Laws & Regulations API

**Base URL:** `https://api.lovdata.no`
**Auth:** API key required — header `X-API-Key: {your-key}`. Register free at [api.lovdata.no](https://api.lovdata.no).
**License:** NLOD 2.0 — free for any purpose including AI/research.
**Attribution:** "Data fra Lovdata, lisensiert under NLOD 2.0."
**Rate limit:** 200 requests/minute.
**Format:** JSON responses. Legal documents in XML-compatible HTML with semantic structure (chapters, paragraphs, sections).

⚠️ **This API requires a free API key.** Without it, all endpoints return 401. The `ping` endpoint works without a key. Registration is self-service at api.lovdata.no.

## Endpoints (29 total)

### Legal source discovery

```bash
# List all legal sources with descriptions
curl -s "https://api.lovdata.no/v1/legalSource/list" -H "X-API-Key: YOUR_KEY"

# List legal areas (categories)
curl -s "https://api.lovdata.no/vocabulary/legalAreas" -H "X-API-Key: YOUR_KEY"
```

### Document listing and retrieval

```bash
# List all documents in a legal source (e.g., laws)
curl -s "https://api.lovdata.no/listBase?base=NL" -H "X-API-Key: YOUR_KEY"

# Get document metadata
curl -s "https://api.lovdata.no/documentMeta?base=NL&ruleFile=lov-2005-06-17-62" -H "X-API-Key: YOUR_KEY"

# Get document index (table of contents)
curl -s "https://api.lovdata.no/documentIndex?base=NL&ruleFile=lov-2005-06-17-62" -H "X-API-Key: YOUR_KEY"
```

### Structured rules (v1 — semantic access)

```bash
# List sources with structured documents
curl -s "https://api.lovdata.no/v1/structuredRules/list" -H "X-API-Key: YOUR_KEY"

# List documents in a source
curl -s "https://api.lovdata.no/v1/structuredRules/list/NL" -H "X-API-Key: YOUR_KEY"

# Get full structured document
curl -s "https://api.lovdata.no/v1/structuredRules/get/NL/lov-2005-06-17-62" -H "X-API-Key: YOUR_KEY"

# Get version in force at a specific date
curl -s "https://api.lovdata.no/v1/structuredRules/get/NL/lov-2005-06-17-62/2024-01-01" -H "X-API-Key: YOUR_KEY"

# Timeline of document versions
curl -s "https://api.lovdata.no/v1/structuredRules/timeline/NL/lov-2005-06-17-62" -H "X-API-Key: YOUR_KEY"
```

### Search

```bash
# Full-text search across all legal sources
curl -s "https://api.lovdata.no/v1/search?query=personopplysninger" -H "X-API-Key: YOUR_KEY"
```

### Rendering and references

```bash
# Render a document section as HTML
curl -s "https://api.lovdata.no/renderRefID?refID=lov-2005-06-17-62-§1" -H "X-API-Key: YOUR_KEY"

# Check existence of a reference
curl -s "https://api.lovdata.no/lookup?ref=lov-2005-06-17-62" -H "X-API-Key: YOUR_KEY"

# Find legal references in text
curl -s "https://api.lovdata.no/genref?text=Etter+arbeidsmiljøloven+§+14-9" -H "X-API-Key: YOUR_KEY"
```

### AI endpoints

```bash
# Strategy search (semantic/vector)
curl -s "https://api.lovdata.no/v1/ai/strategySearch?query=oppsigelse+av+arbeidsforhold" -H "X-API-Key: YOUR_KEY"

# Generate LLM response based on legal sections
curl -s -X POST "https://api.lovdata.no/v1/ai/generateResponse" \
  -H "X-API-Key: YOUR_KEY" \
  -H "Content-Type: application/json" \
  -d '{"messages": [...], "sectionIds": [...]}'
```

### Change tracking

```bash
# API changelog
curl -s "https://api.lovdata.no/apichanges" -H "X-API-Key: YOUR_KEY"

# Document change history
curl -s "https://api.lovdata.no/documentHistory?base=NL&ruleFile=lov-2005-06-17-62" -H "X-API-Key: YOUR_KEY"

# Base-level change log
curl -s "https://api.lovdata.no/baseHistory?base=NL" -H "X-API-Key: YOUR_KEY"
```

## Legal source codes

- `NL` — Norske lover (Norwegian laws)
- `NF` — Norske forskrifter (Norwegian regulations/central)
- Check `/v1/legalSource/list` for the full list.

## Notes

- Documents are delivered as semantic HTML with structured elements for chapters (kapittel), sections (§), paragraphs (ledd), and items (bokstav/nummer).
- The `structuredRules` endpoints provide the richest access with version history — use these for programmatic legal analysis.
- Lovdata publishes law/regulation amendments daily. The API always reflects the current consolidated version.
- The AI endpoints (`strategySearch`, `generateResponse`) enable RAG-style legal question answering.
- Test connectivity without API key: `curl -s "https://api.lovdata.no/ping"` → returns `Pong`.
