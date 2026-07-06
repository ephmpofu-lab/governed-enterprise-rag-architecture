# Governed RAG Knowledge System

A modular Retrieval-Augmented Generation (RAG) system for turning organisational documents into structured, searchable knowledge.

I built the system as four connected n8n workflows rather than one large workflow. Each stage has a clear responsibility: acquire documents, process their content, index the knowledge, and retrieve relevant information for grounded responses.

## Architecture

The system follows four stages:

### 01. Knowledge Acquisition

Detects new documents, registers them, and starts the processing pipeline.

### 02. Knowledge Intelligence

Extracts document text, classifies content, generates metadata, analyses document structure, and produces a summary.

### 03. Vector Indexing

Chunks processed content, creates embeddings, and stores the resulting knowledge representations for semantic retrieval.

### 04. RAG Retrieval

Receives a user question, retrieves relevant context from the vector store, and provides that context to the AI agent for response generation.

### System Flow

Documents  
↓  
Knowledge Acquisition  
↓  
Knowledge Intelligence  
↓  
Vector Indexing  
↓  
Vector Store  
↓  
RAG Retrieval  
↓  
Grounded Response

## Why I Built It This Way

The architecture is based on separation of concerns.

Each workflow can be tested, changed, and monitored independently. A failure in document processing can be traced to that stage without treating the whole RAG pipeline as one black box.

The structure also allows individual components, such as the language model, embedding model, chunking strategy, or vector store, to be changed without redesigning the complete system.

## Governance Principles

Governance was considered as part of the architecture, not as an additional layer added afterwards.

The design supports:

- **Traceability** — documents move through identifiable processing stages.
- **Auditability** — workflow executions and processing states can be inspected.
- **Transparency** — document processing, indexing, retrieval, and generation are separated.
- **Grounded generation** — responses use retrieved organisational knowledge as context.
- **Human oversight** — review and approval points can be introduced at defined stages of the pipeline.

These principles provide a foundation for stronger controls such as source citation, model and prompt versioning, access control, evaluation, and approval gates.

## Technology Stack

| Component | Technology |
|---|---|
| Workflow orchestration | n8n |
| Language processing | OpenAI |
| Embeddings | OpenAI Embeddings |
| Vector storage and retrieval | Supabase |
| Document source | Google Drive |
| Custom processing logic | JavaScript |
| Version control | GitHub |

## Repository Structure

    workflows/
    ├── WF01_RAG_Knowledge_Acquisition.json
    ├── WF02_RAG_Knowledge_Intelligence.json
    ├── WF03_RAG_Vector_Indexing.json
    └── WF04_RAG_Retrieval.json

## Current Development Direction

The next stage is to strengthen the system around:

- retrieval and groundedness evaluation;
- source citation;
- human review gates;
- model and prompt version tracking;
- monitoring and audit logs;
- controlled deployment across development, test, and production environments.

## Project Note

This project focuses on the architecture of a governed RAG system. It demonstrates how modular AI workflows can support traceability, auditability, transparency, human oversight, and grounded retrieval.

It does not claim that the architecture alone provides regulatory compliance.
