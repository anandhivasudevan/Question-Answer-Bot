# Question Answer Bot

A Retrieval-Augmented Generation (RAG) chatbot that answers questions from uploaded PDF documents. Built with LangChain, IBM Watsonx AI, and a Gradio web interface.

---

## Overview

This project implements a document-aware question-answering system. Users upload any PDF — a legal contract, technical manual, research paper, or corporate document — and ask questions about its contents in plain English. The bot retrieves the most relevant sections from the document and uses a large language model to generate precise, context-grounded answers.

The pipeline follows the standard RAG pattern: load → split → embed → store → retrieve → generate.

---

## How It Works

**1. Document Loading**
PDF files are loaded using `PyPDFLoader`, which extracts the raw text page by page.

**2. Text Splitting**
The extracted text is chunked using `RecursiveCharacterTextSplitter` (chunk size: 1000 characters, overlap: 50 characters) to preserve context across boundaries.

**3. Embedding**
Each chunk is converted into a vector embedding using IBM Watsonx's `ibm/slate-125m-english-rtrvr` model via `WatsonxEmbeddings`.

**4. Vector Storage**
Embeddings are stored in a `Chroma` vector database, enabling fast semantic similarity search at query time.

**5. Retrieval**
When a question is submitted, the most semantically similar chunks are retrieved from the vector store using a retriever.

**6. Answer Generation**
The retrieved chunks and the user's question are passed to `mistralai/mixtral-8x7b-instruct-v01` via `WatsonxLLM`. The LLM generates a natural-language answer grounded in the document content.

**7. Interface**
The full pipeline is exposed through a `Gradio` web UI running on port 7860, where users upload a PDF and type their questions.

---

## Project Structure

```
Question-Answer-Bot/
├── QA bot          # Main Python script — full RAG pipeline + Gradio UI
└── README.md
```

---

## Tech Stack

| Component          | Technology                                      |
|--------------------|-------------------------------------------------|
| Language           | Python 3                                        |
| LLM                | Mistral AI — Mixtral 8x7B Instruct (via Watsonx)|
| Embeddings         | IBM Slate 125M English Retriever (via Watsonx)  |
| Orchestration      | LangChain                                       |
| Vector Store       | Chroma                                          |
| Document Loader    | LangChain PyPDFLoader                           |
| Text Splitting     | RecursiveCharacterTextSplitter                  |
| Frontend           | Gradio                                          |
| AI Platform        | IBM Watsonx AI                                  |

---

## Getting Started

### Prerequisites

- Python 3.8+
- An IBM Watsonx AI account with access to the `skills-network` project

### Installation

```bash
# Clone the repository
git clone https://github.com/anandhivasudevan/Question-Answer-Bot.git
cd Question-Answer-Bot

# Install dependencies
pip install ibm-watsonx-ai langchain langchain-ibm langchain-community \
            chromadb pypdf gradio
```

### Running the App

```bash
python "QA bot"
```

The Gradio interface will be available at `http://0.0.0.0:7860`.

---

## Usage

1. Open the Gradio interface in your browser.
2. Upload a PDF file using the file upload field.
3. Type a question about the document in the input box.
4. The bot returns an answer extracted and synthesized from the document.

---

## Configuration

The following parameters can be adjusted inside the script:

| Parameter              | Default                              | Description                            |
|------------------------|--------------------------------------|----------------------------------------|
| `model_id`             | `mistralai/mixtral-8x7b-instruct-v01`| LLM used for answer generation         |
| `MAX_NEW_TOKENS`       | `256`                                | Maximum tokens in the generated answer |
| `TEMPERATURE`          | `0.5`                                | Controls response randomness           |
| `chunk_size`           | `1000`                               | Characters per text chunk              |
| `chunk_overlap`        | `50`                                 | Overlap between chunks                 |
| `embedding model`      | `ibm/slate-125m-english-rtrvr`       | Model used to embed document chunks    |

---

## Architecture

```
PDF File
   |
   v
PyPDFLoader  -->  RecursiveCharacterTextSplitter  -->  WatsonxEmbeddings
                                                              |
                                                              v
                                                        Chroma VectorDB
                                                              |
                                              User Query --> Retriever
                                                              |
                                                              v
                                                   WatsonxLLM (Mixtral 8x7B)
                                                              |
                                                              v
                                                        Answer (Gradio UI)
```

---

## License

This project is open source. See the [LICENSE](LICENSE) file for details.

---
