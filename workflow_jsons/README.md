# Workflow JSONs (Reference Only)

**You don't need to import or set up these workflows.** They're already running on the server. The API endpoints work out of the box.

Use these files as:
- Reference to understand how the backend works
- Templates to customize for your own n8n instance later

---

## What's Here

| File | Type | Purpose |
|------|------|---------|
| `add_data_group_b (universal ingestion).json` | Main workflow | Handles all uploads (PDF, image, text) |
| `mini-app-iaac.json` | Main workflow | Example app with HTML frontend |
| `sub-data-fetching-iaac.json` | Subworkflow | Retrieval/search logic |
| `sub-data-ingestion.json` | Subworkflow | File processing + embedding logic |

---

## What are Subworkflows?

In n8n, a **subworkflow** is a reusable workflow that other workflows can call (like a function in code).

```
Main Workflow (receives webhook)
    └── calls Subworkflow (does the actual work)
                └── returns result to Main
```

**Why use them?**
- Keep workflows small and focused
- Reuse the same logic in multiple places
- Easier to maintain and debug

In our setup:
- Main workflows handle HTTP requests (webhooks)
- Subworkflows do the heavy lifting (AI processing, database operations)

---

## If You Want to Customize Later

1. Import the JSON into your own n8n instance
2. Update credentials (API keys, database connections)
3. Modify the logic to fit your needs
4. Deploy to your own endpoints

For now, just use the provided API endpoints. These files are here for learning.
