# n8n Data Endpoints (Production)

# n8n Data APIs (IAAC 2026) — Quick Guide

These endpoints power a **persistent database** for your projects. Each group gets their own isolated storage.

## What You Can Do

- **Store**: PDFs (auto-split to pages), images, or text notes
- **Search**: Two ways to find your data:
  - **Semantic search**: Find items by meaning (e.g., "photos of plazas with trees")
  - **Filter search**: Find items by exact fields (e.g., all items with `category = "vegetation"`)
  - **Combined**: Use both at once!

> **Key insight**: This is BOTH a vector database (AI-powered semantic search) AND a regular database (exact filter queries). Use whichever fits your app!

This documentation is written so you can paste it into an AI model and build client apps quickly.

## Base URL

All endpoints are hosted under:

- `https://run8n.xyz`

> During development you may see `/webhook-test/...` URLs. For “live” endpoints we use `/webhook/...`.

## Concepts

- **Endpoints do NOT change with `group_id`**: even though endpoint names contain `group_b`, the routes are fixed. You always call the same endpoints and select the dataset using `group_id`.
- **`group_id`**: Required for ingestion and retrieval. It is used server-side to route to the correct database subset. `group_id` is **not** part of `search_filter`.
- **`payload`**: A structured JSON object you send (as a JSON string in ingestion requests). Use this for any additional metadata you want to keep with a record.
- **`search_filter`**: A **Qdrant filter JSON object** (sent as a JSON string in the HTTP request, then parsed server-side). Keys must be `metadata.<field>`.

API reference: see n8n_data_endpoints.md in the same folder
for data schemas: see data_schemas.md in the same folder

Base URL:

- `https://run8n.xyz`

> These are the **live** endpoints (no `-test` in the URL path). For local debugging, n8n also has `/webhook-test/...` which expires quickly in test mode.

## IMPORTANT: Endpoints do NOT change with `group_id`

- The endpoint routes are **fixed** (they unfortunately include `group_b` in the path name).
- Students/apps must choose the dataset via the **`group_id` field** they send (ingestion + retrieval).
- So: **do not invent new endpoints for other groups**. Always use the endpoints below, and only change `group_id`.

---

## 1) Ingestion (Add data)

All ingestion endpoints accept **`multipart/form-data`**.

### 1.1 `POST /webhook/add_pdf_group_b`

Uploads a PDF. The backend may split it into pages and ingest page-level chunks.

**Required**
- `file` (binary PDF)
- `group_id` (string) ← **this selects the dataset/group**

**Recommended**
- `payload` (JSON string; becomes `metadata.payload`)
- `file_name` (string; label for humans)

**Minimal curl**

```bash
curl -X POST 'https://run8n.xyz/webhook/add_pdf_group_b' \
  -F 'file=@/path/to/document.pdf;type=application/pdf' \
  -F 'group_id=group_b' \
  -F 'file_name=document.pdf' \
  -F 'payload={"lat":41.3851,"long":2.1734}'
```



### 1.2 `POST /webhook/add_img_group_b`

Uploads a single image.

**Required**
- `file` (binary image)
- `group_id` (string) ← **this selects the dataset/group**

**Recommended**
- `payload` (JSON string)
- `file_name` (string)

**Minimal curl**

```bash
curl -X POST 'https://run8n.xyz/webhook/add_img_group_b' \
  -F 'file=@/path/to/image.jpg;type=image/jpeg' \
  -F 'group_id=group_b' \
  -F 'file_name=image.jpg' \
  -F 'payload={"source_type":"case_study","category":"public_space","tags":["superblock"],"lat":41.3851,"long":2.1734}'
```

### 1.3 `POST /webhook/add_text_group_b`

Ingests text content (no binary upload).

**Required**
- `content` (string)
- `group_id` (string) ← **this selects the dataset/group**

**Recommended**
- `payload` (JSON string)
- `file_name` (string; label for humans)

**Minimal curl**

```bash
curl -X POST 'https://run8n.xyz/webhook/add_text_group_b' \
  -F 'content=Short note about shade trees and irrigation for Mediterranean plazas.' \
  -F 'group_id=group_b' \
  -F 'file_name=text_note_001' \
  -F 'payload={"source_type":"design_guideline","category":"vegetation_greening","tags":["trees","irrigation"]}'
```

---

## 2) Retrieval (Get data / search)

### 2.1 `GET /webhook/get_data_group_b`

Semantic retrieval with optional strict metadata filtering.

**Query parameters**
- `prompt` *(string)*: semantic query text (required)
- `group_id` *(string)*: routes to the correct DB subset (required; **not part of `search_filter`**)
- `limit` *(number)*: suggested `10–40` depending on use (optional)
- `relevant_keys` *(string)*: comma-separated list of keys to return (optional)
- `search_filter` *(string)*: JSON string for Qdrant filtering (optional)

### `relevant_keys` (recommended default)

Start with:

- `url, summary, content, tags, payload, lat, long, source_type, category`

Use fewer keys for “scouting” (fast scan), and more keys for “detail” passes.

### `search_filter` (IMPORTANT — exact working pattern)

`search_filter` must be a **JSON string** that parses into a Qdrant filter object.

**Use this pattern exactly:**

- Always wrap conditions inside `should` / `must` / `must_not`
- Always use `metadata.<field>` keys

Example (single condition using `should`):

```json
{
  "should": [
    {
      "key": "metadata.category",
      "match": { "value": "vegetation_greening" }
    }
  ]
}
```

Example (AND logic with `must`):

```json
{
  "must": [
    { "key": "metadata.category", "match": { "value": "vegetation_greening" } },
    { "key": "metadata.geographic_scope", "match": { "value": "city_riyadh" } }
  ]
}
```

Example (OR logic with `should`):

```json
{
  "should": [
    { "key": "metadata.source_type", "match": { "value": "policy_document" } },
    { "key": "metadata.source_type", "match": { "value": "design_guideline" } }
  ]
}
```

> Reference: Qdrant filtering clauses (`must` / `should` / `must_not`) in the official docs: `https://qdrant.tech/documentation/concepts/filtering/`.

### Minimal curl (no filter)

```bash
curl -G 'https://run8n.xyz/webhook/get_data_group_b' \
  --data-urlencode 'group_id=group_b' \
  --data-urlencode 'prompt=trees' \
  --data-urlencode 'limit=25' \
  --data-urlencode 'relevant_keys=url, summary, content, tags, payload, lat, long, source_type, category' \
  --data-urlencode 'search_filter={}'
```

### Minimal curl (with filter)

```bash
curl -G 'https://run8n.xyz/webhook/get_data_group_b' \
  --data-urlencode 'group_id=group_b' \
  --data-urlencode 'prompt=trees' \
  --data-urlencode 'limit=25' \
  --data-urlencode 'relevant_keys=url, summary, content, tags, payload, lat, long, source_type, category' \
  --data-urlencode 'search_filter={"should":[{"key":"metadata.category","match":{"value":"vegetation_greening"}}]}'
```

---

## 3) Suggested usage pattern (for students)

### Pattern A: Scouting → Detail
- **Scouting**: `limit=20–40`, loose filters, `relevant_keys=url,summary,source_type,category,tags`
- **Detail**: `limit=5–10`, strict filters from scouting, `relevant_keys=url,summary,content,payload,lat,long`

### Pattern B: Image → Text query
If a user uploads an image, first generate a short description (caption), then use that caption as the `prompt` for retrieval.

---

## 4) Simple Recipes

### Recipe 1: Pure semantic search (no filter)
Best for: "Find anything related to X"

```bash
curl -G 'https://run8n.xyz/webhook/get_data_group_b' \
  --data-urlencode 'group_id=my_group' \
  --data-urlencode 'prompt=sustainable drainage systems' \
  --data-urlencode 'limit=10'
```

### Recipe 2: Filter-only search (no AI)
Best for: "Get all items of type X"
Use an empty prompt and rely on filters:

```bash
curl -G 'https://run8n.xyz/webhook/get_data_group_b' \
  --data-urlencode 'group_id=my_group' \
  --data-urlencode 'prompt=.' \
  --data-urlencode 'limit=50' \
  --data-urlencode 'search_filter={"must":[{"key":"metadata.source_type","match":{"value":"case_study"}}]}'
```

### Recipe 3: Hybrid (semantic + filter)
Best for: "Find X within category Y"

```bash
curl -G 'https://run8n.xyz/webhook/get_data_group_b' \
  --data-urlencode 'group_id=my_group' \
  --data-urlencode 'prompt=shade and cooling' \
  --data-urlencode 'limit=10' \
  --data-urlencode 'search_filter={"must":[{"key":"metadata.category","match":{"value":"vegetation_greening"}}]}'
```

---

## 5) For Students: Quick Start

1. **Pick your group_id** - use something unique like `maria_project` or `team_alpha`
2. **Upload some data** - use curl or build a simple HTML form
3. **Wait ~60 seconds** - the system needs time to process uploads
4. **Search your data** - use the GET endpoint with your group_id

That's it! Your data is persistent and isolated from other groups.
