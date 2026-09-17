# 🔐 Secure RAG Pipeline

A Retrieval-Augmented Generation (RAG) pipeline built with **Python, Qdrant Cloud, Sentence Transformers, and Groq LLM**.

This project retrieves relevant information from a knowledge base using semantic search and generates answers using an LLM. It also demonstrates metadata-based filtering for more controlled information retrieval.

## 🚀 Features

* **Semantic Search:** Converts documents and user queries into vector embeddings.
* **Vector Database:** Stores and retrieves embeddings using Qdrant Cloud.
* **Metadata Filtering:** Filters documents based on categories such as `reimbursement`.
* **LLM Integration:** Uses Groq's `openai/gpt-oss-120b` model to generate answers.
* **Secure Configuration:** Uses environment variables to manage API credentials.
* **Local Embedding Generation:** Uses `all-MiniLM-L6-v2` to generate 384-dimensional embeddings.
* **Context-Based Responses:** Instructs the LLM to answer using retrieved context only.

## 🏗️ Architecture

```text
Knowledge Base (knowledge.json)
              |
              ▼
      Document Loading
              |
              ▼
   Sentence Transformer Model
      (all-MiniLM-L6-v2)
              |
              ▼
      Generate Embeddings
              |
              ▼
        Qdrant Cloud
       Vector Database
              |
              ▼
       Semantic Search
              |
              ▼
     Metadata Filtering
              |
              ▼
     Retrieve Relevant Context
              |
              ▼
         Groq LLM
              |
              ▼
        Final Answer
```

## 🛠️ Technologies Used

| Technology            | Purpose                                      |
| --------------------- | -------------------------------------------- |
| Python                | Core programming language                    |
| uv                    | Python environment and dependency management |
| Sentence Transformers | Text embeddings                              |
| Qdrant Cloud          | Vector database                              |
| Groq                  | Large Language Model inference               |
| python-dotenv         | Environment variable management              |
| NumPy                 | Embedding data processing                    |

## 📂 Project Structure

```text
Secure-RAG-pipeline/
│
├── RagCode.py          # Main RAG pipeline
├── main.py             # Supporting Python file
├── knowledge.json      # Knowledge base
├── pyproject.toml      # Project dependencies
├── uv.lock             # Locked dependency versions
├── .gitignore          # Excludes sensitive and unnecessary files
├── .python-version     # Python version configuration
└── README.md           # Project documentation
```

## ⚙️ Installation and Setup

### 1. Clone the repository

```bash
git clone https://github.com/mohdshajid/Secure-RAG-pipeline.git
cd Secure-RAG-pipeline
```

### 2. Install dependencies

Make sure you have [uv](https://docs.astral.sh/uv/) installed.

```bash
uv sync
```

### 3. Configure environment variables

Create a `.env` file in the project root:

```env
QDRANT_URL=your_qdrant_cluster_url
QDRANT_API_KEY=your_qdrant_api_key
GROQ_API_KEY=your_groq_api_key
```

**Never share your API keys or commit your `.env` file to GitHub.**

### 4. Run the pipeline

```bash
uv run python RagCode.py
```

## 📄 Knowledge Base

The project uses a JSON file containing documents and their categories.

Example:

```json
[
  {
    "text": "Employees receive 20 paid vacation days per year.",
    "category": "vacation"
  },
  {
    "text": "Reimbursement requests must be submitted within 30 days.",
    "category": "reimbursement"
  }
]
```

Each document is converted into an embedding and stored in Qdrant with its original metadata.

## 🔍 Metadata Filtering

The pipeline creates a payload index on the `category` field:

```python
client.create_payload_index(
    collection_name=COLLECTION_NAME,
    field_name="category",
    field_schema=PayloadSchemaType.KEYWORD,
)
```

A filter can then be created to retrieve documents from a specific category:

```python
reimbursement_filter = Filter(
    must=[
        FieldCondition(
            key="category",
            match=MatchValue(value="reimbursement")
        )
    ]
)
```

This allows retrieval to be restricted to documents matching the specified metadata.

## 🤖 LLM Response Generation

The retrieved documents are combined into a context and sent to the Groq LLM.

The prompt instructs the model to:

* Use only the provided context.
* Answer the user's question.
* State that it does not know the answer when the information is unavailable.

Example output:

```text
Final Answer:
You receive 20 paid vacation days per year.
```

## 🔐 Security Measures

### 1. Environment-Based Secret Management

API credentials are loaded using environment variables:

```python
from dotenv import load_dotenv

load_dotenv()

QDRANT_API_KEY = os.getenv("QDRANT_API_KEY")
GROQ_API_KEY = os.getenv("GROQ_API_KEY")
```

Credentials are not hardcoded directly into the Python source code.

### 2. `.gitignore` Protection

The `.gitignore` file excludes sensitive and unnecessary files:

```gitignore
.env
.venv/
__pycache__/
*.pyc
```

This helps prevent accidentally committing API keys and local virtual environment files.

### 3. Metadata-Based Access Filtering

Qdrant metadata filtering is implemented to restrict retrieval based on document categories.

For example, documents can be filtered using the `reimbursement` category.

**Note:** Metadata filtering is a retrieval-control mechanism. It should not be treated as a complete authorization system. Production applications should enforce user permissions and access controls before retrieving sensitive information.

### 4. Context-Restricted LLM Prompt

The LLM is instructed to use only the retrieved context and return an uncertainty response when the answer is unavailable.

This reduces unsupported answers, but prompt instructions alone cannot guarantee complete protection against hallucinations or prompt injection.

### 5. No Sensitive Documents in the Repository

The repository should contain only sample or non-confidential knowledge documents. Private company information, personal data, and confidential documents should not be committed to GitHub.

### 6. Secure Dependency Management

The project uses `uv.lock` to maintain consistent dependency versions across installations.

Dependencies should still be updated and reviewed regularly for security vulnerabilities.

## ⚠️ Current Limitations

* The current demonstration recreates the Qdrant collection when the script runs.
* The final RAG pipeline currently uses an unfiltered search function.
* Metadata filtering alone does not provide complete user-level authorization.
* The LLM depends on the quality and relevance of retrieved context.
* API keys must be configured locally before running the project.

## 🔮 Future Improvements

* Implement user-level authorization and document access control.
* Apply metadata filters to the complete RAG pipeline.
* Add reranking for improved retrieval accuracy.
* Implement hybrid search using keyword and semantic retrieval.
* Add prompt-injection detection and document sanitization.
* Build a Streamlit interface for user interaction.
* Add logging, monitoring, and evaluation metrics.
* Support multiple users with isolated knowledge bases.

## 📌 Learning Objectives

This project demonstrates:

* Retrieval-Augmented Generation (RAG)
* Vector embeddings and semantic similarity
* Qdrant vector database operations
* Metadata-based retrieval filtering
* LLM integration with Groq
* Environment variable management
* Basic security considerations in RAG systems

## 👨‍💻 Author

**Mohd Shajid**

GitHub: [mohdshajid](https://github.com/mohdshajid)

Repository: [Secure-RAG-pipeline](https://github.com/mohdshajid/Secure-RAG-pipeline)

## 📜 License

This project is intended for educational and research purposes.
