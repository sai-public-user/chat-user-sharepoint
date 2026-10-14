## 1. Data Ingestion & Crawling Node (Indexer)
These tools pull data from SharePoint (or other sources) and feed it into your pipeline.
 ### * LlamaIndex (LlamaHub Connectors)
   * Cost: Free (Open Source).
   * Usability: Extremely high for developers. Provides a native Python/TypeScript Microsoft SharePoint reader. Requires you to write the NestJS/Python orchestration script to run the sync jobs.
 ### * Airbyte (Cloud or OSS)
   * Cost: Free for self-hosted OSS; Cloud uses volume-based credits (~$2.50 per credit).
   * Usability: High for generic data movement. Offers a massive library of connectors. Requires managing a separate infrastructure tool alongside your Next.js app.
 ### * LangChain Document Loaders
   * Cost: Free (Open Source).
   * Usability: High for custom pipelines. The SharePointLoader natively handles Entra ID auth. Best used if you are already using LangChain for your LLM orchestration in NestJS.
 ### * Dify.ai
   * Cost: Free (OSS Docker) or SaaS ($59+/month).
   * Usability: High for low-code teams. A visual RAG platform that handles ingestion, crawling, and workflow building without writing complex API integrations.
 ### * Fivetran
   * Cost: High enterprise pricing (Monthly Active Rows model, typically hundreds to thousands per month).
   * Usability: Very high. Fully managed, zero-maintenance data pipelines. Overkill if you only need SharePoint, but excellent for enterprise-wide data consolidation.
 ### * RAGFlow
   * Cost: Free (OSS).
   * Usability: Medium. Focuses heavily on deep document understanding. Great if your SharePoint contains highly complex, visually dense PDFs.
 ### * Apache NiFi
   * Cost: Free (Open Source).
   * Usability: Low for solo developers, high for enterprise data teams. Visual drag-and-drop data routing, but requires managing heavy Java-based infrastructure.
 ### * Microsoft Power Automate
   * Cost: ~$15/user/month (Premium) or Power Automate Process licensing.
   * Usability: High for Microsoft ecosystems. Can trigger a webhook to your NestJS app whenever a file is added to SharePoint, eliminating the need for polling.
## 2. Text Cracking & Chunking Node (Skillset)
These tools crack open PDFs, Word docs, and Visio diagrams, extracting the text and tables cleanly for RAG.
 ### * Unstructured.io
   * Cost: Free (OSS local) or Serverless API ($0.015 per page processed).
   * Usability: High. The industry standard for RAG chunking. It intelligently splits text based on document structure (headers, paragraphs, tables) rather than arbitrary token counts.
 ### * LlamaParse (by LlamaIndex)
   * Cost: ~$0.003 per page (up to 1,000 free pages per day).
   * Usability: High. Exceptionally good at parsing complex tables and outputting clean Markdown, which drastically improves Claude's ability to read architecture matrices.
 ### * Docling (by IBM)
   * Cost: Free (Open Source).
   * Usability: High for Python developers. Rapidly growing OSS tool that converts PDFs to Markdown with high accuracy. Requires a Python microservice alongside your NestJS backend.
 ### * Mistral OCR
   * Cost: API pricing varies (often bundled with Mistral multimodal endpoints).
   * Usability: High for complex visual documents. Uses visual language models to literally "read" the document, perfect for architecture diagrams with embedded text.
 ### * Reducto
   * Cost: SaaS API (Volume-based pricing).
   * Usability: High. Specialized API designed specifically to strip out headers, footers, and noise from PDFs before passing them to an embedding model.
 ### * AWS Textract
   * Cost: ~$1.50 per 1,000 pages for basic text; higher for tables/forms.
   * Usability: Medium. Very robust AWS cloud-native OCR, but less optimized for LLM Markdown formatting compared to LlamaParse or Unstructured.
 ### * Marker (Open Source)
   * Cost: Free (Compute only).
   * Usability: Medium. Highly accurate OSS PDF-to-Markdown converter using deep learning. Requires GPU acceleration to run quickly, meaning high hosting costs.
 ### * Apache Tika
   * Cost: Free (Open Source).
   * Usability: Medium. The legacy standard for extracting text from hundreds of file types (PPTX, DOCX, PDF). Very fast, but terrible at maintaining table structures.
## 3. Vector Embedding Node (EmbedModel)
These models translate your chunked text into mathematical vectors for semantic search.
 ### * Voyage AI (Voyage 4 Large / Voyage Code)
   * Cost: $0.12 per 1M tokens (first 200M free).
   * Usability: High. Currently dominates embedding benchmarks (MTEB). The voyage-code-4 variant is exceptional for software engineering and enterprise architecture documents.
 ### * Cohere Embed (v3 / v4)
   * Cost: ~$0.10 per 1M tokens.
   * Usability: High. World-class multilingual support and native integration with many vector databases.
 ### * Qwen3-Embedding-8B
   * Cost: Free (Apache 2.0 Open Weights); $0.01/1M tokens via API.
   * Usability: High. Massive 32,768 token context window and beats many closed models on benchmarks. Requires local GPU hosting or cheap API access.
 ### * Google Gemini Embedding 2
   * Cost: $0.20 per 1M text tokens.
   * Usability: High. A true multimodal embedding space, meaning you can embed text and raw images (like architecture diagrams) into the exact same vector space.
 ### * Jina Embeddings v5
   * Cost: Free weights (CC BY-NC 4.0) or API access.
   * Usability: High. Uses "Matryoshka" representation, allowing you to truncate the vector size (saving database storage costs) without losing much accuracy.
 ### * Nomic Embed Text v2
   * Cost: Free (Apache 2.0).
   * Usability: High for efficiency. Very small, fast model that runs easily on CPU/Edge devices while maintaining strong benchmark performance.
 ### * Microsoft Harrier-oss-v1
   * Cost: Free (MIT License).
   * Usability: Medium. Requires self-hosting, but offers a 32,768 context window and ensures data never leaves your internal network.
 ### * Mistral Codestral Embed
   * Cost: $0.15 per 1M tokens.
   * Usability: Medium. Specifically tuned for code and technical retrieval.
## 4. Vector & Metadata Storage Node (VectorIndex)
These databases store your embeddings and metadata, allowing for rapid similarity search and security filtering.
 ### * Pinecone
   * Cost: Serverless tier charges ~$0.002 per GB/hour + read/write operations (often <$10/month for small apps).
   * Usability: Extremely high. Fully managed SaaS, heavily optimized developer experience, and excellent metadata filtering. Easiest API to integrate with NestJS.
 ### * Qdrant
   * Cost: Free (OSS) or Managed Cloud (starting ~$25/month).
   * Usability: High. Built in Rust for extreme speed and memory efficiency. Exceptional payload (metadata) filtering for your strict row-level security requirements.
 ### * PostgreSQL + pgvector
   * Cost: $15–$50/month depending on the database host (e.g., Supabase, RDS).
   * Usability: High for unified architectures. Allows you to keep your traditional relational data (users, logs) and vectors in the exact same database. Requires tuning the HNSW indexes manually.
 ### * Milvus / Zilliz Cloud
   * Cost: Free (OSS) or Zilliz Cloud Serverless.
   * Usability: High for massive scale. Designed to handle billions of vectors. Overkill for a few thousand SharePoint documents, but highly robust.
 ### * Weaviate
   * Cost: Free (OSS) or Managed Cloud (Serverless available).
   * Usability: High. An "AI-native" database that can actually handle the vectorization step internally, abstracting the embedding model API calls away from your NestJS code.
 ### * Chroma
   * Cost: Free (Open Source).
   * Usability: Very high for local prototyping. Runs easily in-memory or on disk with Python/Node.js, but lacks the enterprise RBAC and distributed clustering of Qdrant or Pinecone.
 ### * Elasticsearch
   * Cost: High (Managed cloud deployments often start at $90+/month).
   * Usability: Medium. The gold standard for traditional keyword search, now retrofitted with vector capabilities. Great if your enterprise already pays for an Elastic cluster.
 ### * OpenSearch
   * Cost: Dependent on AWS/Managed hosting (starts ~$50/month).
   * Usability: Medium. An AWS-backed fork of Elasticsearch. Offers excellent out-of-the-box hybrid search (combining keyword and vector scores) but carries configuration overhead.
