# Chapter 4 – System Design

## 4.1 Introduction

System design is a critical phase in the software development lifecycle that transforms the requirements identified during system analysis into a structured and implementable solution. It provides a detailed blueprint of the system, defining its architecture, components, data flow, and interactions among various modules. The objective of system design is to ensure that the proposed system is organized in a manner that is efficient, scalable, and capable of meeting the defined functional and non-functional requirements.

In the context of "InSight Forge," the system design phase involves formulating an architecture that integrates multiple technologies into a cohesive research automation framework. The system combines user interaction, intelligent agent-based processing, tool integration, and output generation within a unified platform. This requires careful consideration of how different components interact and how data flows through the system.

The proposed system follows a modular design approach, where the overall functionality is divided into distinct modules: user interface, sidebar configuration, research agent with integrated tools, output handling, and external service communication. Each module is responsible for a specific set of tasks and communicates with other modules to achieve the system's overall objective. This modularity enhances maintainability, flexibility, and ease of future expansion.

A key aspect of the design is the incorporation of an agent-based architecture using the CrewAI framework [5]. The research agent acts as the central component that coordinates task execution, selects appropriate tools, and manages information flow. The system also integrates Large Language Models (LLMs) such as GPT-4 [3] and open-source models like LLaMA [14] running on Ollama [8], which provide the intelligence for natural language understanding and content generation.

This chapter presents the detailed design of the proposed system, including its architecture, module descriptions, data flow diagrams, UML diagrams, system flowchart, and data storage design.

## 4.2 System Architecture

The system architecture of "InSight Forge" defines the structural framework through which various components interact to perform the research workflow. The architecture supports an end-to-end automated pipeline, starting from user input and culminating in the generation of a structured research report.

### 4.2.1 Architecture Overview and Working Principle

The system follows a **layered and modular architecture** consisting of three primary layers: the Presentation Layer, the Business Logic Layer, and the External Services Layer. These layers work together in a coordinated manner to process user queries and generate meaningful results.

**[INSERT DIAGRAM: Figure 4.1 — System Architecture]**

*Figure 4.1: Three-Layer System Architecture of InSight Forge*

```
Mermaid code for Figure 4.1 is available in diagrams/all_mermaid_diagrams.md
```

The workflow proceeds as follows:

1. The **User** enters a research query and optionally uploads PDF documents through the interface.
2. The **Presentation Layer** (Streamlit) captures the input, validates API key configuration, and forwards the data to the Business Logic Layer.
3. The **Business Logic Layer** (CrewAI Engine) creates the research agent, which autonomously determines the required actions — invoking web search via Exa AI, reading uploaded PDFs, or both.
4. The **External Services Layer** provides data through the Exa AI API (web research with citations) and LLM providers (OpenAI, GROQ, or Ollama) for language understanding and report generation.
5. The agent synthesizes all gathered information and generates a structured research report.
6. The **Output Handler** captures the agent's real-time progress and displays it in the Streamlit interface.
7. The final report is displayed to the user and made available for download in Markdown format.

The three architectural layers and their responsibilities are:

| Layer | Responsibility | Components |
|-------|---------------|------------|
| **Presentation Layer** | User interaction, configuration, input/output display | `app.py`, `sidebar.py` (Streamlit) |
| **Business Logic Layer** | AI agent orchestration, tool execution, output capture | `researcher.py`, `output_handler.py` (CrewAI) |
| **External Services Layer** | Data retrieval, LLM inference | Exa AI API, OpenAI API, GROQ API, Ollama |

### 4.2.2 Key Characteristics of the Architecture

- **Modularity:** Each component operates independently, facilitating easy maintenance and upgrades.
- **Scalability:** The architecture supports the addition of new tools, agents, and LLM providers.
- **Flexibility:** Multiple AI models can be integrated and configured at runtime through the sidebar.
- **Automation:** The agent-based design minimizes manual intervention by autonomously orchestrating the research workflow.

## 4.3 Module Description

The system is designed using a modular approach, where the overall functionality is divided into distinct modules. Each module performs specific tasks and interacts with other modules to achieve the complete research workflow. The major modules are described below.

### 4.3.1 Application Controller Module (`app.py`)

The Application Controller Module serves as the main entry point of the system. It is implemented using Streamlit [7] and acts as the orchestrator that connects all other modules.

The primary responsibilities of this module include:

- Configuring the Streamlit page layout (title, icon, wide layout, expanded sidebar)
- Patching SQLite for ChromaDB compatibility (required internally by CrewAI)
- Rendering the sidebar via the Sidebar Configuration Module and validating API keys
- Providing a text input area for research queries and a multi-file PDF uploader
- Saving uploaded PDFs to temporary files and appending their paths to the task description
- Initiating the research workflow by creating the agent, task, and crew
- Capturing and displaying the agent's real-time output using the Output Handler Module
- Displaying the final Markdown report and providing a download button

This module ensures seamless communication between the user interface and the backend processing components.

### 4.3.2 Sidebar Configuration Module (`sidebar.py`)

The Sidebar Configuration Module manages all user configuration settings through an interactive sidebar panel. It handles:

- **LLM Provider Selection:** Radio buttons for choosing between OpenAI, GROQ, and Ollama
- **Model Selection:** Provider-specific dropdowns with preset models and a custom model input option
- **Ollama Model Discovery:** Automatically queries the local Ollama instance at `http://localhost:11434/api/tags` to detect available models. Returns an empty list gracefully if Ollama is not running
- **API Key Management:** Password-masked input fields for OpenAI, GROQ, and Exa API keys. Keys are stored as environment variables (`os.environ`) for the session duration only — never written to disk
- **About Section:** Expandable panel with usage guidance

The module returns a dictionary containing the selected provider and model, which is used by the Research Agent Module for LLM configuration.

### 4.3.3 Research Agent Module (`researcher.py`)

The Research Agent Module is the core intelligence component of the system. It is implemented using the CrewAI framework [5] and contains the AI agent definition, custom tool implementations, task configuration, and crew execution logic.

This module contains two custom tool classes and three orchestration functions:

**Custom Tools:**

- **PDFAnalysisTool:** A custom tool extending CrewAI's `BaseTool` class that reads uploaded PDF files using `pypdf` [9] and extracts text content, truncated to 12,000 characters to stay within LLM context limits.
- **EXAAnswerTool:** A custom tool extending `BaseTool` that sends natural-language queries to the Exa AI answer API (`https://api.exa.ai/answer`) [6] and returns answers with numbered citations (title + URL). Input validation is handled using a Pydantic schema (`EXAAnswerToolSchema`) [10].

**Orchestration Functions:**

- **`create_researcher(selection)`:** Creates and configures the CrewAI Agent with the role "Senior Academic Research Analyst," equipped with both tools, and connected to the selected LLM provider.
- **`create_research_task(researcher, task_description)`:** Defines a structured research task with a detailed expected output template specifying seven report sections (Executive Summary, Key Findings, In-Depth Analysis, Research Gaps, Critique, Conclusion, Sources).
- **`run_research(researcher, task)`:** Assembles a CrewAI Crew with the agent and task, then executes the sequential research workflow.

### 4.3.4 Output Handler Module (`output_handler.py`)

The Output Handler Module provides real-time visibility into the AI agent's processing. It implements a custom `sys.stdout` replacement that captures the CrewAI agent's verbose output and displays it in a scrollable Streamlit container.

The key components include:

- **`StreamlitProcessOutput` class:** Replaces `sys.stdout` during research execution. It cleans ANSI escape codes and LiteLLM debug messages from the output, deduplicates repeated lines, and updates the Streamlit display in real-time.
- **`capture_output(container)` context manager:** A Python context manager that temporarily redirects stdout to the custom handler and guarantees restoration of the original stdout using `try/finally`, even if exceptions occur.

This module ensures that users can observe the agent's reasoning process, tool invocations, and synthesis steps as they happen.

### 4.3.5 External Service Integration

The system integrates with multiple external services through the Tool Integration and LLM Processing layers:

**Web Search Integration (Exa AI):**
- The EXAAnswerTool sends HTTP POST requests to `https://api.exa.ai/answer` with the research query
- Authentication uses the `x-api-key` header with the user-provided Exa API key
- Responses include answer text and citations with titles and URLs
- Web search is effectively disabled for Ollama users since the Exa API key input is hidden when Ollama is selected

**LLM Provider Integration:**
- **OpenAI:** Configured via `LLM(api_key=OPENAI_API_KEY, model="openai/{model}")` with model name normalization
- **GROQ:** Configured via `LLM(api_key=GROQ_API_KEY, model="groq/{model}")`
- **Ollama:** Configured via `LLM(base_url="http://localhost:11434", model="ollama/{model}")` for fully local inference

## 4.4 Data Flow Diagrams (DFD)

A Data Flow Diagram (DFD) is a graphical representation that illustrates the flow of data within a system. It shows how data moves between processes, data stores, and external entities. In InSight Forge, the DFDs represent the movement of user queries, uploaded documents, processed information, and generated outputs across different components.

### 4.4.1 Level 0 DFD (Context Diagram)

The Level 0 DFD, also known as the Context Diagram, provides a high-level overview of the entire system and its interaction with external entities. It treats InSight Forge as a single process.

**[INSERT DIAGRAM: Figure 4.2 — Level 0 DFD (Context Diagram)]**

*Figure 4.2: Level 0 Data Flow Diagram — Context Diagram*

```
Mermaid code for Figure 4.2 is available in diagrams/all_mermaid_diagrams.md
```

**Explanation:**

In this diagram:
- The **User** provides: a research query (text), optional PDF documents, model/provider selection, and API keys.
- The **InSight Forge System** processes all inputs using the research agent, integrated tools, and language models.
- The system produces: a structured research report (displayed and saved as Markdown), real-time agent progress logs, and warning/error messages for invalid configurations.
- **External APIs** (Exa AI, OpenAI, GROQ) provide web research data and LLM inference services.
- **Ollama** (local) provides local LLM inference without internet dependency.

### 4.4.2 Level 1 DFD

The Level 1 DFD decomposes the system into its internal processes, showing how data flows between the major modules.

**[INSERT DIAGRAM: Figure 4.3 — Level 1 DFD]**

*Figure 4.3: Level 1 Data Flow Diagram*

```
Mermaid code for Figure 4.3 is available in diagrams/all_mermaid_diagrams.md
```

**Explanation:**

The Level 1 DFD consists of five interconnected processes:

1. **P1 — User Interface Processing (`app.py` + `sidebar.py`):**
   The user enters a research query and optionally uploads PDF files through the Streamlit interface. The sidebar captures provider selection, model choice, and API keys. Validation checks ensure required API keys are present before proceeding. Uploaded PDFs are saved to temporary files.

2. **P2 — Research Agent Processing (`researcher.py`):**
   The research agent is created with the selected LLM configuration and equipped with the EXA Answer Tool and PDF Analysis Tool. A structured research task is defined with the expected 7-section output template. The agent interprets the query and autonomously decides which tools to invoke.

3. **P3 — Tool Execution:**
   - **P3a — Web Search (EXAAnswerTool):** Sends the query to the Exa AI API and retrieves answers with citations.
   - **P3b — PDF Processing (PDFAnalysisTool):** Reads uploaded PDF files using pypdf and extracts text content (truncated to 12,000 characters per file).

4. **P4 — LLM Processing:**
   The selected LLM (OpenAI, GROQ, or Ollama) processes the combined information — user query, web search results, and extracted PDF content — to synthesize a structured research report following the defined template.

5. **P5 — Output Generation:**
   The generated report is saved to `output/research_report.md`, displayed as rendered Markdown in the Streamlit interface, and made available for download. Simultaneously, the Output Handler captures and displays the agent's real-time progress in a scrollable container.

**Data Stores:**
- **D1 — Temporary PDF Storage:** OS temp directory where uploaded PDFs are saved
- **D2 — Report File:** `output/research_report.md` (overwritten each run)
- **D3 — Environment Variables:** In-memory API key storage (`os.environ`)

## 4.5 UML Diagrams

Unified Modeling Language (UML) diagrams provide standardized visual representations of a system's structure and behavior. For InSight Forge, three UML diagrams are presented: a Use Case Diagram, a Sequence Diagram, and a Class Diagram.

### 4.5.1 Use Case Diagram

The Use Case Diagram illustrates the interactions between the user (actor) and the system's functionalities.

**[INSERT DIAGRAM: Figure 4.4 — Use Case Diagram]**

*Figure 4.4: Use Case Diagram of InSight Forge*

```
Mermaid code for Figure 4.4 is available in diagrams/all_mermaid_diagrams.md
```

**Explanation:**

The diagram shows a single primary actor — the **User** — who interacts with the following use cases:

| Use Case | Description |
|----------|-------------|
| **Select LLM Provider** | User chooses between OpenAI, GROQ, or Ollama |
| **Select Model** | User picks a specific model or enters a custom model string |
| **Enter API Keys** | User provides required API keys (OpenAI/GROQ + Exa) |
| **Enter Research Query** | User types a research topic or question |
| **Upload PDF Documents** | User optionally uploads one or more PDF research papers |
| **Start Research** | User initiates the automated research workflow |
| **View Real-Time Progress** | User observes the agent's live reasoning and tool usage |
| **View Research Report** | User reads the generated structured report |
| **Download Report** | User downloads the report as a Markdown file |

The use cases "View Real-Time Progress" and "View Research Report" are included by "Start Research" (they occur as part of the research execution). "Upload PDF Documents" is an optional extension of the research workflow.

### 4.5.2 Sequence Diagram

The Sequence Diagram illustrates the temporal flow of interactions between system components during a research operation.

**[INSERT DIAGRAM: Figure 4.5 — Sequence Diagram]**

*Figure 4.5: Sequence Diagram — Research Workflow Execution*

```
Mermaid code for Figure 4.5 is available in diagrams/all_mermaid_diagrams.md
```

**Explanation:**

The sequence diagram depicts the following interaction flow:

1. The **User** opens the application and configures the sidebar (provider, model, API keys).
2. The **User** enters a research query and optionally uploads PDF documents, then clicks "Start Research."
3. **`app.py`** validates that required API keys are present. If missing, a warning is displayed and execution stops.
4. **`app.py`** saves uploaded PDFs to temporary files and appends their paths to the task description.
5. **`app.py`** calls `create_researcher(selection)` in **`researcher.py`**, which initializes the LLM connection and creates the Agent instance.
6. **`app.py`** calls `create_research_task(agent, query)` to define the structured task.
7. **`app.py`** activates `capture_output(container)` from **`output_handler.py`** to redirect stdout.
8. **`app.py`** calls `run_research(agent, task)`, which assembles the Crew and calls `crew.kickoff()`.
9. The **CrewAI Engine** sends the research prompt to the selected **LLM Provider**.
10. The **LLM** determines which tools to use and requests tool invocations.
11. The **EXAAnswerTool** queries the **Exa AI API** and returns answers with citations.
12. The **PDFAnalysisTool** reads the uploaded **PDF files** and returns extracted text.
13. The **LLM** synthesizes all gathered information into a structured report.
14. The **CrewAI Engine** returns the `CrewOutput` to `app.py`.
15. **`output_handler.py`** restores the original stdout.
16. **`app.py`** displays the rendered Markdown report and provides a download button.

### 4.5.3 Class Diagram

The Class Diagram shows the static structure of the system's custom classes and their relationships.

**[INSERT DIAGRAM: Figure 4.6 — Class Diagram]**

*Figure 4.6: Class Diagram of InSight Forge Custom Components*

```
Mermaid code for Figure 4.6 is available in diagrams/all_mermaid_diagrams.md
```

**Explanation:**

The system defines four custom classes:

| Class | Base Class | Purpose |
|-------|-----------|---------|
| **PDFAnalysisTool** | `crewai.tools.BaseTool` | Extracts text from uploaded PDF files |
| **EXAAnswerToolSchema** | `pydantic.BaseModel` | Input validation schema for the Exa tool (requires `query` field) |
| **EXAAnswerTool** | `crewai.tools.BaseTool` | Queries Exa AI for web research with citations |
| **StreamlitProcessOutput** | (none) | Custom stdout replacement for real-time output display |

**Key relationships:**
- `PDFAnalysisTool` and `EXAAnswerTool` both inherit from `BaseTool`
- `EXAAnswerTool` uses `EXAAnswerToolSchema` for input validation
- `EXAAnswerToolSchema` inherits from `BaseModel` (Pydantic) [10]
- `StreamlitProcessOutput` is an independent class that replaces `sys.stdout`

## 4.6 System Flowchart

The system flowchart provides a step-by-step visualization of the complete research workflow, from user input to report generation.

**[INSERT DIAGRAM: Figure 4.7 — System Flowchart]**

*Figure 4.7: System Flowchart of InSight Forge*

```
Mermaid code for Figure 4.7 is available in diagrams/all_mermaid_diagrams.md
```

**Explanation:**

The flowchart illustrates the following process:

1. **Start** — The user launches the application.
2. **Configure Settings** — The user selects an LLM provider and model via the sidebar.
3. **API Keys Provided?** — The system checks whether required API keys are entered.
   - If **No** → Display warning message → Return to configuration.
   - If **Yes** → Proceed.
4. **Enter Research Query** — The user types the research topic.
5. **Upload PDFs?** — Decision point:
   - If **Yes** → Save PDFs to temporary files → Append paths to task description.
   - If **No** → Skip PDF processing.
6. **Click "Start Research"** — The user initiates the workflow.
7. **Create Research Agent** — The system initializes the CrewAI agent with the selected LLM and tools.
8. **Create Research Task** — A structured task is defined with the 7-section expected output template.
9. **Execute Research Crew** — The CrewAI crew runs the sequential research process.
10. **Agent Decides Tool Usage** — The agent autonomously selects tools:
    - **Web Search needed?** → Invoke EXAAnswerTool → Retrieve answers + citations from Exa AI.
    - **PDF Analysis needed?** → Invoke PDFAnalysisTool → Extract text from uploaded PDFs.
11. **LLM Synthesizes Information** — The selected LLM processes all gathered data and generates the structured report.
12. **Display Report** — The Markdown report is rendered in the Streamlit interface.
13. **Save Report** — The report is saved to `output/research_report.md`.
14. **Download Available** — The user can download the report as a `.md` file.
15. **End.**

## 4.7 Data Storage Design

InSight Forge is designed as a lightweight application with **no database**. All data is either transient or file-based. This design decision was made to keep the system simple, privacy-focused, and easy to deploy.

| Data | Storage Type | Location | Lifecycle |
|------|-------------|----------|-----------|
| **API Keys** | Environment variables (in-memory) | `os.environ` | Session-scoped; cleared when the Streamlit server restarts |
| **Uploaded PDFs** | Temporary files | OS temp directory (`tempfile.NamedTemporaryFile`) | Created per research run; not explicitly cleaned up |
| **Research Report** | File | `output/research_report.md` | Persistent; overwritten on each new research run |
| **Agent Output** | In-memory string | `StreamlitProcessOutput.output_text` | Session-scoped; lost on page refresh |
| **User Selections** | Streamlit session state | In-memory | Session-scoped |

**Key design decisions:**

- **No persistent database:** The system processes data in a stateless manner. Each research run is independent — there is no session history, user accounts, or saved research projects.
- **API keys in-memory only:** Keys are set as `os.environ` variables and are never written to disk, ensuring user privacy and security.
- **Report overwrite:** Each research run overwrites the previous `research_report.md`. There is no built-in versioning or history — this is identified as a limitation and addressed in future enhancements.
- **Temporary PDF files:** Uploaded PDFs are saved using Python's `tempfile` module with `delete=False` to persist across Streamlit's re-execution model. They are not explicitly cleaned up after the research run.
