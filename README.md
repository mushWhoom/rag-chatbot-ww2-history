# MiniChatBot-History (RAG Implementation)

## Project Overview
**MiniChatBot-History** is an intelligent, context-aware chatbot designed using the **Retrieval-Augmented Generation (RAG)** architecture. Unlike standard large language models (LLMs) that rely solely on their pre-trained knowledge, this system actively extracts and references information from a provided, external Knowledge Base (a PDF document). 

Specifically, this project uses a document detailing the history of **World War II**, allowing the chatbot to accurately answer queries about the timeline, involved alliances (Allies vs. Axis), battle locations across continents, and the political/social impacts of the war.

## System Architecture & Workflow
The chatbot operates through a three-step RAG pipeline to ensure high accuracy and reduce "hallucinations" (invented facts):

### 1. Document Ingestion & Chunking
*   **Process:** The system ingests a source PDF document (`document.pdf`) containing World War II history using `PyPDFLoader`. 
*   **Chunking:** The document is broken down into smaller, manageable text segments (chunks) using `RecursiveCharacterTextSplitter`. This step ensures that the text snippets are small enough to be processed efficiently while retaining contextual meaning through text overlapping (`chunk_overlap`).

### 2. Embedding Model & Vector Database
*   **Embedding Extraction:** The system uses `HuggingFaceEmbeddings` (specifically a multilingual model) to translate the human-readable text chunks into dense mathematical vectors (embeddings). 
*   **Storage & Retrieval:** These vectors are stored in **ChromaDB**, a specialized vector database. When a user asks a question, the question is also converted into a vector. ChromaDB then calculates the distance between the question's vector and the document vectors to retrieve the most relevant text chunks.

### 3. Generation (LLM via Google Gemini)
*   **Response Generation:** The retrieved historical context, along with the user's original prompt, is fed into the **Google Gemini LLM** (via `ChatGoogleGenerativeAI` from the LangChain framework).
*   **Final Output:** The LLM synthesizes the retrieved facts and generates a natural, conversational response in Indonesian (`StrOutputParser`).

## Tech Stack
*   **Framework:** [LangChain](https://www.langchain.com/) (For orchestrating the RAG pipeline)
*   **LLM Provider:** Google Gemini API (`langchain-google-genai`)
*   **Vector Database:** ChromaDB (`langchain-chroma`)
*   **Embeddings:** Hugging Face Transformers (`sentence-transformers`)
*   **Document Loading:** PyPDF (`pypdf`)

## Analysis and Interpretation of the RAG Approach
Implementing a RAG system for historical education provides several significant advantages over using a standalone LLM:

1.  **High Accuracy & Verifiability:** Because the Gemini model is explicitly instructed to answer *only* based on the retrieved vector chunks from ChromaDB, the responses are heavily grounded in the provided PDF. This prevents the model from generating historically inaccurate timelines or mixing up WW2 events with other historical periods.
2.  **Multilingual Capability:** The decision to utilize a multilingual Hugging Face embedding model is crucial. It allows the system to accurately parse Indonesian historical texts and map the semantic meaning of Indonesian queries effectively, ensuring the retrieval mechanism works flawlessly regardless of the language used in the source PDF.
3.  **Dynamic Knowledge Updates:** If the historical context needs to be updated or expanded (e.g., adding a new chapter on the Pacific Theater), we simply append the new PDF text to the database. The Gemini model does not need to be expensively retrained or fine-tuned; it instantly gains access to the new knowledge.

## How to Run the Project
1. Clone this repository: `git clone https://github.com/your-username/repo-name.git`
2. Install the required dependencies: 
   ```bash
   pip install langchain-google-genai langchain langchain-community chromadb pypdf sentence-transformers
