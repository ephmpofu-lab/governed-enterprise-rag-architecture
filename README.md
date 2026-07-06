# Governed RAG Knowledge System

A modular Retrieval-Augmented Generation (RAG) system for turning organisational documents into structured, searchable knowledge.

I built the system as four connected n8n workflows rather than one large workflow. Each stage has a clear responsibility: acquire documents, process their content, index the knowledge, and retrieve relevant information for grounded responses.

## Architecture

The system follows four stages:

**01. Knowledge Acquisition**  
Detects new documents, registers them, and starts the processing pipeline.

**02. Knowledge Intelligence**  
Extracts document text, classifies content, generates metadata, analyses document structure, and produces a summary.

**03. Vector Indexing**  
Chunks processed content, creates embeddings, and stores the resulting knowledge representations for semantic retrieval.

**04. RAG Retrieval**  
Receives a user question, retrieves relevant context from the vector store, and provides that context to the AI agent for response generation.

```text
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
