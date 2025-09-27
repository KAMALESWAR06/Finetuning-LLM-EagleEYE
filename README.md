📘 Maritime RAG Pipeline – README
🚀 Project Overview

This project implements a Retrieval-Augmented Generation (RAG) pipeline for maritime logistics data.
The goal is to let users ask natural language questions (e.g., “How many tanker vessels arrived at Klaipėda on 2021-01-10?”) and get answers grounded in structured CSV port traffic data.

🔑 What’s inside

Data ingestion & preprocessing
Your static maritime dataset (final_data.csv) is split into chunks and embedded.

Vector database (FAISS)
Stores the embeddings for efficient similarity search.

RAG pipeline

Query → Retrieve top-k chunks → Feed to LLM → Get natural-language answer.

Models used

sentence-transformers/all-MiniLM-L6-v2 → embeddings

TinyLlama-1.1B-Chat (local CPU) → answering

(Optional) GPT-4 or other API models can be plugged in.

Why RAG (not fine-tuning)?

Data is static but large → retrieval is enough.

Fine-tuning (QLoRA) is scaffolded but optional, needs GPU.

🛠️ Setup Instructions (Windows 10/11)
1. Navigate to project folder
cd C:\Users\ADMIN\OneDrive\Desktop\maritime_rag_pipeline

2. Create and activate virtual environment
python -m venv .venv
.\.venv\Scripts\Activate.ps1


⚠️ If activation is blocked, run once:

Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass

3. Install dependencies
pip install -r requirements.txt
pip install -U langchain-huggingface langchain-community

📊 Build the Vector Database (FAISS)

This step converts your final_data.csv into searchable embeddings.

python .\scripts\chunk_data.py --input ".\data\final_data.csv" --output .\vector_db --sample_rows 50000 --chunk_size 1000 --chunk_overlap 100 --embedding_model sentence-transformers/all-MiniLM-L6-v2


Loads first 50k rows from your CSV

Splits into chunks of 1000 rows (with 100 overlap)

Embeds using MiniLM

Saves FAISS index → .\vector_db\index.faiss + metadata

💬 Ask Questions (RAG QA)

Now you can ask natural-language questions:

python .\scripts\rag_qa.py --index .\vector_db --query "How many tanker vessels arrived at Klaipėda on 2021-01-10?" --top_k 2


Example output:

🔹 Loading FAISS index...
🔹 Loading model TinyLlama/TinyLlama-1.1B-Chat-v1.0...
🔹 Running query...

✅ Answer:
On 2021-01-10, 2 tanker vessels arrived at Klaipėda.
Sources: retrieved chunks from FAISS


Try more queries:

python .\scripts\rag_qa.py --index .\vector_db --query "Which vessels stayed in Klaipėda longer than 48 hours?" --top_k 3

python .\scripts\rag_qa.py --index .\vector_db --query "What ports had the most tanker arrivals in January 2021?" --top_k 5

📂 Project Structure
maritime_rag_pipeline/
│
├── data/
│   └── final_data.csv         # your cleaned dataset
│
├── vector_db/                 # generated FAISS index & metadata
│
├── scripts/
│   ├── chunk_data.py          # build FAISS vector DB
│   ├── rag_qa.py              # retrieval + LLM QA
│   ├── evaluate_retrieval.py  # check recall@k
│   └── fine_tune_qlora.py     # optional QLoRA fine-tuning
│
├── requirements.txt           # dependencies
└── README.md                  # this guide

⚡ Notes for Recruiters

Tech stack: Python, Pandas, LangChain, FAISS, HuggingFace, TinyLlama (LLM).

What it does: Lets maritime analysts query historical vessel data in plain English.

Why it matters:

Helps detect port congestion, track tanker arrivals, and monitor dwell times.

Shows applied use of RAG + LLM in a real logistics dataset.

Scalability: Works locally, but can be deployed to cloud with API + GPU for 10k+ users.

Extensibility: Can be enriched with CO₂ estimation, congestion logic, and trend analysis.

✅ With this README, you’ll always know:

Which folder to cd into

Which commands to run in order

How to explain the project as a production-ready RAG pipeline
