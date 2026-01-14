# Data Schemas (Inputs + Outputs)

This section describes the **canonical fields** used across ingestion and retrieval.

## 1) Metadata schema (stored under `metadata.*`)

Each document chunk/record supports the following metadata keys (use these for filtering):

- **`summary`** *(string)*: AI-generated 2–3 sentence summary
- **`source_type`** *(enum string)*:
  `pdf_page`, `policy_document`, `planning_document`, `design_guideline`, `technical_standard`,
  `research_paper`, `case_study`, `species_database`, `project_report`, `email`, `presentation`
- **`category`** *(enum string)*:
  `climate_microclimate`, `vegetation_greening`, `pedestrian_mobility`, `public_space`,
  `transit_infrastructure`, `building_architecture`, `water_irrigation`, `materials_surfaces`,
  `policy_regulation`, `simulation_tools`, `project_management`, `general`
- **`tags`** *(array of strings)*: specific keywords (e.g., `superblock`, `Cerdà`, `SUDS`, `plaça`, `Eixample`)
- **`themes`** *(array of strings)*: broader themes (e.g., `walkability`, `tactical_urbanism`, `green_corridors`, `heat_mitigation`)
- **`design_elements`** *(enum string)*:
  `trees`, `shrubs`, `groundcover`, `shade_structures`, `pergolas`, `paving`, `water_features`,
  `street_furniture`, `lighting`, `signage`, `buildings`, `pathways`, `none`
- **`publication_date`** *(date string)*: `YYYY-MM-DD`
- **`payload`** *(object)*: structured JSON (your extra fields, e.g. `{"project":"Superilles","lat":...}`)
- **`lat`** *(number)*: latitude
- **`long`** *(number)*: longitude

### Important routing field
- **`group_id`** *(string)*: required on ingestion; used to route to a database subset.

## 2) Ingestion input schema (client → API)

Ingestion endpoints accept **`multipart/form-data`**.

Common fields:
- **`group_id`** *(string, required)*
- **`payload`** *(string, optional but recommended)*: JSON string that will be stored as `metadata.payload`
- **`file_name`** *(string, optional)*: client-side label

Type-specific:
- **PDF / Image**: send binary file as **`file`**
- **Text**: send the text body as **`content`**

## 3) Retrieval input schema (client → API)

Retrieval endpoint is a **GET** request with query parameters:

- **`prompt`** *(string, required)*: semantic query text (what you are searching for)
- **`limit`** *(number, optional)*: how many results to return (default depends on backend)
- **`group_id`** *(string, required)*: routes to the correct database subset
- **`relevant_keys`** *(string, optional)*: comma-separated list of fields to include in results
- **`search_filter`** *(string, optional)*: JSON string (Qdrant filter) using `metadata.<field>` keys

## 4) Retrieval output schema (API → client)

Output is an array of result objects. The exact keys depend on `relevant_keys`, but typically include:

- **`url`** *(string)*: public file/page URL (e.g., S3)
- **`summary`** *(string)*
- **`content`** *(string)*: chunk text (if requested)
- **`tags`** *(array)* (if requested)
- **`payload`** *(object)* (if requested)
