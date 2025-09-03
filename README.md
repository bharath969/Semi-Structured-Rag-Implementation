# 📚 Semi-structured RAG

This project demonstrates how to perform **Retrieval-Augmented Generation (RAG)** on semi-structured documents such as PDFs containing text, tables, and images.

---

## 🚀 Overview

Traditional RAG pipelines struggle with **semi-structured data**:

- Text splitting can break tables and distort meaning.
- Tables stored as raw HTML or text often embed poorly.
- Images are ignored or poorly represented.

This project solves those issues by:

1. Extracting **text, tables, and images** separately.
2. Converting **tables from HTML → Markdown** for better embedding quality.
3. Embedding **text, tables, and images separately** using appropriate models.
4. Storing embeddings in **Redis** as a vector database.
5. Using **multi-vector retrieval** to provide context-rich answers via LLMs.

---

## 📂 Workflow

### 1. Data Extraction

- Use `UnstructuredPDFLoader` in two modes:
  - Extract **tables & images** without chunking.
  - Extract **text** with chunking (to fit embedding context).

### 2. Table Conversion

- Tables are extracted in **HTML** format.
- Converted to **Markdown** using `htmltabletomd` → ensures embeddings capture **row/column semantics** better.

### 3. Embeddings

Each content type is handled separately:

- **Text Chunks** → embedded using a **text embedding model**.
- **Tables (Markdown)** → embedded as structured text.
- **Images** → processed with **captioning / CLIP embeddings** to capture visual meaning.

This separation ensures **better semantic search** across multiple data modalities.

### 4. Vector Store (Redis)

- All embeddings + metadata (page number, type: text/table/image) are stored in **Redis**.
- Queries are also embedded, and Redis performs **nearest-neighbor search**.
- Retrieved chunks are combined into a **multi-vector retriever** for context.

### 5. Retrieval & QA

- Retrieved context is fed into an **LLM (GPT-4o, Groq LLaMA-4 Scout, etc.)**.
- The LLM generates **grounded answers** using both textual and structured data.

### 6. Visualization

- **Tables** are rendered in Markdown.
- **Images** are displayed inline in the notebook.

---

## ⚙️ Installation

````bash
# Clone the repository
git clone https://github.com/yourusername/semi-structured-rag.git
cd semi-structured-rag

# Create virtual environment (named rag_env)
python -m venv rag_env

# Activate environment
source rag_env/bin/activate   # On Windows: rag_env\\Scripts\\activate

# Install dependencies
pip install -r requirements.txt

---

## 🗄️ Redis Setup (Docker)

Make sure Redis is running before starting the notebook:

```bash
# Run Redis container
docker run -d   --name rag-redis   -p 6379:6379   redis/redis-stack:latest
````

This will start Redis and expose it on port **6379**.

---

## ▶️ Usage

1. Place your PDF inside the project folder.
2. Start Jupyter Notebook inside the virtual environment:

   ```bash
   jupyter notebook semi_structured_rag.ipynb
   ```

3. The notebook will:
   - Extract **text, tables, and images** from your document.
   - Convert tables to **Markdown** for embedding.
   - Store data in **Redis** as a vector store.
   - Allow you to ask questions powered by **RAG + LLMs**.

---

## 📊 Example

- Extracted **tables** are displayed as Markdown.
- Extracted **images** are displayed inline.
- Queries like: _"What are the key statistics in Table 2?"_ will retrieve both **text and table context**.

---

## 📌 Future Improvements

- Add support for **multi-document RAG**.
- Enhance table summarization with fine-tuned LLMs.
- Extend vector store support beyond Redis (e.g., Pinecone, Weaviate, Milvus).

---

## 📝 License

This project is licensed under the MIT License.
