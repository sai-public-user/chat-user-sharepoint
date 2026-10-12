# Next.js Full-Stack Execution Plan (Solo Developer)

## Why This is the Right Move
By dropping NestJS and using Next.js (App Router) for both the frontend and backend, you gain massive speed advantages:
1. **Single Auth Flow:** NextAuth.js (Auth.js) handles Microsoft Entra ID login and protects your API routes effortlessly. No more passing and validating JWTs between two different servers.
2. **Server Actions:** You can write a function to query Cosmos DB and call it directly from a React button click without writing a REST API endpoint.
3. **Unified Typing:** TypeScript interfaces for your metadata are shared instantly between the database and the UI.

## The "Catch" to Watch Out For (Heavy Ingestion)
Next.js is often deployed on serverless platforms (like Vercel), which have strict timeout limits (e.g., 15-60 seconds). Running OCR on a 50-page PDF, chunking it, embedding it, and saving it to Azure AI Search will trigger a timeout. 
* **The Fix:** Deploy your Next.js app to **Azure App Service (Node.js)** instead of Vercel. This gives you a continuous, always-on server environment where background ingestion tasks won't time out. 

---

## Month 1 Goal: The Full-Stack Skeleton

### Week 1: Scaffold & Security (Entra ID)
**Goal:** A secure, deployed web app where users can log in.
* **Initialize Next.js:** Create a Next.js 15 app (App Router). Install Tailwind CSS and `shadcn/ui`.
* **Build the Shell:** Create your root layout with a sidebar (for the Tag Explorer later) and a main content area.
* **Integrate Entra ID:** Install `next-auth`. Create an App Registration in Azure and configure `[...nextauth].ts` (or the Auth.js v5 equivalent) to authenticate users via Microsoft.
* **Protected Routes:** Use Next.js Middleware to redirect unauthenticated users to the Microsoft login page. 

### Week 2: Data & Storage Connections
**Goal:** The server can talk to Azure storage securely.
* **Provision Azure:** Set up Azure Cosmos DB (NoSQL or PostgreSQL) and Azure Blob Storage.
* **Database Connection:** Install the Azure Cosmos DB SDK (or Prisma if using Postgres). Create a central `lib/db.ts` file to manage the database connection singleton.
* **Server Actions (Write):** Create a Server Action `uploadArtifact(data)` that takes form input (Title, Segment, Domain) and writes a record to Cosmos DB.
* **Server Actions (Blob):** Create a Server Action to handle secure file uploads directly to Azure Blob Storage, returning the blob URL to save in Cosmos DB.

### Week 3: AI & Vector Search Scaffolding
**Goal:** Establish the pipes for your RAG architecture.
* **Provision Services:** Deploy Azure AI Search and Azure OpenAI.
* **Index Creation:** Manually create the schema in Azure AI Search for your architecture documents (ID, content_chunk, vector_embedding, and metadata tags).
* **OpenAI Setup:** Create `lib/ai.ts`. Install the OpenAI SDK and configure it to point to your Azure OpenAI endpoint.
* **Test the LLM:** Create a simple Server Action `askQuestion(prompt)` that passes a hardcoded string to GPT-4o and returns the stream to the UI.

### Week 4: The End-to-End Verification
**Goal:** Connect the UI all the way through to the AI and back.
* **The Upload UI:** Build a simple form in Next.js to upload a text file and apply tags.
* **The Pipeline Action:** Write a single Server Action that takes the text file, sends it to Azure OpenAI to get a vector embedding, and pushes the text + vector + tags to Azure AI Search.
* **The Search UI:** Build a basic input box. When a user searches, vector-embed their query, query Azure AI Search, and display the raw JSON results on the screen.

---

## What this means for your progress
By the end of Week 4, your application will look incredibly basic—just a few forms and raw text on the screen. **Do not let this discourage you.** 

You will have successfully connected Next.js, Microsoft Entra ID, Cosmos DB, Blob Storage, Azure AI Search, and Azure OpenAI. You will have solved 100% of the hardest architectural integrations. Month 2 will be purely focused on making the ingestion logic smarter (chunking PDFs) and making the chat interface beautiful.
