# Intelligent Website Chatbot: RAG-Powered Knowledge Agent

This **n8n workflow** is the engine behind an intelligent website chatbot. It moves beyond simple FAQ responses by using a **Retrieval-Augmented Generation (RAG)** framework. This means your bot doesn't just "make up" answers; it searches a dedicated knowledge base (**Supabase Vector Store**) for facts *before* crafting a clear, accurate response with the help of an **OpenAI Chat Model**.

It’s built entirely in n8n, making it easy to deploy, manage, and scale without touching complex code.

## Core Features

* **Fact-Grounded Responses (RAG):** The bot uses your internal documents as its single source of truth, minimizing hallucinations and maximizing accuracy.
* **Conversation Memory:** Maintains context across a dialogue, allowing users to ask follow-up questions without repeating themselves.
* **Integrated Knowledge Management:** A separate, easy-to-use flow lets you upload new files (e.g., product manuals, guides) directly into your vector store to instantly update the bot's knowledge.
* **Low-Code Power:** The entire system is managed via a visual n8n canvas, simplifying maintenance and updates.

---

## How the Workflow is Structured

The n8n canvas is logically split into two primary functions: the **Chat Flow** (handling customer questions) and the **Ingestion Flow** (handling document updates).

### 1. The Chat Interaction Flow (Left Side)

This is the real-time customer-facing pathway.

| Node | What it Does |
| :--- | :--- |
| **When chat message received** | The entry point—it wakes up when a user types a message. |
| **AI Agent** | The core brain. It orchestrates the entire response process: figuring out the user's intent, using the RAG tool to get context, and maintaining the chat history. |
| **OpenAI Chat Model** | The large language model (LLM) that generates the final, human-sounding response. |
| **Simple Memory** | A basic database that holds the conversation history so the bot remembers what was just said. |
| **Supabase Vector Store** (as a Tool) | The key RAG component. The AI Agent uses this to search your document vectors for relevant chunks of information *before* answering. |

### 2. The Knowledge Base Ingestion Flow (Right Side)

This flow is for developers/admins to upload and update the bot’s knowledge base.

| Node | What it Does |
| :--- | :--- |
| **When clicking 'Execute workflow'** | Manual trigger to start the process—used whenever you have a new document to add. |
| **Download file** | Pulls the document (PDF, TXT, etc.) into the workflow for processing. |
| **Default Data Loader** | A crucial step: it takes the raw file and smartly splits it into smaller, manageable chunks, which is essential for effective RAG search. |
| **Embeddings OpenAI** | Generates vector representations (numeric codes) for each text chunk. These vectors are what the RAG search actually uses. |
| **Supabase Vector Store1** | Takes the new vectors and the corresponding text and saves them into your Supabase database, updating the bot's knowledge. |

---

## Prerequisites and Setup

You'll need a few external services and credentials configured in your n8n instance:

1.  **n8n Instance:** A self-hosted or cloud instance of n8n.
2.  **OpenAI Account:** Required for the Chat Model and the Embeddings service. You'll need an **API Key**.
3.  **Supabase Project:** A working Supabase instance configured with the $\text{pgvector}$ extension to store your document embeddings.
4.  **n8n Credentials:** Make sure you have the respective **OpenAI** and **Supabase** credentials set up correctly within n8n.

### Getting It Running:

1.  **Import the Workflow:** Load the provided n8n JSON file into your n8n canvas.
2.  **Connect Credentials:** Double-check that all **OpenAI** and **Supabase** nodes are correctly linked to your existing n8n credentials.
3.  **Load Knowledge:** Execute the **Knowledge Base Ingestion Flow** (the right-hand side) to upload your initial set of documents.
4.  **Activate:** Turn the main workflow **ON** and connect the `When chat message received` trigger to your frontend interface.

That's it! Your new intelligent, RAG-powered chatbot is ready for action.
