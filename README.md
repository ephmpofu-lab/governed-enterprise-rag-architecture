# Governed Enterprise RAG Architecture

A modular Retrieval-Augmented Generation (RAG) system designed around a simple principle: enterprise AI should be useful without becoming opaque.

The project separates document acquisition, knowledge processing, vector indexing, and retrieval into independent workflows. This makes the system easier to inspect, test, change, and govern than a single end-to-end workflow where ingestion, transformation, retrieval, and generation are tightly coupled.

The current implementation uses n8n for workflow orchestration, OpenAI models for language processing and embeddings, Google Drive as a document source, and Supabase for operational data and vector retrieval.

---

## 1. The Problem

A basic RAG prototype is relatively easy to build.

An enterprise knowledge system is harder.

Documents change. Different document types require different processing. Failed ingestion runs must be visible. Retrieved context must be traceable to source material. Model outputs should remain grounded in approved organizational knowledge, and changes to individual processing stages should not require rebuilding the entire system.

This project explores that problem through a modular RAG architecture with explicit workflow boundaries.

The system is designed to support:

- controlled document ingestion;
- document classification and metadata generation;
- structured knowledge extraction;
- document summarisation;
- chunking and embedding;
- vector-based retrieval;
- grounded AI responses;
- workflow-level execution history;
- modular testing and maintenance;
- human review points where organizational policy requires them.

---

## 2. Architecture

The system is divided into four workflows:

| Workflow | Responsibility | Primary Output |
|---|---|---|
| WF01 — Knowledge Acquisition | Detect and register new source documents | Registered document record |
| WF02 — Knowledge Intelligence | Extract, classify, analyse, and structure document knowledge | Enriched document representation |
| WF03 — Vector Indexing | Chunk, embed, and index processed content | Searchable vector knowledge base |
| WF04 — Retrieval | Retrieve relevant context and generate grounded responses | Context-grounded answer |

The workflow sequence is:

Source Document  
→ Knowledge Acquisition  
→ Knowledge Intelligence  
→ Vector Indexing  
→ Retrieval  
→ Grounded Response

Each workflow has one primary responsibility and passes a defined output to the next stage.

---

## 3. Workflow 01 — Knowledge Acquisition

**Purpose:** establish a controlled entry point for organizational knowledge.

The workflow begins when a new document is detected in the configured source repository. The document is registered in the operational data layer before downstream processing begins.

### Process

1. Detect a newly created source document.
2. Create the initial document record.
3. assign the document to the knowledge-processing workflow.

### Why this workflow is separate

Acquisition is intentionally separated from document intelligence.

This prevents source-system events from being tightly coupled to model processing and creates a clear record of what entered the knowledge pipeline and when.

The separation also makes it possible to change the document source without redesigning the intelligence and retrieval layers.

---

## 4. Workflow 02 — Knowledge Intelligence

**Purpose:** transform a raw document into a structured and interpretable knowledge object before vector indexing.

This is the main document-processing layer of the architecture.

### Processing sequence

1. Download the source file.
2. Extract text from the document.
3. validate the document processing path.
4. Classify the document.
5. Generate structured metadata.
6. Analyse document structure and content.
7. Generate a document summary.
8. Consolidate the intelligence outputs.
9. Pass the processed document to the indexing workflow.

The workflow uses structured output parsers to constrain model responses to expected schemas.

This stage is deliberately placed before embedding. The aim is not simply to place raw text into a vector database, but to preserve contextual information that can support retrieval, filtering, provenance, and later governance controls.

---

## 5. Workflow 03 — Vector Indexing

**Purpose:** convert processed organizational knowledge into retrievable semantic representations.

### Process

1. Receive the processed document.
2. Retrieve the corresponding document record.
3. Divide content into retrieval units.
4. Generate vector embeddings.
5. Store embeddings and document content in the vector store.
6. Create indexing records.
7. update document processing status.

The indexing workflow is isolated from both document intelligence and user-facing retrieval.

This separation allows chunking strategies, embedding models, indexing logic, and vector-store configurations to evolve without changing the document acquisition process or the retrieval interface.

---

## 6. Workflow 04 — Grounded Retrieval

**Purpose:** answer user questions using retrieved organizational knowledge rather than relying only on the model's parametric memory.

### Retrieval sequence

1. Receive a user question.
2. Convert the query into an embedding representation.
3. Search the vector knowledge base for semantically relevant content.
4. Provide retrieved context to the AI agent.
5. Generate a response based on the retrieved evidence.

The vector store is exposed to the agent as a retrieval tool. This keeps knowledge retrieval distinct from the language model itself.

The design reduces dependence on unsupported model recall and provides a foundation for source attribution and evidence-based responses.

---

## 7. Governance by Architecture

Governance in this project is treated as a system property rather than a final compliance check.

### Traceability

Documents move through identifiable processing stages:

Acquisition  
→ Intelligence  
→ Indexing  
→ Retrieval

The workflow separation makes it possible to determine where a document entered the system, which processing stage handled it, and where failures occurred.

### Auditability

Workflow executions provide an operational history of pipeline activity.

The architecture supports recording:

- document ingestion events;
- processing status;
- workflow execution outcomes;
- indexing activity;
- processing failures;
- model-processing stages.

A production implementation should extend this with centralized audit logs, model and prompt version records, retrieval evidence, user-action records, and retention policies appropriate to the deployment context.

### Transparency

The architecture separates source knowledge, document transformation, vector representation, retrieval, and answer generation.

This distinction matters because a generated answer should not be treated as equivalent to its source evidence.

The system is designed so that provenance and source attribution can be added at the retrieval and response layers.

### Human Oversight

The architecture allows human review gates to be introduced at defined control points, for example:

- approval of sensitive documents before indexing;
- review of low-confidence classifications;
- validation of high-impact outputs;
- approval before knowledge-base publication;
- escalation of retrieval failures or unsupported answers.

Human oversight is therefore implemented according to use case and risk, rather than assumed to be satisfied merely because a person can access the workflow.

### Grounded Generation

The retrieval layer supplies relevant organizational context to the model at query time.

This provides a practical control against unsupported generation, although RAG alone does not guarantee factual correctness. Retrieval quality, source quality, chunking strategy, prompt design, and evaluation remain part of system assurance.

---

## 8. Design Principles

The architecture follows three structural principles.

### Separation of Concerns

Each workflow owns a distinct business and technical responsibility.

Acquisition does not perform retrieval. Retrieval does not modify source documents. Indexing does not control source ingestion.

### Single Source of Truth

Document identity, processing state, and knowledge representations should be managed through explicit system records rather than duplicated independently across workflows.

### Fail Visibility Over Silent Failure

A failed document-processing stage should be observable.

The system architecture favours explicit status transitions, execution records, and recoverable workflow boundaries over silent continuation after processing failure.

---

## 9. Model Strategy

The current system uses foundation models for document intelligence, embedding generation, and grounded response generation.

The present architecture uses **retrieval augmentation rather than model fine-tuning**.

This is an intentional distinction.

RAG changes the context available to the model at inference time. Fine-tuning changes model behaviour through additional training.

The modular architecture allows future evaluation of:

- alternative embedding models;
- model replacement;
- prompt versioning;
- reranking;
- hybrid retrieval;
- domain-specific evaluation datasets;
- model fine-tuning where evidence demonstrates that retrieval and prompting alone are insufficient.

Model changes should be evaluated before promotion into the production workflow.

---

## 10. Development and Deployment Approach

The project is structured to support a DevOps-oriented delivery lifecycle.

The four workflows can be exported as version-controlled JSON definitions. Changes can therefore be reviewed, tested, and promoted through controlled environments rather than edited directly in a production workflow.

A production delivery path would follow:

Development  
→ Workflow Testing  
→ Integration Testing  
→ Evaluation  
→ Controlled Release  
→ Monitoring  
→ Improvement

Recommended environment separation:

- Development — workflow construction and experimentation;
- Test — integration testing with controlled documents and queries;
- Production — approved workflows, credentials, data connections, and monitoring.

Secrets and credentials must remain outside the repository and be managed through environment-specific credential stores.

---

## 11. Evaluation Strategy

A RAG system should not be evaluated only on whether it returns an answer.

Evaluation should cover the full pipeline.

### Ingestion

- Was the correct document detected?
- Was the complete document extracted?
- Was document identity preserved?

### Document Intelligence

- Was classification correct?
- Was metadata extracted accurately?
- Was structural analysis consistent?
- Was the summary faithful to the source?

### Retrieval

- Were relevant chunks retrieved?
- Were important source passages missed?
- Was irrelevant context introduced?

### Generation

- Is the answer supported by retrieved evidence?
- Does the answer introduce unsupported claims?
- Is the response useful for the user query?
- Can the answer be traced back to its supporting sources?

These evaluations provide the basis for controlled improvement of prompts, chunking strategy, embedding configuration, retrieval logic, and model selection.

---

## 12. Technology Stack

| Layer | Technology |
|---|---|
| Workflow Orchestration | n8n |
| Language Models | OpenAI |
| Embeddings | OpenAI Embeddings |
| Vector Retrieval | Supabase Vector Store |
| Operational Data | Supabase |
| Document Source | Google Drive |
| Workflow Logic | JavaScript and n8n nodes |
| Version Control | GitHub |

---

## 13. Repository Structure

```text
governed-enterprise-rag-architecture/
│
├── README.md
│
├── workflows/
│   ├── WF01_RAG_Knowledge_Acquisition.json
│   ├── WF02_RAG_Knowledge_Intelligence.json
│   ├── WF03_RAG_Vector_Indexing.json
│   └── WF04_RAG_Retrieval.json
│
├── architecture/
│   └── rag-system-architecture.png
│
├── screenshots/
│   ├── WF01-acquisition.png
│   ├── WF02-intelligence.png
│   ├── WF03-indexing.png
│   └── WF04-retrieval.png
│
└── docs/
    ├── architecture-decisions.md
    ├── governance-controls.md
    └── evaluation-strategy.md
