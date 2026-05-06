# 🔍 InSight Forge: Academic Research Agent

**InSight Forge** is an advanced, autonomous academic research assistant powered by **CrewAI**, **Exa AI**, and **Streamlit**. It is designed to act as a Senior Academic Research Analyst, conducting deep, critical, and insightful explorations of any given topic or uploaded research papers.

![CrewAI Logo](https://cdn.prod.website-files.com/66cf2bfc3ed15b02da0ca770/66d07240057721394308addd_Logo%20(1).svg)  

## ✨ Key Features

- **Multi-LLM Support**: Seamlessly integrate with OpenAI (GPT-4o, o1, etc.), GROQ (Llama 3, DeepSeek, Qwen), and local Ollama models.
- **Deep Web Search**: Powered by Exa AI, it intelligently queries the web to find the most relevant academic sources and citations.
- **PDF Analysis Capability**: Upload multiple PDF research papers for the agent to read, parse, and incorporate into its in-depth analysis.
- **Detailed Markdown Reports**: Automatically generates structured academic reports including Executive Summaries, Key Findings, Research Gaps, and Critiques.
- **Live Output Tracking**: Real-time visibility into the agent's thought processes and actions via Streamlit.
- **Privacy & Security**: API keys are handled entirely in memory and cleared upon session end.

---

## 🛠️ Technology Stack & Architecture

The application is built using a modular architecture to separate UI from agent logic:

- **Frontend**: [Streamlit](https://streamlit.io/) provides a sleek, responsive, and interactive user interface.
- **Orchestration**: [CrewAI](https://crewai.com/) orchestrates the autonomous agent workflow, tools, and tasks.
- **Search Engine**: [Exa AI](https://exa.ai) acts as the neural search engine for finding the most accurate web citations.
- **PDF Parsing**: `PyMuPDF` (via `pypdf`) is used to extract text from uploaded research papers.
- **LLM Integration**: Langchain/CrewAI native LLM wrappers connect to OpenAI, GROQ, and Ollama.

### Code Structure

```text
manual-literature-review/
├── app.py                  # Main Streamlit application and UI layout
├── requirements.txt        # Project dependencies
└── source/
    ├── components/
    │   ├── researcher.py   # Defines the CrewAI Agent, Task, and custom Tools (Exa, PDF)
    │   └── sidebar.py      # Manages the Streamlit sidebar, model selection, and API keys
    └── utils/
        └── output_handler.py # Captures standard output for real-time Streamlit display
```

---

## 🧩 Detailed Component Breakdown

### 1. Main App (`app.py`)
This is the entry point. It configures the Streamlit page layout and handles the SQLite workaround required by ChromaDB (used internally by CrewAI). It renders the UI components, manages PDF file uploads via Streamlit's `file_uploader`, and initializes the CrewAI execution flow, capturing real-time logs.

### 2. The Researcher Agent (`source/components/researcher.py`)
This module contains the core AI logic and custom tools:
- **`PDFAnalysisTool`**: A custom `BaseTool` that reads temporary PDF files using `PdfReader` and extracts up to 12,000 characters of text for the agent to analyze.
- **`EXAAnswerTool`**: A custom `BaseTool` that sends natural language queries to Exa AI's `/answer` endpoint, returning highly relevant answers along with source citations.
- **`create_researcher()`**: Initializes a single CrewAI `Agent` with the role "Senior Academic Research Analyst". It assigns the chosen LLM provider and equips the agent with both the Exa and PDF analysis tools.
- **`create_research_task()`**: Defines the exact expected output format (Executive Summary, Key Findings, In-Depth Analysis, Research Gaps, Critique, Conclusion, and Sources).
- **`run_research()`**: Initializes the `Crew` and executes the sequential process to generate the report.

### 3. Sidebar & Configuration (`source/components/sidebar.py`)
Manages all user configurations:
- Detects running local Ollama instances via its API (`http://localhost:11434/api/tags`).
- Presents LLM provider and model selection dropdowns.
- Securely accepts API keys (`OPENAI_API_KEY`, `GROQ_API_KEY`, `EXA_API_KEY`) and stores them in `os.environ` for the duration of the session.

### 4. Output Handler (`source/utils/output_handler.py`)
A context manager (`capture_output`) that intercepts `sys.stdout` and `sys.stderr` to display the agent's real-time inner monologue, tool usage, and action logs directly inside the Streamlit UI, mimicking a terminal experience.

---

## 📋 Prerequisites

- **Python**: Version 3.10+ (Tested up to <3.13)
- **API Keys**:
  - [OpenAI](https://platform.openai.com/) OR [GROQ](https://console.groq.com/) API Key.
  - [Exa AI](https://exa.ai) API Key (required for web search capabilities; not strictly needed if only using Ollama models that don't trigger the search tool).
- **Ollama** (Optional): If you plan to run models locally.

---

## 🚀 Installation & Quick Start

1. **Clone the repository:**
   ```bash
   git clone <your-repo-url>
   cd manual-literature-review
   ```

2. **Create and activate a virtual environment:**
   ```bash
   python -m venv .venv
   source .venv/bin/activate  # On Windows use: .venv\Scripts\activate
   ```

3. **Install dependencies:**
   ```bash
   pip install -r requirements.txt
   ```
   *Note: The project uses `pysqlite3-binary` to override the system SQLite3 to satisfy ChromaDB requirements on older systems.*

4. **Launch the application:**
   ```bash
   streamlit run app.py
   ```

---

## 💡 Usage Guide

1. **Configure the Environment**: Open the app in your browser (usually `http://localhost:8501`). On the left sidebar, select your LLM provider (OpenAI, GROQ, or Ollama) and enter your corresponding API keys.
2. **Define the Research**: In the main area, enter your research topic or question. Be as specific as possible.
3. **Upload PDFs (Optional)**: If you have specific papers you want the agent to review, upload them using the file uploader.
4. **Execute**: Click **Start Research**.
5. **Monitor**: Watch the live logs as the agent searches the web, reads PDFs, and synthesizes the data.
6. **Download**: Once complete, read the generated markdown report and use the provided button to download it as a `.md` file.

---

## ⚠️ Known Limitations

- **Ollama Web Search**: Models running via Ollama generally have limited function-calling capabilities compared to OpenAI/GROQ. As such, Exa web search might not trigger effectively when using local models.
- **PDF Context Limit**: The current PDF parser extracts a maximum of 12,000 characters per PDF to avoid exceeding the context window limits of smaller models and limiting token costs.

---

