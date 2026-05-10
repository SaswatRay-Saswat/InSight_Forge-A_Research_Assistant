# Chapter 3 – System Analysis and Requirements

## 3.1 Introduction

System analysis is a crucial phase in software development that involves examining the existing environment, identifying system requirements, and evaluating the feasibility of the proposed solution. It serves as a bridge between conceptual understanding and practical implementation by defining what the system should accomplish and how it should function.

This chapter analyzes the current research landscape (summarized from the literature review in Chapter 2), presents the proposed system, defines detailed functional and non-functional requirements, and conducts a feasibility study from technical, economic, and operational perspectives.

## 3.2 Proposed System

As discussed in Chapter 2, existing research tools suffer from fragmented workflows, lack of intelligent task orchestration, inability to combine multiple data sources, and limited structured report generation. The proposed system, "InSight Forge: An AI-Based Research Assistant," addresses these gaps by providing a unified platform that automates the end-to-end research workflow.

The core of the proposed system is an AI-driven research agent, implemented using CrewAI [5], capable of understanding user queries in natural language and performing multi-step tasks autonomously. Upon receiving a query, the agent intelligently determines the appropriate actions — retrieving information from the web using Exa AI [6], analyzing user-uploaded PDF documents using pypdf [9], or both — and synthesizes the results into a structured research report.

Key differentiators of the proposed system include:
- **Unified platform** integrating web search, PDF analysis, and report generation
- **Autonomous agent-based workflow** that requires no manual prompt engineering
- **Multiple LLM provider support** (OpenAI, GROQ, Ollama) for flexibility
- **Consistent 7-section report template** enforced automatically
- **Real-time progress transparency** showing the agent's reasoning process
- **Fully local operation** capability via Ollama for privacy-sensitive research

## 3.3 Functional Requirements

Functional requirements define the specific operations the system must perform. Each requirement is identified with a unique ID and priority level.

**Table 3.1: Functional Requirements**

| ID | Requirement | Description | Priority |
|---|---|---|---|
| FR-01 | Research Query Input | The system shall provide a text input area for users to enter research queries in natural language | High |
| FR-02 | PDF Upload and Processing | The system shall allow users to upload one or more PDF files; text shall be extracted using pypdf and truncated to 12,000 characters per file | High |
| FR-03 | Multi-Provider LLM Support | The system shall support three LLM providers: OpenAI, GROQ, and Ollama, with provider-specific model selection | High |
| FR-04 | Web Research via Exa AI | The system shall use the Exa AI answer API to retrieve web-based research with citations (title + URL); web search shall be disabled when using Ollama | High |
| FR-05 | Structured Report Generation | The system shall generate research reports following a consistent 7-section Markdown template (Executive Summary, Key Findings, In-Depth Analysis, Research Gaps, Critique, Conclusion, Sources) | High |
| FR-06 | Real-Time Progress Display | The system shall display the AI agent's thought process and actions in real-time during research execution, with ANSI codes and debug messages filtered | Medium |
| FR-07 | Report Download | The system shall provide a download button to save the generated report as a Markdown (.md) file | Medium |
| FR-08 | Sidebar Configuration | The system shall provide a sidebar with model selection (radio + dropdown), API key inputs (password-masked), and an about section | High |
| FR-09 | Custom Model Input | The system shall allow users to enter a custom model string when "Custom" is selected from the OpenAI or GROQ model dropdown | Low |
| FR-10 | Ollama Model Discovery | The system shall automatically query the local Ollama instance to discover and list available models; a warning shall be displayed if Ollama is unreachable | Medium |
| FR-11 | API Key Validation | The system shall verify that required API keys are provided before allowing research to begin; warnings shall be displayed for missing keys | High |
| FR-12 | Error Handling | The system shall handle errors during research execution gracefully, displaying error messages without crashing the application | High |

## 3.4 Non-Functional Requirements

Non-functional requirements define the quality attributes and performance characteristics of the system.

**Table 3.2: Non-Functional Requirements**

| ID | Category | Requirement |
|---|---|---|
| NFR-01 | Performance | Application startup time shall be less than 5 seconds |
| NFR-02 | Performance | Sidebar rendering and interaction shall respond within 1 second |
| NFR-03 | Performance | PDF text extraction shall complete within 3 seconds per standard-length paper |
| NFR-04 | Performance | Real-time output display shall update within 500ms of new agent output |
| NFR-05 | Usability | The interface shall follow a clean, centered layout with clear visual hierarchy |
| NFR-06 | Usability | All API key inputs shall be password-masked for privacy |
| NFR-07 | Usability | Users shall complete a full research workflow in 5 or fewer interactions |
| NFR-08 | Security | API keys shall be stored in-memory only as environment variables; they shall not be persisted to disk |
| NFR-09 | Security | API key input fields shall use password masking |
| NFR-10 | Reliability | The system shall handle API failures, invalid inputs, and missing configurations without crashing |
| NFR-11 | Reliability | The stdout capture mechanism shall always restore original stdout, even on exceptions |
| NFR-12 | Compatibility | The application shall be compatible with Python 3.10 and above |
| NFR-13 | Compatibility | The application shall work in Chrome, Firefox, Safari, and Edge browsers |
| NFR-14 | Scalability | The system shall support uploading and analyzing multiple PDF files in a single research session |

## 3.5 Feasibility Study

A feasibility study evaluates the practicality of the proposed system across three dimensions: technical, economic, and operational.

### 3.5.1 Technical Feasibility

Technical feasibility assesses whether the required technologies and tools are available for development and deployment.

The implementation is based on widely used and well-supported technologies:
- **Python 3.10+** [13] provides the core runtime with extensive AI/NLP library support
- **Streamlit** [7] enables rapid web application development directly from Python
- **CrewAI** [5] provides the agent orchestration framework with built-in support for custom tools and sequential task execution
- **Exa AI** [6] offers a neural search API with citation support accessible via REST endpoints
- **pypdf** [9] provides reliable PDF text extraction
- **Ollama** [8] enables local LLM deployment for offline operation

All technologies are open-source or provide free-tier API access, are compatible with each other, and have active community support. The modular design ensures that components can be developed, tested, and maintained independently.

**Verdict:** The proposed system is **technically feasible**.

### 3.5.2 Economic Feasibility

Economic feasibility evaluates the cost-effectiveness of development and operation.

**Development Costs:**
- All frameworks and libraries used (Python, Streamlit, CrewAI, pypdf, Pydantic) are open-source and freely available
- No licensing fees are required for development

**Operational Costs:**

| Scenario | Cost |
|----------|------|
| Using GROQ free tier + Exa free tier | $0 |
| Using Ollama (local models) | $0 (runs on user's hardware) |
| Using OpenAI API (pay-per-use) | Typically < $0.10 per research report |
| Using OpenAI API (heavy usage) | Variable, based on token consumption |

Compared to subscription-based alternatives like ChatGPT Plus ($20/month) or Gemini Advanced ($20/month), InSight Forge offers significantly lower operational costs, especially for users who choose GROQ's free tier or Ollama's local models.

**Verdict:** The proposed system is **economically feasible**.

### 3.5.3 Operational Feasibility

Operational feasibility examines how well the system fits within the intended user environment.

The system is designed with a simple web-based interface that requires no specialized technical knowledge to operate. Users interact through intuitive elements: text areas for queries, file uploaders for PDFs, radio buttons for provider selection, and dropdowns for model selection. The integration of all functionalities into a single platform eliminates the need to switch between multiple tools.

Installation requires standard Python development tools (Python, pip, virtual environment), and the system can be launched with a single command (`streamlit run app.py`). The system's support for Ollama enables use in environments without internet access.

**Verdict:** The proposed system is **operationally feasible**.

**Table 3.3: Feasibility Study Summary**

| Dimension | Key Factors | Verdict |
|-----------|------------|---------|
| **Technical** | All technologies are open-source, compatible, and well-supported; modular design enables independent development | ✅ Feasible |
| **Economic** | Zero development cost; operational cost ranges from $0 (GROQ/Ollama) to < $0.10/report (OpenAI) | ✅ Feasible |
| **Operational** | Simple web interface; single-command launch; offline capability via Ollama; no specialized training required | ✅ Feasible |
