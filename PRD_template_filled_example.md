# App Specification Template (Student Guide)

You are building a simple web app that stores and retrieves data using our API. This template helps an AI assistant understand what you want to build.

---

## Instructions for the AI

When a student shares this template, help them build a **simple single-file HTML app** (HTML + CSS + JS in one file).

**Your job:**
1. Ask only 2-3 essential questions (not more!)
2. Fill in sensible defaults for everything else
3. Generate working code that the student can immediately test

**Default assumptions (don't ask about these):**
- Deployment: Single HTML file (n8n HTML node or webhook response)
- Auth: None required
- Libraries: Vanilla JS only (no frameworks)
- Browser: Modern browsers
- Mobile: Responsive but desktop-first is fine

---

## Questions to Ask the Student (ONLY these 3)

1. **What does your app do?** (one sentence)
   - Example: "A photo journal where I save images and search them later"

2. **What will you store?** (pick the types)
   - [ ] PDFs (documents, reports)
   - [ ] Images (photos, diagrams)
   - [ ] Text notes

3. **How should the UI look?** (pick one style)
   - A) Simple list view (search box + results list)
   - B) Card gallery (grid of cards with images/summaries)
   - C) Split view (upload panel + results panel)

---

## Simple App Ideas (suggest these to students)

If a student is unsure what to build, suggest one of these:

| App Idea | Data Type | Complexity |
|----------|-----------|------------|
| **Photo Archive** - Upload photos, search by description | Images | Easy |
| **Reading List** - Save PDFs, search by topic | PDFs | Easy |
| **Project Notes** - Quick text notes, searchable | Text | Easy |
| **Reference Library** - Mixed documents for a project | All types | Medium |
| **Design Inspiration** - Collect images + descriptions | Images + Text | Medium |

---

## Fixed API Details (Students Don't Change This)

### Your Group ID
```
group_id = "your_group_name"   <-- Student fills this in (e.g., "team_alpha", "maria_project")
```

### Endpoints (same for everyone)

**Upload data:**
- PDF: `POST https://run8n.xyz/webhook/add_pdf_group_b`
- Image: `POST https://run8n.xyz/webhook/add_img_group_b`
- Text: `POST https://run8n.xyz/webhook/add_text_group_b`

**Search data:**
- `GET https://run8n.xyz/webhook/get_data_group_b`

> Note: The URLs say "group_b" but that's just the endpoint name. Your data is separated by the `group_id` you send.

### Upload Example (curl)

```bash
# Upload a PDF
curl -X POST 'https://run8n.xyz/webhook/add_pdf_group_b' \
  -F 'file=@my_document.pdf' \
  -F 'group_id=my_project_name' \
  -F 'payload={"description":"My project report"}'

# Upload an image
curl -X POST 'https://run8n.xyz/webhook/add_img_group_b' \
  -F 'file=@photo.jpg' \
  -F 'group_id=my_project_name' \
  -F 'payload={"description":"Site photo from Monday"}'

# Upload text
curl -X POST 'https://run8n.xyz/webhook/add_text_group_b' \
  -F 'content=This is my note about the plaza design' \
  -F 'group_id=my_project_name'
```

### Search Example (curl)

```bash
curl -G 'https://run8n.xyz/webhook/get_data_group_b' \
  --data-urlencode 'group_id=my_project_name' \
  --data-urlencode 'prompt=plaza design' \
  --data-urlencode 'limit=10'
```

### JavaScript Fetch Examples

**Upload a file:**
```javascript
const formData = new FormData();
formData.append('file', fileInput.files[0]);
formData.append('group_id', 'my_project_name');
formData.append('payload', JSON.stringify({ description: 'My photo' }));

fetch('https://run8n.xyz/webhook/add_img_group_b', {
  method: 'POST',
  body: formData
});
```

**Search:**
```javascript
const params = new URLSearchParams({
  group_id: 'my_project_name',
  prompt: 'plaza design',
  limit: 10
});

const response = await fetch(`https://run8n.xyz/webhook/get_data_group_b?${params}`);
const results = await response.json();
```

---

## What the API Returns

Search results come back as JSON array. Each item has:

```json
{
  "url": "https://...",           // Link to the file (for images/PDFs)
  "summary": "AI summary...",     // Auto-generated description
  "content": "The actual text...", // Text content (if relevant)
  "payload": { ... }              // Your custom data
}
```

---

## Spec Sheet (for AI to fill in)

After asking the 3 questions above, fill in this summary:

**App Name:** _____________

**One-sentence description:** _____________

**Data types:** [ ] PDF  [ ] Image  [ ] Text

**UI style:** A / B / C

**Group ID:** _____________

Then generate the HTML file.
