# IAAC 2026 — Build Your Own Data App

A simple API for storing and searching PDFs, images, and text. Each team gets their own database.

## Quick Start

1. **Download this repo** (or clone it)
2. **Dump all `.md` files into ChatGPT** (or use with Claude/Cursor/VSCode)
3. **Fill out the template** in `PRD_template.md`
4. **Let the AI generate your app**

---

## What You Get

| Feature | Description |
|---------|-------------|
| **Persistent Storage** | Upload files → they stay forever (per group) |
| **Semantic Search** | Find things by meaning ("photos of shaded plazas") |
| **Filter Search** | Find things by exact fields (category, tags, etc.) |
| **Auto-Summaries** | PDFs and images get AI-generated descriptions |

---

## Your Task

**Build a simple web app that stores and retrieves data.**

Examples:
- Photo archive for site visits
- Document library for research
- Notes app with smart search
- Reference collection for a project

Keep it simple! A working demo is better than an ambitious failure.

---

## The API (4 endpoints)

**Base URL:** `https://run8n.xyz`

| Action | Method | Endpoint |
|--------|--------|----------|
| Upload PDF | POST | `/webhook/add_pdf_group_b` |
| Upload Image | POST | `/webhook/add_img_group_b` |
| Upload Text | POST | `/webhook/add_text_group_b` |
| Search | GET | `/webhook/get_data_group_b` |

> The URLs say "group_b" but your data is separated by the `group_id` you send.

---

## Option A: n8n HTML Node (Recommended)

Build your app as a single HTML file and serve it from n8n.

**Pros:** No hosting needed, everything in one place
**Cons:** Single file only (HTML + CSS + JS together)

### n8n HTML Node Tips

```html
<!-- Inject data from n8n workflow -->
<script>
const DATA_STRING = `{{ JSON.stringify($json) }}`;
const data = JSON.parse(DATA_STRING);
</script>
```

For static pages, use the **Webhook → HTML Node → Respond to Webhook** pattern.

---

## Option B: External Hosting

Host your HTML anywhere (GitHub Pages, Netlify, your own server) and call the API via fetch.

**Pros:** Multiple files, easier debugging
**Cons:** Need to host it somewhere

---

## Features for Planners & Architects

### GPS Coordinates
Store location data with your uploads:

```javascript
// Get user's location
navigator.geolocation.getCurrentPosition(pos => {
  const payload = {
    lat: pos.coords.latitude,
    long: pos.coords.longitude,
    description: "Site visit photo"
  };
  // Include in your upload...
});
```

### Camera Access
Capture photos directly in your app:

```html
<input type="file" accept="image/*" capture="environment" id="camera">
```

```javascript
document.getElementById('camera').addEventListener('change', async (e) => {
  const file = e.target.files[0];
  // Upload the captured image...
});
```

### Map Integration
Display locations with Leaflet (CDN allowed):

```html
<link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
<script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
```

---

## Using AI to Build Your App

### Step 1: Fill the template
Open `PRD_template.md` and answer the 4 questions.

### Step 2: Give everything to AI
Upload these files to ChatGPT/Claude:
- `PRD_template.md` (your filled template)
- `PRD_template_filled_example.md` (full instructions)
- `n8n_data_endpoints.md` (API details)

### Step 3: Iterate
Ask the AI to generate the HTML. Test it. Ask for fixes. Repeat.

**Tip:** Start with a minimal version, then add features one at a time.

---

## Files in This Repo

| File | Purpose |
|------|---------|
| `README.md` | This file |
| `PRD_template.md` | Blank template — fill this out |
| `PRD_template_filled_example.md` | Full guide with API examples |
| `n8n_data_endpoints.md` | Detailed API documentation |
| `datas_schemas.md` | All available metadata fields |
| `workflow_jsons/` | Example n8n workflows |

---

## Need Help?

1. Re-read the docs (seriously, the answer is usually there)
2. Ask your AI assistant (paste the error message)
3. Ask a classmate
4. Ask the instructor

Good luck!
