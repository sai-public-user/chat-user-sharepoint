
##Approach 1: Microsoft Graph API + Next.js + Claude (Zero-Copy)
 * Architecture: Your Next.js Server Actions intercept the user's query, execute a live search against corporate SharePoint using the Microsoft Graph REST API, retrieve the top file contents, and pass them to the Claude API.
 * Pros:
   * No databases to manage. Zero data duplication.
   * Microsoft Graph natively enforces Entra ID security (users can't search what they don't have access to).
 * Cons (Fails BRD):
   * Graph search is primarily keyword-based, not semantic.
   * Fatal Flaw: It is nearly impossible to dynamically filter by your strict 6-Vector metadata (e.g., boosting APPROVED and hiding SUPERSEDED versions). Claude will receive outdated working drafts and confidently hallucinate.
 * Best fit: A quick weekend prototype, but unsuited for a governed EA platform.
---
##Approach 2: Azure AI Search + Cosmos DB + Next.js + Claude (The Enterprise Standard)
 * Architecture: You use Azure Blob Storage for files, Cosmos DB for the 6-Vector metadata ledger, and Azure AI Search as the hybrid retrieval brain. Next.js Server Actions orchestrate the query: they fetch the Entra ID user claims, query Azure AI Search with hard metadata filters, and send the results to Claude (hosted securely in the Azure AI Foundry).
 * Pros:
   * Perfect BRD Alignment: Azure AI Search allows strict metadata filtering at the database level (Status eq 'APPROVED' AND Audience eq 'INTERNAL').
   * Bulletproof Security: You inject the user's Entra ID group directly into the Azure Search query, mathematically guaranteeing unauthorized data is dropped before Claude sees it.
   * Enterprise Compliance: Claude models are available directly inside the Azure ecosystem, meaning your data never leaves your secure Microsoft tenant.
 * Cons:
   * Requires managing three separate Azure data services (Search, Cosmos, Blob).
   * Next.js is primarily request-driven; building the background ingestion/sync pipeline from Blob to Azure AI Search will require setting up Vercel Cron Jobs or Azure Functions.
 * Best fit: The target production architecture for strict enterprise compliance.
---
##Approach 3: Postgres (pgvector) + Prisma + Next.js + Claude (The "Solo Dev All-in-One")
 * Architecture: You drop Cosmos DB and Azure AI Search entirely. Instead, you use a single PostgreSQL database equipped with the pgvector extension. You use the Prisma ORM inside Next.js to manage your metadata, relationships, and vector embeddings all in one place. Next.js handles the hybrid search logic and calls the Claude API.
 * Pros:
   * Developer Velocity: Unbeatable for a solo developer. You manage one database schema for both your metadata ledger and your vector search.
   * Relational data makes building the BRD's "Relationship Graph" (linking strategies to patterns) incredibly easy.
   * Very cheap to operate (can be hosted on Supabase, Vercel Postgres, or Azure Database for PostgreSQL).
 * Cons:
   * Postgres full-text search + vector search (Hybrid Search) requires manually writing complex SQL queries; it is not as "out-of-the-box" perfect as Azure AI Search's proprietary ranking algorithms.
   * You have to manually code the row-level security (RLS) to ensure EXTERNAL users don't see INTERNAL documents.
 * Best fit: The absolute best approach for a solo developer who wants to move fast and keep the architecture delightfully simple without sacrificing custom BRD governance.
---
##Approach 4: AWS Bedrock Knowledge Bases + Next.js + Claude (AWS Managed RAG)
 * Architecture: Next.js acts as the UI and API layer, but you offload all the heavy lifting to AWS. Documents are pushed to Amazon S3, which automatically syncs to Amazon Bedrock Knowledge Bases. Next.js calls a single Bedrock API, which handles the search and Claude synthesis natively.
 * Pros:
   * Fully managed pipeline: AWS handles the chunking, vectorization, and Claude integration out-of-the-box.
   * Claude is a first-class citizen on AWS Bedrock.
 * Cons:
   * Bedrock Knowledge Bases are somewhat of a "black box." Injecting your highly specific EA lifecycle rules (Latest Approved) and granular metadata filters into the Bedrock retrieval step requires clunky workarounds compared to Azure or Postgres.
 * Best fit: Teams strictly mandated to use AWS native services.
---
##Approach 5: Commercial Enterprise AI Search (e.g., Glean, Coveo) + Next.js UI
 * Architecture: Purchase an off-the-shelf Enterprise Search API. Connect it to your corporate SharePoint. Build your custom Next.js UI on top of their search API, and let their system handle Claude.
 * Pros:
   * Out-of-the-box enterprise security trimming and data connectors.
   * Lowest amount of backend logic to write.
 * Cons:
   * Loss of Control: You cannot easily build custom EA workflows (like the specific ARB dashboard or custom lifecycle approval flows defined in your BRD).
   * Massive licensing costs.
 * Best fit: Organizations looking to buy rather than build.
---
##The Verdict & Recommendation
As a solo developer building a Next.js full-stack app, you should choose between Approach 2 and Approach 3.
##Choose Approach 2 (Azure AI Search + Cosmos DB + Claude on Azure):
 * If your company has strict InfoSec policies that mandate using Microsoft Azure enterprise services.
 * If you don't want to write complex search ranking algorithms yourself (Azure AI Search's hybrid semantic ranker is world-class).
##Choose Approach 3 (Postgres + pgvector + Prisma + Claude API):
 * If you want maximum developer speed.
 * If managing three different Azure databases (Search, Cosmos, Blob) sounds like an operational nightmare for one person. With Postgres, your metadata, version history, and vectors all live in one highly relational, easily queryable place.
---
