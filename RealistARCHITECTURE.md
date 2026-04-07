# Realist Intelligence: Technical Architecture & System Design

This document outlines the high-level architecture and design patterns of the Realist Intelligence ecosystem, including the **Catalyst OS** orchestration layer and the **Federated RAG** pipeline.

## 1. System Overview
Realist Intelligence is built as a distributed microservice architecture, separating the core reasoning engines from the data ingestion and management layers.

- **Primary Stack:** .NET 8 (Web API) & Python 3.10+ (AI Engine)
- **Deployment:** Containerized via Render (Current), migrating to Azure (Target)
- **Data Persistence:** MongoDB (Global Knowledge) & Redis (Real-time Session Context)

## 2. Core Components

### A. The Controller Stage (.NET 8)
Acting as the "Brain" of the request lifecycle, the .NET 8 API handles:
- **Identity & Security:** JWT Authentication with Argon2id password hashing.
- **Request Routing:** Intent classification to determine if a query requires "Fast" or "Deep" reasoning.
- **Resilience:** Integrated **Polly** policies for Circuit Breaking and Retries when communicating with LLM providers.

### B. The AI Engine (Python)
The Python service manages high-performance asynchronous tasks:
- **Multi-Model Pipeline:** Routes complex logic to specialized models (e.g., GPT-4o, DeepSeek) while handling low-latency tasks via GPT-3.5/4-Nano.
- **Auto-Evolve Loop:** An autonomous critique-and-refine cycle that verifies AI outputs against a confidence threshold before delivery.

### C. Federated RAG Pipeline
Unlike standard RAG, our **Federated** approach retrieves context from multiple isolated "Graphs":
- **Global Knowledge:** Persistent enterprise-wide data stored in MongoDB.
- **Session Graph:** Real-time, transient context stored in Redis to maintain "short-term memory" during a collaboration session.
- **Vector Search:** SIMD-optimized cosine similarity for sub-millisecond retrieval of relevant context fragments.

## 3. Data Flow & Logic
1. **Ingest:** User query is sanitized and intent-mapped.
2. **Contextualize:** The system fetches relevant embeddings from both Global and Session stores.
3. **Reason:** The combined prompt (Query + Context) is processed by the AI Pipeline.
4. **Synthesize:** Results are validated by the "Auto-Evolve" agent and streamed back to the frontend.

## 4. API Specification
Our system follows a strict **OpenAPI 3.0** standard. 
- **Live Documentation:** [Swagger UI Link]
- **API Definition:** `openapi.json` is included in this repository under `/api-spec`.

## 5. Security & Privacy(IN DEVELOPMENT)
- **One-Way Mirror Framework:** Ensures user data is used for context retrieval without being permanently stored in the LLM's training set.
- **Rate Limiting:** IP-based and User-based throttling to prevent resource exhaustion.