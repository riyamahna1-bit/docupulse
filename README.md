<<<<<<< HEAD
# docupulse
=======
# RAG App – PDF Question Answering with Gemini + Qdrant

A Retrieval-Augmented Generation (RAG) pipeline that lets you ingest PDF documents and ask questions about their content. Built with **Inngest** for orchestration, **Google Gemini** for embeddings & generation, and **Qdrant** as the vector database.

## How It Works

1. **Ingest** – Upload a PDF, it gets chunked into text segments, embedded using Gemini, and stored in Qdrant.
2. **Query** – Ask a question, it gets embedded, relevant chunks are retrieved from Qdrant, and Gemini generates an answer using that context.

## Tech Stack

- **FastAPI** – web server
- **Inngest** – event-driven function orchestration (background jobs, retries, step functions)
- **Google Gemini API** (`google-genai`) – embeddings (`gemini-embedding-001`) + text generation (`gemini-2.5-flash`)
- **Qdrant** – vector database (running via Docker)
- **LlamaIndex** – PDF parsing & text chunking
- **Pydantic** – data validation and serialization

## Project Structure
aiagentproject/

├── main.py            # Inngest functions: PDF ingestion + RAG query

├── data_loader.py      # PDF loading, chunking, and embedding logic

├── vector_db.py         # Qdrant wrapper (upsert + search)

├── custom_types.py     # Pydantic models for type-safe step outputs

├── .env                # API keys (GEMINI_API_KEY)

└── qdrant_storage/      # Persistent Qdrant data (Docker volume)

## Setup

### 1. Install dependencies
```bash
pip install inngest fastapi uvicorn google-genai llama-index llama-index-readers-file qdrant-client python-dotenv
```

### 2. Set environment variables
Create a `.env` file:
GEMINI_API_KEY=your_key_here

### 3. Start Qdrant (Docker)
```bash
docker run -d --name qdrant -p 6333:6333 -v "${PWD}/qdrant_storage:/qdrant/storage" qdrant/qdrant
```

### 4. Start the Inngest dev server
```bash
npx inngest-cli@latest dev
```

### 5. Start the FastAPI app
```bash
uvicorn main:app --reload
```

## Usage

### Ingest a PDF
Trigger the `rag/ingest_pdf` event via the Inngest dashboard (`localhost:8288`):
```json
{
  "data": {
    "pdf_path": "C:/path/to/your/file.pdf"
  }
}
```

### Ask a question
Trigger the `rag/query_pdf_ai` event:
```json
{
  "data": {
    "question": "What does the document say about X?",
    "top_k": 5
  }
}
```

## Key Implementation Notes

- Embeddings use Gemini's `gemini-embedding-001` model (3072 dimensions)
- Gemini client is pinned to API version `v1` to avoid model availability issues on `v1beta`
- Qdrant's `PointStruct` requires `vector` (singular) and plain Python lists, not numpy arrays
- Vector search uses `query_points()` (the modern Qdrant client API, replacing the deprecated `.search()`)
>>>>>>> 3e025f7 (Initial commit: Add RAG framework and streamlit app)
