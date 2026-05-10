# Chapter 5 – Implementation

Implementation is the phase of software development in which the system design is transformed into a working application through coding, integration, and deployment of various components. In "InSight Forge," the implementation phase focuses on developing an intelligent research automation system capable of processing user queries, analyzing documents, retrieving information from online sources, and generating structured research reports. The system integrates multiple technologies and tools to create a unified research workflow.

## 5.1 Technologies Used

The development of "InSight Forge" involves the integration of various technologies, frameworks, and libraries. Each technology serves a specific purpose in the research workflow. The selection of these technologies is based on factors such as flexibility, ease of integration, scalability, and support for Artificial Intelligence applications.

The major technologies used in the implementation are:

- **Python [13]:**
  Python is used as the primary programming language for the entire system. It was chosen due to its extensive ecosystem of AI and NLP libraries, simple syntax for rapid prototyping, and strong community support. All backend logic, tool integration, and processing modules are implemented in Python.

- **Streamlit [7]:**
  Streamlit is used for developing the web-based user interface. It enables rapid creation of interactive web applications directly from Python code, eliminating the need for separate frontend development. In InSight Forge, Streamlit handles the page layout, sidebar configuration, research input, real-time progress display, and report rendering.

- **CrewAI [5]:**
  CrewAI is the agent orchestration framework that forms the core of the system's research workflow. It provides abstractions for defining AI agents with specific roles, goals, and tools, and manages the sequential execution of research tasks. CrewAI enables the autonomous decision-making behavior of the research agent.

- **Large Language Models (LLMs):**
  The system supports three LLM providers for natural language understanding and report generation:
  - **OpenAI** [3]: Cloud-based models including GPT-4o, GPT-4o-mini, o1, o1-mini, o1-preview, and o3-mini
  - **GROQ**: Cloud-based inference for models including Qwen 2.5-32B, DeepSeek R1, and LLaMA 3.3-70B
  - **Ollama** [8]: A local LLM platform for running open-source models such as LLaMA 3.2 [14] on the user's own hardware, enabling fully offline operation

- **Exa AI (`exa-py`) [6]:**
  The Exa AI search API is used for web-based information retrieval. Unlike general-purpose search engines, Exa uses neural search to find research-quality content. The `exa-py` library is used to communicate with the Exa answer API endpoint, which returns answers with verified citations (title + URL).

- **pypdf [9]:**
  The `pypdf` library is used for extracting textual content from uploaded PDF research papers. It reads PDF files page by page and returns the extracted text, which the research agent incorporates into its analysis.

- **Pydantic [10]:**
  Pydantic is used for input validation in the EXAAnswerTool. It defines the `EXAAnswerToolSchema` class, which ensures that the tool receives a valid query string before making API calls.

- **Requests:**
  The `requests` library provides HTTP communication capabilities. It is used for making API calls to the Exa AI answer endpoint and for querying the local Ollama instance to discover available models.

- **pysqlite3-binary:**
  This library provides SQLite3 compatibility for ChromaDB, which is used internally by CrewAI. The system patches `sys.modules` at startup to replace the default `sqlite3` module with `pysqlite3`, resolving version compatibility issues on certain operating systems.

## 5.2 Development Environment

The development environment for InSight Forge is configured to support AI-based processing, web application development, and tool integration.

**Hardware Requirements:**

| Component | Requirement |
|-----------|------------|
| RAM | 8 GB or higher |
| Processor | AMD Ryzen 5 / Intel Core i5 or higher |
| Storage | SSD (recommended for faster file I/O) |
| Network | Stable internet connection (for cloud-based LLM and Exa API access) |

**Software Requirements:**

| Software | Version / Details | Purpose |
|----------|------------------|---------|
| Python | 3.10 or higher | Core programming language |
| Streamlit | Latest | User interface development |
| CrewAI | Latest | Research agent workflow orchestration |
| exa-py | Latest | Web-based information retrieval |
| pypdf | Latest | PDF document text extraction |
| Requests | Latest | HTTP communication and API calls |
| pysqlite3-binary | Latest | SQLite compatibility for ChromaDB |
| Pydantic | Latest (transitive dependency) | Input data validation |

**Operating System:**
Any OS capable of running Python-based applications — Windows 10/11, Linux (Ubuntu, Fedora), or macOS.

**Optional:**
- **Ollama** (for local LLM inference): Requires installation from https://ollama.com and at least one model downloaded (e.g., `ollama pull llama3.2`)

## 5.3 Implementation Details

The implementation of InSight Forge follows a modular structure where each component is responsible for a specific functionality. The major implementation aspects are described below, with key code excerpts from the actual source files.

### 5.3.1 Frontend Implementation (Streamlit)

The user interface is implemented using Streamlit in `app.py`. The interface provides an interactive environment for user communication with the system.

The Streamlit page is configured with a wide layout and expanded sidebar:

**Code Snippet 5.1 — SQLite Patching and Page Configuration (`app.py`):**
```python
# Handle SQLite for ChromaDB
try:
    __import__('pysqlite3')
    import sys
    sys.modules['sqlite3'] = sys.modules.pop('pysqlite3')
except (ImportError, KeyError):
    pass

import streamlit as st

# Configure the page
st.set_page_config(
    page_title="InSight Forge",
    page_icon="Detective",
    layout="wide",
    initial_sidebar_state="expanded"
)
```

The SQLite patching is performed at the top of the file, before any other imports, to ensure ChromaDB (used internally by CrewAI) receives a compatible SQLite version. The `try/except` block ensures the application works even if `pysqlite3-binary` is not installed.

The interface provides a text area for research queries and a multi-file PDF uploader:

**Code Snippet 5.2 — PDF Upload and Temporary File Handling (`app.py`):**
```python
# Handle uploaded PDFs
full_task = task_description
if uploaded_pdfs:
    pdf_paths = []
    for uploaded_file in uploaded_pdfs:
        with tempfile.NamedTemporaryFile(delete=False, suffix=".pdf") as tmp:
            tmp.write(uploaded_file.getbuffer())
            pdf_paths.append(tmp.name)
    full_task += f"\n\nAlso analyze these uploaded PDFs: {pdf_paths}"
```

Each uploaded PDF is saved to a temporary file with `delete=False` to persist across Streamlit's re-execution model. The file paths are appended to the task description so the research agent knows which PDFs to analyze.

### 5.3.2 Sidebar Configuration (`sidebar.py`)

The sidebar handles all user configuration, including provider selection, model choice, and API key management.

**Code Snippet 5.3 — Ollama Model Discovery (`sidebar.py`):**
```python
def get_ollama_models():
    """Get list of available Ollama models from local instance."""
    try:
        response = requests.get("http://localhost:11434/api/tags")
        if response.status_code == 200:
            models = response.json()
            return [model["name"] for model in models["models"]]
        return []
    except:
        return []
```

This function queries the local Ollama instance API to dynamically discover available models. It fails silently (returns an empty list) if Ollama is not running, ensuring the application remains functional regardless of Ollama's availability.

API keys are stored as environment variables for the session duration and are never written to disk:

```python
if openai_api_key:
    os.environ["OPENAI_API_KEY"] = openai_api_key
```

### 5.3.3 Custom Tool Development (`researcher.py`)

The system implements two custom tools as classes extending CrewAI's `BaseTool`:

**Code Snippet 5.4 — PDFAnalysisTool Implementation (`researcher.py`):**
```python
class PDFAnalysisTool(BaseTool):
    name: str = "Analyze Uploaded PDF"
    description: str = "Read and analyze research paper PDF files uploaded by the user"

    def _run(self, pdf_path: str):
        try:
            reader = PdfReader(pdf_path)
            text = ""
            for page in reader.pages:
                text += page.extract_text() or ""
            return text[:12000]
        except Exception as e:
            return f"PDF reading error: {str(e)}"
```

The tool uses `pypdf.PdfReader` to extract text from all pages of the uploaded PDF. The content is truncated to 12,000 characters to stay within the context limits of smaller LLMs. Error handling ensures that corrupted or unreadable PDFs produce descriptive error messages rather than crashing the application.

**Code Snippet 5.5 — EXAAnswerTool Implementation (`researcher.py`):**
```python
class EXAAnswerToolSchema(BaseModel):
    query: str = Field(..., description="The question you want to ask Exa.")

class EXAAnswerTool(BaseTool):
    name: str = "Ask Exa a question"
    description: str = "A tool that asks Exa a question and returns the answer."
    args_schema: Type[BaseModel] = EXAAnswerToolSchema
    answer_url: str = "https://api.exa.ai/answer"

    def _run(self, query: str):
        headers = {
            "accept": "application/json",
            "content-type": "application/json",
            "x-api-key": os.environ.get("EXA_API_KEY")
        }
        response = requests.post(
            self.answer_url,
            json={"query": query, "text": True},
            headers=headers
        )
        response.raise_for_status()
        response_data = response.json()
        answer = response_data["answer"]
        citations = response_data.get("citations", [])
        output = f"Answer: {answer}\n\n"
        if citations:
            output += "Citations:\n"
            for citation in citations:
                output += f"- {citation['title']} ({citation['url']})\n"
        return output
```

The EXAAnswerTool sends natural-language queries to the Exa AI answer API via HTTP POST. Input validation is handled by the Pydantic `EXAAnswerToolSchema`, which ensures a valid query string is provided. The tool authenticates using the `EXA_API_KEY` environment variable and returns the answer along with formatted citations.

### 5.3.4 AI Agent and Task Configuration (`researcher.py`)

The research agent is created with a specific role, goal, and backstory that guide its behavior:

**Code Snippet 5.6 — Agent Creation (`researcher.py`):**
```python
def create_researcher(selection):
    """Create a research agent with the specified LLM configuration."""
    provider = selection["provider"]
    model = selection["model"]

    if provider == "GROQ":
        llm = LLM(api_key=os.environ.get("GROQ_API_KEY"), model=f"groq/{model}")
    elif provider == "Ollama":
        llm = LLM(base_url="http://localhost:11434", model=f"ollama/{model}")
    else:
        llm = LLM(api_key=os.environ.get("OPENAI_API_KEY"), model=f"openai/{model}")

    researcher = Agent(
        role='Senior Academic Research Analyst',
        goal='Produce extremely deep, critical, and insightful academic research reports',
        backstory='You are a PhD-level researcher with 15+ years experience...',
        tools=[EXAAnswerTool(), PDFAnalysisTool()],
        llm=llm,
        verbose=True,
        allow_delegation=False,
    )
    return researcher
```

The agent is configured with the role "Senior Academic Research Analyst" and equipped with both the EXA and PDF tools. The LLM is dynamically configured based on the user's provider selection. `allow_delegation=False` ensures a single-agent architecture for simplicity.

The expected output template enforces a consistent 7-section report structure:

**Code Snippet 5.7 — Expected Output Template (`researcher.py`):**
```python
expected_output="""
# Executive Summary
(3-4 strong paragraphs with key takeaways and significance)

# Key Findings
- 6-10 detailed bullet points with supporting evidence

# In-Depth Analysis
• Compare different approaches/methodologies
• Discuss strengths and limitations of existing work
• Highlight contradictions or debates in the field

# Research Gaps & Future Directions
• Clearly identify 4-6 important gaps in current research
• Suggest specific future research questions

# Critique and Comparison of Research
• Strengths and weaknesses of each paper
• Comparison between different methodologies and results

# Conclusion
Strong concluding insights and recommendations

# Sources
Numbered list with full titles, authors, and URLs
"""
```

### 5.3.5 Real-Time Output Capture (`output_handler.py`)

The Output Handler provides real-time visibility into the agent's processing through a custom stdout replacement:

**Code Snippet 5.8 — StreamlitProcessOutput and ANSI Cleaning (`output_handler.py`):**
```python
class StreamlitProcessOutput:
    def __init__(self, container):
        self.container = container
        self.output_text = ""
        self.seen_lines = set()

    def clean_text(self, text):
        # Remove ANSI escape codes
        ansi_escape = re.compile(r'\x1B(?:[@-Z\\-_]|\[[0-?]*[ -/]*[@-~])')
        text = ansi_escape.sub('', text)
        # Remove LiteLLM debug messages
        if text.strip().startswith('LiteLLM.Info:') or \
           text.strip().startswith('Provider List:'):
            return None
        # Clean up formatting tokens
        text = text.replace('[1m', '').replace('[95m', '') \
                   .replace('[92m', '').replace('[00m', '')
        return text

    def write(self, text):
        cleaned_text = self.clean_text(text)
        if cleaned_text is None:
            return
        lines = cleaned_text.split('\n')
        new_lines = []
        for line in lines:
            line = line.strip()
            if line and line not in self.seen_lines:
                self.seen_lines.add(line)
                new_lines.append(line)
        if new_lines:
            new_content = '\n'.join(new_lines)
            self.output_text = f"{self.output_text}\n{new_content}" \
                if self.output_text else new_content
            self.container.text(self.output_text)
```

The `StreamlitProcessOutput` class replaces `sys.stdout` during agent execution. It cleans ANSI escape codes (terminal formatting), filters out LiteLLM debug messages, deduplicates repeated lines using a set, and updates the Streamlit container in real-time. The `capture_output` context manager wraps the research execution and guarantees stdout restoration via `try/finally`.

## 5.4 Code Structure Overview

The project follows a modular directory structure that separates the user interface, agent logic, utility functions, and output:

**[INSERT DIAGRAM: Figure 5.1 — Project Directory Structure]**

```
manual-literature-review/
│
├── app.py                              # Main Streamlit application entry point
├── requirements.txt                    # Python package dependencies
│
├── source/
│   ├── components/
│   │   ├── researcher.py              # CrewAI agent, custom tools (EXA + PDF),
│   │   │                              # task definition, and crew execution
│   │   └── sidebar.py                 # Sidebar UI: provider/model selection,
│   │                                  # API key management, Ollama discovery
│   └── utils/
│       └── output_handler.py          # Real-time stdout capture for Streamlit
│                                      # display with ANSI cleaning
│
├── output/
│   └── research_report.md             # Auto-generated research report (latest run)
│
└── .streamlit/                        # Streamlit configuration directory
```

The major source files and their roles are:

| File | Location | Responsibility |
|------|----------|---------------|
| **`app.py`** | Root | Main entry point; page config, input handling, PDF upload, workflow orchestration, result display |
| **`researcher.py`** | `source/components/` | Defines the AI agent (role, goal, backstory), two custom tools (PDFAnalysisTool, EXAAnswerTool), task with 7-section output template, and crew execution |
| **`sidebar.py`** | `source/components/` | Sidebar UI rendering; LLM provider selection, model dropdown, Ollama model discovery, API key inputs |
| **`output_handler.py`** | `source/utils/` | Custom stdout handler class for real-time agent output display; ANSI cleaning, deduplication, context manager |
| **`requirements.txt`** | Root | Lists all Python dependencies: crewai, crewai-tools, streamlit, exa-py, requests, pysqlite3-binary, PyMuPDF, pypdf |
