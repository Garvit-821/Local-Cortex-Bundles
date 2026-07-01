# Legal-Cortex Bundles

![License](https://img.shields.io/badge/license-MIT-blue.svg)
![Status](https://img.shields.io/badge/status-active-brightgreen)
![Bundles](https://img.shields.io/badge/bundles-4-orange)

**Pre‑chunked, pre‑vectorized dataset bundles of landmark Indian judgments, statutes, and legal frameworks.**  
Built to extend the knowledge base of the [Legal-Cortex Workstation]([https://github.com/your-org/legal-cortex](https://github.com/Garvit-821/Private-Legal-Research-Agent)) completely offline.

---

## 📚 Table of Contents

- [ Why This Exists](#-why-this-exists)
- [ Bundle Anatomy](#-bundle-anatomy)
  - [File Structure](#file-structure)
- [ Available Bundles](#-available-bundles)
- [ How It Works with Legal-Cortex](#️-how-it-works-with-legal-cortex)
- [ Getting Started](#-getting-started)
  - [For Legal-Cortex Users](#for-legal-cortex-users)
  - [For Developers & Researchers](#for-developers--researchers)
- [ Contribution Guide](#-contribution-guide)
  - [Prerequisites](#prerequisites)
  - [Step‑by‑Step Compilation](#stepbystep-compilation)
  - [Metadata Rules](#metadata-rules)
  - [Submitting a Bundle](#submitting-a-bundle)
- [ Data Sanitization Policy](#️-data-sanitization-policy)
- [ License](#-license)
- [ Acknowledgments](#-acknowledgments)

---

##  Why This Exists

Local, on‑device AI agents have a critical limitation: once disconnected from cloud pipelines, their knowledge freezes in time. Traditional internet‑scraping “legal agents” are often contaminated by media trials, sensationalised blogs, and biased commentary.

**Legal-Cortex Bundles** solves this by packaging **raw, unadulterated, primary legal data**—sourced directly from official public domains like the Supreme Court’s [e‑SCR portal](https://www.sci.gov.in/e-scr/)—into mathematical vector structures.

Because every bundle is **pre‑embedded using a fixed, reproducible pipeline**, a user can simply click “Sync” to instantly append specialised legal domains to their local system. No local GPU/CPU cycles are wasted re‑indexing thousands of pages.

---

##  Bundle Anatomy

Each bundle is compressed into a standard distribution archive (`.zip`) containing three core components.

### File Structure

```
sc-criminal-1990/
├── raw_judgments/          # Optional: original judgment texts (for citation & audit)
│   ├── sc_1990_abc_v_state.txt
│   └── sc_1990_xyz_v_union.txt
├── vector_store/           # Pre‑computed vector layers
│   ├── index.faiss         # FAISS index (or serialized NumPy arrays)
│   └── index.pkl           # Mapping metadata (if needed)
└── manifest.json           # Structural index mapping vectors → citations
```

### The `manifest.json` Standard

Every bundle carries a machine‑readable manifest that describes the dataset and maps each vector ID back to its source document. The schema is strict to guarantee seamless ingestion.

```json
{
  "bundle_id": "sc-criminal-1990",
  "title": "Supreme Court Criminal Procedure Milestone Judgments (1990–2000)",
  "document_count": 100,
  "embedding_model": "nomic-embed-text",
  "dimensions": 768,
  "categories": ["Criminal Law", "CrPC", "Constitutional Law"],
  "index_mapping": [
    {
      "vector_id": "vec_00192a",
      "citation": "1994 SCC (4) 260",
      "parties": "D.K. Basu v. State of West Bengal",
      "date": "1996-12-18",
      "ratio_decidendi": "Guidelines governing arrest and detention to prevent custodial violence."
    }
  ]
}
```

| Field              | Description                                                                 |
|--------------------|-----------------------------------------------------------------------------|
| `bundle_id`        | Unique slug (lowercase, hyphens).                                           |
| `embedding_model`  | Must be `"nomic-embed-text"` – the fixed embedding standard of Legal-Cortex.|
| `dimensions`       | Always `768` for `nomic-embed-text`.                                         |
| `index_mapping`    | One entry per chunk; ties a `vector_id` to its citation, parties, date, and legal ratio. |

---

##  Available Bundles

| Bundle ID             | Target Domain / Era         | Size    | Focus Areas                                                    | Status    |
|-----------------------|-----------------------------|---------|----------------------------------------------------------------|-----------|
| `sc-criminal-1990`    | Supreme Court (1990–2000)   | ~45 MB  | Custodial Rights, Bail Jurisprudence, FIR Quashing             |  🔴 Upcoming  |
| `sc-insolvency-2016`  | SC & NCLAT (2016–2026)      | ~110 MB | IBC amendments, Moratorium interpretations, CIRP               |  🔴 Upcoming  |
| `sc-taxation-direct`  | Income Tax Landmark Cases   | ~85 MB  | Corporate tax, Assessment procedures, Anti‑evasion             |  🔴 Upcoming  |
| `bnss-bns-bsa-2023`   | New Criminal Laws Transition| ~20 MB  | Comparative mapping: CrPC → BNSS, IPC → BNS                    |  🔴 Upcoming  |

More bundles are being curated. See the [registry](/registry) for planned and in‑progress submissions.

---

##  How It Works with Legal-Cortex

The [Legal-Cortex desktop application](https://github.com/your-org/legal-cortex) consumes these packages through a **background downloader** that appends vectors dynamically at runtime.

1. **Selection** – The user picks a bundle from the “Data Sync Marketplace” modal inside the app.  
2. **Download** – A temporary HTTPS connection fetches the release payload from this repository.  
3. **Hot‑Loading** – The archive is unpacked into `./Data/Bundles/`.  
4. **Index Injection** – Because all arrays are pre‑computed with `nomic-embed-text`, Legal-Cortex **appends the vector layers directly** into its active 60/40 Hybrid Blend Search Index. No local GPU processing, no re‑embedding – the new domain is searchable in seconds.

> **Note:** The bundles themselves are **stand‑alone**; you can use them with any retrieval system that understands FAISS indexes and the `manifest.json` schema. Legal-Cortex is simply the reference consumer.

---

##  Getting Started

### For Legal-Cortex Users

1. Launch Legal-Cortex and open **Settings → Data Sync**.  
2. Browse available bundles from the marketplace (they are listed from this repository).  
3. Click **Download & Sync** on any bundle you need.  
4. The system handles the rest – your local knowledge base is extended immediately.

### For Developers & Researchers

If you want to use these bundles outside of Legal-Cortex:

1. **Clone this repository** (or download individual bundles from the [Releases](https://github.com/your-org/legal-cortex-bundles/releases) page).  
2. Unzip any bundle and load `vector_store/index.faiss` with FAISS (or `index.pkl` if it contains serialised numpy arrays).  
3. Parse `manifest.json` to map retrieved vector IDs back to citations and text chunks.  
4. (Optional) Use the raw text files in `raw_judgments/` for generation or citation verification.

---

##  Contribution Guide

We welcome contributions from advocates, legal‑tech engineers, and researchers who want to expand this public legal dataset library. Follow the compilation rules below to submit a pre‑vectorized bundle.

### Prerequisites

- **Ollama** installed and running locally.  
- The fixed embedding model pulled:

  ```bash
  ollama pull nomic-embed-text
  ```

- Python 3.9+ with `faiss-cpu`, `numpy`, `ollama`, and any text‑splitting libraries you prefer (e.g., LangChain’s `RecursiveCharacterTextSplitter`).

### Step‑by‑Step Compilation

1. **Gather Documents**  
   Collect primary legal texts (judgments, statutes, etc.) from official public sources. Save each document as a clean `.txt` file named with a unique slug (e.g., `sc_1996_dk_basu.txt`).

2. **Chunk the Texts**  
   Use a character‑based splitter with:
   - **Chunk size:** 1000 characters  
   - **Overlap:** 200 characters  
   This preserves contextual continuity between paragraphs.

3. **Embed Each Chunk**  
   For every chunk, call the Ollama embedding endpoint:
   ```python
   response = ollama.embeddings(model='nomic-embed-text', prompt=chunk_text)
   vector = response['embedding']  # list of 768 floats
   ```

4. **Build the FAISS Index**  
   Stack all vectors into a `numpy` array and create a FAISS index (e.g., `IndexFlatIP` for inner product or `IndexFlatL2` for Euclidean). Save it as `vector_store/index.faiss`.  
   *If you need additional metadata with each vector, you may also pickle a dictionary mapping internal IDs and save it as `index.pkl`.*

5. **Create the `manifest.json`**  
   Follow the exact schema described [above](#the-manifestjson-standard). Every chunk must have an entry in `index_mapping` that links its `vector_id` to:
   - Full case citation  
   - Party names  
   - Date  
   - The *ratio decidendi* or principal legal proposition

6. **Bundle Everything**  
   Place the `raw_judgments/` folder (optional but strongly encouraged), the `vector_store/` folder, and `manifest.json` into a directory named after your `bundle_id`. Compress it as a `.zip` archive.

### Metadata Rules

- Every chunk **must** be coupled with a verifiable court citation (neutral citation where available).  
- If a chunk discusses a specific statutory provision (e.g., Section 41 of the CrPC), mention it in the `ratio_decidendi` or in an optional `statutes` field inside the manifest entry.  
- Bundle IDs must be lower‑case, hyphenated slugs (e.g., `sc-criminal-1990`).  
- The `embedding_model` must be `"nomic-embed-text"`; the `dimensions` must be `768`.

### Submitting a Bundle

1. Fork this repository.  
2. Add your bundle configuration file (just the `.zip` archive path and metadata) to the `/registry` folder.  
3. **Large binary files** (the actual `.zip`) must be handled via **Git LFS** or a secure cloud link. Our GitHub Actions will automate the fetch and validation.  
4. Open a Pull Request with a clear description of the bundle’s scope, source, and verification steps.  
5. The maintainers will review the manifest, check data integrity, and merge if everything complies.

---

##  Data Sanitization Policy

To uphold advocate‑client confidentiality and comply with the **Digital Personal Data Protection (DPDP) Act, 2023**, any contributor submitting litigation profiles or judgments from lower courts **must** run their content through a local Named Entity Recognition (NER) model **before** chunking.

**Sensitive details to redact or anonymise:**
- Phone numbers  
- Personal bank account numbers  
- Victim identities in cases protected under **Section 228A IPC** (or its BNSS equivalent)  
- Any information that could breach statutory non‑disclosure thresholds

This repository is intended for **research, training, and professional assistance** only. Raw personal data has no place here.

---

##  License

The data distribution framework (scripts, manifests, indexing logic) is released under the [MIT License](LICENSE).  

The underlying judicial materials and statutory texts are compiled from **public legal domains** and are used for non‑commercial research, training, and professional assistance within fair‑use / fair‑dealing educational parameters. No copyright is claimed over the original legal documents.

---

##  Acknowledgments

- The **Supreme Court of India** for making landmark judgments openly accessible through e‑SCR and other portals.  
- The **Ollama** project for providing a consistent local embedding pipeline.  
- All contributors who dedicate time to curating and sanitising these bundles for the public good.

---

*For questions, open an issue*  
*Happy offline lawyering! ⚖️*
```
