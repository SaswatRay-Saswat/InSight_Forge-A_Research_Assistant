# Chapter 5 – Implementation

Implementation is the phase of software development in which the system design is transformed into a working application through coding, integration, and deployment of various components. In "InSight Forge," the implementation phase focuses on developing an intelligent research automation system capable of processing user queries, analyzing documents, retrieving information from online sources, and generating structured research reports. The system integrates multiple technologies and tools to create a unified research workflow.

## 5.1 Technologies Used

The development of "InSight Forge" involves the integration of various technologies, frameworks, and libraries. Each technology serves a specific purpose in the research workflow. The selection of these technologies is based on factors such as flexibility, ease of integration, scalability, and support for Artificial Intelligence applications.

The major technologies used in the implementation are:

- **Python [13]:**
  Python is used as the primary programming language for the entire system. It was chosen for its extensive ecosystem of AI and NLP libraries, simple syntax for rapid prototyping, and strong community support. All backend logic, tool integration, and processing modules are implemented in Python.

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

The implementation of InSight Forge follows a modular structure where each component is responsible for a specific functionality. The system is implemented using Python and integrates multiple modules to create a seamless workflow that accepts user input, processes information using integrated tools and language models, and generates structured research reports in Markdown (.md) format. The major implementation aspects are described below.

### 5.3.1 Application Entry Point and Configuration (`app.py`)

The `app.py` file serves as the main entry point and central coordinator of the system. At startup, the application performs an SQLite compatibility patch, replacing the default `sqlite3` module with `pysqlite3-binary` to ensure ChromaDB (used internally by CrewAI) functions correctly. This patch is wrapped in a try-except block so the application continues to work even if the library is not installed.

The Streamlit page is configured with a wide layout, expanded sidebar, and a custom title ("InSight Forge") and icon. The application then renders the sidebar configuration panel and captures the user's provider and model selection.

The main interface provides a text input area for research queries and a multi-file PDF uploader that accepts `.pdf` files. When the user uploads PDF documents, each file is saved to a temporary location using Python's `tempfile` module with `delete=False` to ensure the files persist across Streamlit's re-execution model. The temporary file paths are appended to the task description so that the research agent knows which documents to analyze.

Before starting the research workflow, the application validates that the required API keys have been entered. If any required key is missing, a warning message is displayed and the research process is blocked. Once validation passes, the application creates the research agent, defines the research task, activates the real-time output capture mechanism, and initiates the research workflow. Upon completion, the generated Markdown report is rendered in the interface and a download button is provided.

### 5.3.2 Sidebar Configuration (`sidebar.py`)

The sidebar configuration module manages all user settings through an interactive sidebar panel. It provides radio buttons for selecting the LLM provider (OpenAI, GROQ, or Ollama) and a dropdown menu for choosing a specific model from the selected provider's available options. Each provider has a preset list of supported models, along with a "Custom" option that allows users to enter any model string manually.

A key feature of this module is the dynamic Ollama model discovery functionality. When Ollama is selected as the provider, the system sends an HTTP GET request to the local Ollama instance at `http://localhost:11434/api/tags` to retrieve the list of installed models. If Ollama is not running or is unreachable, the function returns an empty list gracefully and displays a warning message, ensuring the application remains functional regardless of Ollama's availability.

The sidebar also provides password-masked input fields for API keys. For OpenAI, both an OpenAI API key and an Exa API key are required. For GROQ, a GROQ API key and an Exa API key are needed. For Ollama, no API keys are required since inference runs locally. The entered API keys are stored as environment variables (`os.environ`) for the session duration only and are never written to disk, ensuring user privacy and security.

The module returns a dictionary containing the selected provider and model, which is used by the Research Agent Module for LLM configuration.

### 5.3.3 PDF Analysis Tool (`researcher.py`)

The PDF analysis functionality is implemented as a custom tool class named `PDFAnalysisTool`, which extends CrewAI's `BaseTool` class. This tool is responsible for reading uploaded PDF files and extracting their textual content for analysis by the research agent.

When invoked, the tool receives the file path of the uploaded PDF and uses the `pypdf` library's `PdfReader` to open the file. It iterates through all pages of the document, extracting text from each page and concatenating the results. The extracted text is truncated to 12,000 characters to stay within the context window limits of smaller language models. This truncation ensures compatibility across all supported LLM providers, including less capable local models running on Ollama.

Error handling is built into the tool to manage scenarios such as corrupted files, password-protected PDFs, or invalid file paths. In case of any error during PDF reading, the tool returns a descriptive error message rather than crashing the application.

### 5.3.4 Web Search Tool (`researcher.py`)

The web search functionality is implemented as a custom tool class named `EXAAnswerTool`, also extending CrewAI's `BaseTool` class. This tool enables the research agent to retrieve relevant information from online sources using the Exa AI answer API.

Input validation for this tool is handled through a Pydantic schema class (`EXAAnswerToolSchema`), which ensures that a valid query string is provided before the API call is made. When the tool is invoked, it constructs an HTTP POST request to the Exa AI answer endpoint (`https://api.exa.ai/answer`) with the research query. Authentication is performed using the `x-api-key` header with the Exa API key retrieved from the environment variables.

The API response contains a synthesized answer along with a list of citations, each including a title and URL. The tool formats this response into a structured output string containing the answer text followed by a numbered list of citations. This ensures that every piece of information retrieved is traceable to a verifiable source, maintaining citation integrity in the generated research reports.

### 5.3.5 Research Agent and Task Configuration (`researcher.py`)

The core intelligence of the system is implemented through three orchestration functions in the researcher module.

The `create_researcher` function creates and configures the CrewAI Agent instance. The agent is assigned the role of "Senior Academic Research Analyst" with a detailed goal and backstory that guide its behavior during research. The function dynamically configures the LLM connection based on the user's provider selection — using the OpenAI API key and model format for OpenAI, the GROQ API key and format for GROQ, or the local Ollama base URL for local models. Both the EXAAnswerTool and PDFAnalysisTool are attached to the agent, enabling it to autonomously decide which tools to use based on the research context. The agent is configured with `verbose=True` for real-time output and `allow_delegation=False` for a single-agent architecture.

The `create_research_task` function defines a structured research task with a detailed expected output template. This template specifies seven sections that every generated report must follow: Executive Summary, Key Findings, In-Depth Analysis, Research Gaps and Future Directions, Critique and Comparison of Research, Conclusion, and Sources. By enforcing this template, the system ensures consistent, well-organized reports regardless of the research topic or selected model.

The `run_research` function assembles a CrewAI Crew with the configured agent and task, then executes the research workflow by calling `crew.kickoff()`. The crew is configured for sequential processing, and the output is saved to `output/research_report.md`.

### 5.3.6 Real-Time Output Handling (`output_handler.py`)

The output handler module provides real-time visibility into the AI agent's processing through a custom stdout replacement mechanism. This is one of the most technically significant components of the system, as it enables users to observe the agent's reasoning process as it happens.

The module implements a `StreamlitProcessOutput` class that temporarily replaces `sys.stdout` during research execution. When the CrewAI agent generates output (including reasoning traces, tool invocation logs, and synthesis steps), this custom handler intercepts the text, processes it, and displays it in a scrollable Streamlit container.

The processing pipeline includes several cleaning steps. ANSI escape codes (terminal formatting characters) are removed using regular expression matching. LiteLLM debug messages and provider list logs are filtered out to reduce noise. Residual formatting tokens are stripped. A deduplication mechanism using a set data structure prevents the same line from appearing multiple times in the display.

The module also provides a `capture_output` context manager that wraps the research execution. This context manager redirects stdout to the custom handler at the start of research and guarantees restoration of the original stdout using a `try/finally` block, even if exceptions occur during research execution. This ensures the application's standard output behavior is never permanently altered.

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
