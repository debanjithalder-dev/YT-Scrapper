# 🎯 YouTube DSA Lecture RAG System

An AI-powered **Retrieval-Augmented Generation (RAG)** system that helps students find specific concepts within a creator's YouTube DSA playlist.

Instead of manually searching through hours of lecture videos, students can simply ask questions in English or Hinglish and receive context-aware answers along with clickable timestamps that take them directly to the relevant explanation in the video.

## 🚀 Key Features

- **Semantic Search:** Retrieve relevant lecture segments using natural language queries.
- **Timestamp-Level Retrieval:** Get precise timestamps corresponding to the explanation of a particular concept.
- **Context-Aware Answers:** Generate answers grounded in the actual lecture transcripts rather than relying solely on the LLM's pretrained knowledge.
- **Hinglish & English Support:** Accept questions in both English and Hinglish.
- **Interactive Video Playback:** Click on a retrieved timestamp to jump directly to the relevant moment within the embedded YouTube player.
- **Hallucination Prevention:** Reject unrelated questions when the required information is not available in the indexed lectures.
- **Efficient Transcript Caching:** Avoid unnecessary re-transcription when rebuilding or updating the vector index.
- **Retrieval Evaluation:** Evaluate retrieval performance using a manually curated golden dataset.

## 🏗️ System Architecture

The system follows a multi-stage RAG pipeline:

1. **Playlist Extraction:** Extract video metadata and download audio from the selected YouTube DSA playlist using `yt-dlp`.
2. **Speech-to-Text:** Convert lecture audio into text transcripts using `faster-whisper`.
3. **Time-Based Chunking:** Divide transcripts into overlapping chunks while preserving their original timestamps.
4. **Embedding Generation:** Generate semantic embeddings using `BAAI/bge-m3` or a lightweight alternative.
5. **Vector Storage:** Store embeddings and associated metadata in Qdrant.
6. **Semantic Retrieval:** Retrieve the most relevant lecture segments based on the student's query.
7. **Answer Generation:** Use an LLM through Groq or Gemini to generate a concise, context-grounded response.
8. **Timestamp Citation:** Return clickable video references that seek directly to the relevant explanation.

## 🛠️ Technology Stack

| Component | Technology |
|---|---|
| Programming Language | Python |
| Video Extraction | yt-dlp |
| Speech Recognition | faster-whisper |
| Embedding Model | BAAI/bge-m3 |
| Lightweight Embedding | all-MiniLM-L6-v2 |
| Vector Database | Qdrant |
| LLM | Groq / Gemini |
| Backend | FastAPI |
| Frontend | HTML, CSS, JavaScript |
| Video Integration | YouTube IFrame API |
| CLI | Typer, Rich |
| Environment Management | uv |

## 📂 Project Structure

```text
ytscraper/
│
├── ytrag/
│   ├── config.py
│   ├── playlist.py
│   ├── transcribe.py
│   ├── chunk.py
│   ├── embed.py
│   ├── index.py
│   ├── answer.py
│   ├── evaluate.py
│   └── cli.py
│
├── api/
│   ├── main.py
│   └── static/
│       └── index.html
│
├── eval/
│   └── golden.json
│
├── transcripts/
├── index/
├── requirements.txt
├── pyproject.toml
├── .env.example
└── README.md
```

## ⚙️ Installation & Setup

### 1. Clone the Repository

```bash
git clone <REPO_URL>
cd week3/ytscraper
```

### 2. Install Dependencies

Install `uv` if it is not already installed:

```bash
pip install uv
```

Create the virtual environment and install dependencies:

```bash
uv sync
```

### 3. Configure Environment Variables

Create a `.env` file using the provided example:

```bash
cp .env.example .env
```

Add your API credentials:

```env
GROQ_API_KEY=your_groq_api_key
GEMINI_API_KEY=your_gemini_api_key
```

You only need to configure the API key for the LLM provider you intend to use.

- Groq: https://console.groq.com/keys
- Gemini: https://aistudio.google.com/apikey

### 4. Load the Prebuilt Index

```bash
uv run ytrag load
```

This loads the precomputed vector index and avoids downloading and transcribing the entire playlist again.

### 5. Start the Application

```bash
uv run ytrag serve
```

Open the application in your browser:

```text
http://127.0.0.1:8000
```

You can now ask questions and retrieve relevant lecture explanations with timestamps.

## 💻 Usage

### Start the Web Application

```bash
uv run ytrag serve
```

### Ask a Question Through CLI

```bash
uv run ytrag ask "memoization aur tabulation mein kya difference hai?"
```

### Perform Semantic Search

```bash
uv run ytrag search "sliding window"
```

### View Index Statistics

```bash
uv run ytrag stats
```

### Evaluate Retrieval Performance

```bash
uv run ytrag eval --verbose
```

## 📊 Retrieval Evaluation

The system uses a manually curated golden dataset to evaluate retrieval quality.

Evaluation considers:

- Whether the expected lecture appears within the top-K retrieved results.
- Whether the retrieved timestamp falls within the expected time range.
- Whether unrelated questions are correctly rejected.

This helps measure retrieval accuracy and ensure that returned timestamps correspond to the actual lecture explanations.

## 🛡️ Reliability & Hallucination Control

A key design consideration is ensuring that generated answers are supported by retrieved lecture content.

The system incorporates:

- Similarity-distance thresholding to filter irrelevant results.
- Refusal handling for topics not covered in the lectures.
- Citation validation to prevent unsupported video references.
- Cached transcripts to avoid expensive repeated transcription.
- Idempotent indexing to prevent duplicate vector entries.

If a topic is not covered in the indexed lectures, the system responds:

**"Ye topic in lectures me cover nahi hua."**


**Built with Python, RAG, Vector Search and Generative AI.**
