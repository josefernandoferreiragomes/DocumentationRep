# Introduction to AI-Powered Information Extraction — Overview

## Core Concept

AI-powered information extraction converts unstructured or semi-structured content into structured, machine-readable data.

Typical input sources:
- scanned documents
- PDFs
- forms
- invoices
- receipts
- images
- handwritten text

Typical outputs:
- extracted text
- fields and values
- tables
- metadata
- searchable knowledge

Microsoft positions information extraction as a combination of:
- Computer Vision
- OCR (Optical Character Recognition)
- NLP
- Document Intelligence
- Knowledge Mining

The process usually follows this pipeline:

| Stage | Purpose |
|---|---|
| Data ingestion | Receive documents/images |
| OCR | Convert images to text |
| Layout analysis | Detect structure |
| Field extraction | Identify key/value pairs |
| Validation | Verify extracted data |
| Storage/Search | Store structured output |

---

## Information Extraction Categories

### 1. OCR-Based Extraction
Extracts text from:
- scanned documents
- photographs
- screenshots
- handwritten content

Focus:
- character recognition
- text localization
- language detection

---

### 2. Form Extraction
Extracts structured fields from:
- invoices
- receipts
- tax forms
- IDs
- contracts

Focus:
- key-value mapping
- table extraction
- semantic field recognition

---

### 3. Knowledge Mining
Transforms extracted data into searchable knowledge repositories using:
- Azure AI Search
- indexing
- enrichment pipelines

Enables:
- enterprise search
- semantic retrieval
- document intelligence systems

---

## Azure Services Relevant to AI-900

| Service | Purpose |
|---|---|
| Azure AI Vision | OCR and image analysis |
| Azure AI Document Intelligence | Form and document extraction |
| Azure AI Search | Knowledge mining and indexing |
| Azure AI Foundry | AI orchestration and tooling |

---

## Key AI-900 Concepts

### Structured vs Unstructured Data

| Type | Example |
|---|---|
| Structured | SQL tables |
| Semi-structured | JSON, XML |
| Unstructured | PDFs, images, scanned forms |

Information extraction converts unstructured content into structured datasets.

---

### OCR vs Document Intelligence

| OCR | Document Intelligence |
|---|---|
| Reads text | Understands document structure |
| Character-level extraction | Semantic field extraction |
| Generic | Domain-aware |
| Limited context | Contextual relationships |

Common misconception:
- OCR alone does **not** understand invoices, totals, signatures, or tables.
- Document Intelligence builds on OCR to interpret meaning and structure.

---

## Architecture Implications

Modern enterprise workflows often use:
1. OCR
2. Layout analysis
3. Field extraction
4. Validation
5. Storage/indexing
6. Search/chat interfaces

Typical use cases:
- insurance claims
- accounts payable automation
- healthcare records
- compliance processing
- contract analysis

---

## Exam-Relevant Terminology

| Term | Meaning |
|---|---|
| OCR | Optical Character Recognition |
| Layout analysis | Detecting document structure |
| Key-value pairs | Linked labels and values |
| Knowledge mining | Extracting searchable insights |
| Document Intelligence | Azure service for document processing |
| Enrichment pipeline | AI processing during indexing |

---

## Common Misconceptions

| Misconception | Reality |
|---|---|
| OCR understands documents | OCR only extracts text |
| All PDFs are text-readable | Many PDFs are image-based |
| Form extraction is rule-based only | Modern systems use ML models |
| Tables are easy to extract | Table structures are complex in scanned docs |

---

## Key Takeaways

- Information extraction converts unstructured content into structured data.
- OCR is foundational but insufficient for semantic understanding.
- Azure AI Document Intelligence extends OCR with structure and field extraction.
- Knowledge mining enables enterprise-scale search and retrieval.
- AI-900 expects understanding of service roles and extraction workflows.

---

## Official References

- https://learn.microsoft.com/en-us/training/modules/introduction-information-extraction/
- https://learn.microsoft.com/en-us/azure/ai-services/document-intelligence/model-overview
- https://learn.microsoft.com/en-us/azure/ai-services/computer-vision/overview-ocr

---
---

# Vision-Based Information Extraction

## OCR (Optical Character Recognition)

OCR converts text inside images or scanned documents into machine-readable text.

Azure OCR capabilities support:
- printed text
- handwritten text
- multilingual content
- PDFs
- images
- Office documents

Azure AI uses deep learning models for OCR.

---

## OCR Workflow

| Step | Description |
|---|---|
| Image preprocessing | Improve readability |
| Text localization | Detect text regions |
| Character recognition | Identify characters |
| Post-processing | Correct errors/context |

---

## Azure OCR Capabilities

Azure AI Vision Read API and Document Intelligence Read model support:
- handwriting recognition
- paragraph detection
- line segmentation
- word coordinates
- language detection
- layout extraction

The Document Intelligence Read model operates at higher resolution than standard Vision OCR.

---

## OCR vs General Computer Vision

| OCR | General Vision |
|---|---|
| Reads text | Detects objects/scenes |
| Text-centric | Image-centric |
| Used in documents | Used in photos/videos |

Example:
- OCR → reads invoice number
- Vision → identifies a car in an image

---

## Layout Analysis

Beyond text extraction, systems analyze:
- paragraphs
- headers
- tables
- checkboxes
- selection marks
- reading order

Layout analysis is critical for:
- invoices
- forms
- contracts
- receipts

---

## Challenges in Vision Extraction

### Image Quality
Poor scans reduce accuracy:
- blur
- skew
- shadows
- low resolution

---

### Handwriting
Handwritten text is harder because:
- inconsistent spacing
- variable styles
- overlapping characters

---

### Complex Layouts
Documents may contain:
- nested tables
- stamps
- signatures
- rotated text
- mixed languages

---

## AI-900 Relevant Azure Services

| Service | Main Capability |
|---|---|
| Azure AI Vision | OCR and image analysis |
| Azure AI Document Intelligence | Document OCR + structure |
| Azure AI Search | Index extracted content |

---

## Real-World Scenarios

| Scenario | Example |
|---|---|
| Invoice automation | Extract totals/vendors |
| Healthcare | Read medical forms |
| Banking | Process checks/IDs |
| Logistics | Extract shipping labels |
| Compliance | Search scanned archives |

---

## Architecture Considerations

### Synchronous vs Asynchronous
- Small image OCR may be synchronous.
- Large document pipelines are usually asynchronous.

### Prebuilt vs Custom
| Type | Use Case |
|---|---|
| Prebuilt | Standard forms |
| Custom | Organization-specific layouts |

---

## Common Misconceptions

| Misconception | Reality |
|---|---|
| OCR accuracy is always high | Input quality heavily matters |
| OCR understands semantics | It mainly extracts text |
| PDFs always contain readable text | Many PDFs are scanned images |

---

## Key Takeaways

- OCR extracts text from visual sources.
- Azure AI combines OCR with layout understanding.
- Layout analysis is essential for complex document extraction.
- Document Intelligence extends OCR with semantic structure.
- Input quality strongly impacts extraction accuracy.

---

## Official References

- https://learn.microsoft.com/en-us/training/modules/introduction-information-extraction/3-vision-extraction
- https://learn.microsoft.com/en-us/azure/ai-services/document-intelligence/prebuilt/read
- https://learn.microsoft.com/en-us/azure/ai-services/computer-vision/overview-ocr

---
---

# Form Extraction and Document Intelligence

## Core Concept

Form extraction identifies:
- fields
- labels
- values
- tables
- document structure

from semi-structured documents.

Unlike raw OCR, form extraction attempts to understand:
- relationships
- semantic meaning
- document intent

---

## Azure AI Document Intelligence

Azure AI Document Intelligence enables:
- invoice extraction
- receipt processing
- ID analysis
- contract extraction
- custom document models

Previously known as:
- Azure Form Recognizer

---

## Extraction Pipeline

| Stage | Function |
|---|---|
| OCR | Extract raw text |
| Layout analysis | Detect structure |
| Key-value mapping | Associate labels and values |
| Semantic interpretation | Understand fields |
| Output generation | Produce structured JSON |

---

## Prebuilt Models

Microsoft provides pretrained models for common documents:

| Model | Example |
|---|---|
| Invoice | Vendor, totals, taxes |
| Receipt | Merchant, items, totals |
| ID Document | Passport/license fields |
| Business Card | Contact extraction |

Prebuilt models reduce training effort and accelerate deployment.

---

## Custom Models

Custom extraction models are trained using organization-specific forms.

Used when:
- layouts are proprietary
- fields are domain-specific
- documents vary from standard templates

Training requires:
- labeled sample documents
- field annotations

---

## Key-Value Pair Extraction

Example:

| Key | Value |
|---|---|
| Invoice Number | INV-1045 |
| Total Amount | €1450 |
| Due Date | 2026-05-30 |

The system learns spatial relationships between labels and values.

---

## Table Extraction

Important capability for:
- invoices
- purchase orders
- reports

Challenges:
- merged cells
- inconsistent spacing
- scanned quality issues

---

## Confidence Scores

Extraction systems typically output confidence scores.

Purpose:
- validation
- human review workflows
- automation thresholds

Example:
- High confidence → automatic approval
- Low confidence → manual verification

---

## AI-900 Important Distinctions

| Capability | Meaning |
|---|---|
| OCR | Extract text |
| Layout | Understand positioning |
| Field extraction | Identify semantic fields |
| Classification | Identify document type |

---

## Real-World Enterprise Use Cases

| Industry | Use Case |
|---|---|
| Finance | Invoice automation |
| Healthcare | Patient intake forms |
| Insurance | Claims processing |
| Legal | Contract indexing |
| Government | Identity verification |

---

## Architecture Implications

### Human-in-the-Loop
Production systems often include:
- confidence validation
- exception handling
- reviewer interfaces

### Scalability
Document Intelligence supports:
- asynchronous processing
- large document batches
- cloud-native workflows

---

## Common Misconceptions

| Misconception | Reality |
|---|---|
| OCR is enough for invoices | Semantic extraction is required |
| Prebuilt models solve every use case | Custom models are often necessary |
| Table extraction is trivial | Tables are one of the hardest tasks |
| Extraction is always deterministic | Confidence-based validation is common |

---

## Key Takeaways

- Form extraction builds on OCR and layout analysis.
- Azure AI Document Intelligence extracts semantic document fields.
- Prebuilt models accelerate common business scenarios.
- Custom models support organization-specific forms.
- Confidence scoring is critical in production automation.

---

## Official References

- https://learn.microsoft.com/en-us/training/modules/introduction-information-extraction/4-form-extraction
- 
- https://azure.microsoft.com/products/ai-foundry/tools/document-intelligence
- https://azure.microsoft.com/products/ai-foundry/tools/content-understanding
- https://learn.microsoft.com/en-us/azure/ai-services/document-intelligence/model-overview
- https://learn.microsoft.com/en-us/azure/ai-services/document-intelligence/
