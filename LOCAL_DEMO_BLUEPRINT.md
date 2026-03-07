# Local-Only Document Scanner Demo Blueprint

This blueprint defines a **single-machine, privacy-preserving** scanner demo suitable for fact-find workflows.

## Goal
Build a credible v1 demo that captures documents, runs OCR/extraction locally, and stores artifacts + metadata for review/audit—without cloud services.

## Scope and non-goals

### In scope (v1)
- Full-screen scanner experience in browser (mobile-friendly).
- Local API for ingest + OCR orchestration.
- Local OCR and extraction models only.
- Local image + metadata persistence.
- Manual review/confirm step before data is used.

### Out of scope (v1)
- Third-party OCR APIs.
- External data transfer.
- Phone-to-desktop sync.
- Liveness/NFC/fraud detection.
- Complex document classification pipelines.

## Recommended stack

### Frontend
- Next.js (or React SPA for speed).
- Tailwind CSS for rapid UI polish.
- Framer Motion (optional) for “document detected” and transition states.

### Backend
- FastAPI (localhost service).
- Pydantic schemas for request/response validation.
- SQLAlchemy (optional) for model organization.

### OCR / extraction pipeline
- OpenCV for preprocessing:
  - blur checks
  - edge detection
  - perspective correction
  - crop + threshold cleanup
- PaddleOCR for general OCR.
- PassportEye or FastMRZ for passport/MRZ parsing.
- Rule/regex extraction for structured fields (name, DOB, address, ID number).

### Storage
- Local folders for image artifacts.
- SQLite for metadata and structured extracted fields.

## Architecture

```text
Phone or browser camera
        ↓
React / Next.js scanner UI
        ↓
Local FastAPI service (localhost)
        ↓
OpenCV preprocessing
        ↓
PaddleOCR / MRZ parser
        ↓
Local storage folders + SQLite
```

## Local storage layout

```text
demo-scanner/
  frontend/
  backend/
  data/
    originals/
    processed/
    exports/
  app.db
```

## Minimal data model

### `documents`
- `id`
- `scan_session_id`
- `doc_type` (passport | proof_of_address | generic_id)
- `original_image_path`
- `processed_image_path`
- `ocr_text`
- `created_at`
- `user_confirmed` (bool)

### `document_fields`
- `id`
- `document_id`
- `field_name`
- `field_value`
- `confidence` (optional)
- `source` (mrz | ocr | regex)

### `scan_sessions`
- `id`
- `case_ref` (optional)
- `started_at`
- `completed_at`
- `status`

## MVP document types

### A) Passport
- Capture image.
- Detect/crop passport region.
- Parse MRZ.
- Autofill:
  - full name
  - DOB
  - nationality
  - document number
  - expiry date

### B) Proof of address
- Capture full page.
- OCR full text.
- Extract likely fields:
  - name
  - address
  - document date
  - issuer

### C) Generic ID / licence
- Capture image.
- OCR visible text.
- Rule-based extraction of likely identity fields.

## UX priorities (what makes demo feel premium)
- Full-screen camera with overlay frame.
- Auto-capture cues (“hold steady”, “document detected”).
- Instant post-capture preview (original + cropped).
- Clean extracted-fields panel with confidence indicators.
- Explicit “Use data” confirmation action.

## Local demo flows

### Flow 1: ID scan
1. Open fact-find form.
2. Tap **Scan ID**.
3. Camera capture + auto-crop.
4. OCR/extraction runs locally.
5. User reviews extracted fields.
6. On confirm, fields populate form.
7. Images + metadata saved locally.

### Flow 2: Supporting document
1. Tap **Scan Document**.
2. Capture supporting document.
3. OCR/extraction runs locally.
4. User reviews full text + fields.
5. Confirm and save to case record.

## Suggested implementation phases

### Phase 1 (1–2 days)
- Scanner UI shell + capture/review states.
- FastAPI endpoints for image upload + stub response.
- Local folder + SQLite persistence.

### Phase 2 (2–3 days)
- OpenCV preprocessing integration.
- PaddleOCR inference wiring.
- Generic field extraction rules.

### Phase 3 (1–2 days)
- Passport MRZ parser integration.
- Structured field confidence + review UI improvements.
- Export endpoint (JSON/CSV) for demo artifacts.

## Demo positioning statement
Use a clear, credible v1 claim:

> “Locally run document capture and extraction demo for fact-find workflows. Stores original image and extracted fields for audit trail. Supports ID capture and general document scanning.”
