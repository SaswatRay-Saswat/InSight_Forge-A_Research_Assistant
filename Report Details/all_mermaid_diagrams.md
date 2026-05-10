# All Mermaid Diagrams — InSight Forge Report

Render each diagram at [mermaid.live](https://mermaid.live), export as PNG/SVG, and insert into your Word/Google Docs report at the corresponding `[INSERT DIAGRAM]` placeholder.

---

## Figure 1.1 — Traditional vs AI-Assisted Research Workflow (Chapter 1)

```mermaid
graph LR
    subgraph Traditional["Traditional Research Workflow"]
        direction TB
        T1["Search Multiple Platforms"] --> T2["Read Documents Manually"]
        T2 --> T3["Extract Key Information"]
        T3 --> T4["Compare & Analyze Sources"]
        T4 --> T5["Organize Findings"]
        T5 --> T6["Write Structured Report"]
    end

    subgraph AI["InSight Forge Workflow"]
        direction TB
        A1["Enter Research Query"] --> A2["Upload PDFs (Optional)"]
        A2 --> A3["AI Agent Searches Web + Reads PDFs"]
        A3 --> A4["LLM Synthesizes All Sources"]
        A4 --> A5["Structured Report Generated Automatically"]
    end

    Traditional ~~~ AI

    style Traditional fill:#ffcccc,stroke:#cc0000,color:#000
    style AI fill:#ccffcc,stroke:#00cc00,color:#000
```

**Note:** If Mermaid side-by-side doesn't render well, draw this as two parallel vertical flowcharts in draw.io: left side (red-tinted, 6 manual steps) and right side (green-tinted, 5 automated steps) with a "Weeks" label on left and "Minutes" label on right.

---

## Figure 4.1 — System Architecture (Chapter 4, §4.2)

```mermaid
graph TB
    subgraph PL["PRESENTATION LAYER (Streamlit Web Application)"]
        SB["Sidebar<br/>(sidebar.py)<br/>Provider, Model, API Keys"]
        RI["Research Input<br/>(app.py)<br/>Query + PDF Upload"]
        RD["Results Display<br/>(app.py)<br/>Report + Download"]
    end

    subgraph BL["BUSINESS LOGIC LAYER (CrewAI Engine)"]
        AG["Senior Academic Research<br/>Analyst Agent<br/>(researcher.py)"]
        ET["EXA Answer<br/>Tool"]
        PT["PDF Analysis<br/>Tool"]
        OH["Output Handler<br/>(output_handler.py)"]
    end

    subgraph ES["EXTERNAL SERVICES"]
        EXA["Exa AI API"]
        OAI["OpenAI API"]
        GRQ["GROQ API"]
    end

    subgraph LR["LOCAL RESOURCES"]
        OLL["Ollama<br/>(localhost:11434)"]
        PDF["PDF Files<br/>(Uploaded)"]
        FS["File System<br/>(output/)"]
    end

    PL --> BL
    AG --> ET
    AG --> PT
    ET --> EXA
    PT --> PDF
    AG --> OAI
    AG --> GRQ
    AG --> OLL
    BL --> FS
    OH --> PL

    style PL fill:#e3f2fd,stroke:#1565c0,color:#000
    style BL fill:#fff3e0,stroke:#e65100,color:#000
    style ES fill:#fce4ec,stroke:#c62828,color:#000
    style LR fill:#e8f5e9,stroke:#2e7d32,color:#000
```

---

## Figure 4.2 — Level 0 DFD / Context Diagram (Chapter 4, §4.4.1)

```mermaid
graph LR
    U["👤 User"]
    S["InSight Forge<br/>System"]
    API["External APIs<br/>(Exa AI, OpenAI, GROQ)"]
    OLL["Ollama<br/>(Local)"]

    U -->|"Research Query<br/>PDF Documents<br/>Provider/Model Selection<br/>API Keys"| S
    S -->|"Structured Research Report<br/>Real-Time Progress Logs<br/>Warning Messages"| U
    S -->|"Search Queries<br/>LLM Prompts"| API
    API -->|"Answers + Citations<br/>Generated Text"| S
    S -->|"LLM Prompts"| OLL
    OLL -->|"Generated Text"| S
```

---

## Figure 4.3 — Level 1 DFD (Chapter 4, §4.4.2)

```mermaid
graph TB
    U["👤 User"]

    U -->|"Query, PDFs,<br/>Config, API Keys"| P1

    subgraph P1["P1: User Interface Processing<br/>(app.py + sidebar.py)"]
        direction LR
        P1a["Validate Config"]
        P1b["Save PDFs to Temp"]
    end

    P1 -->|"Selection dict +<br/>Full Task Description"| P2

    subgraph P2["P2: Research Agent Processing<br/>(researcher.py)"]
        direction LR
        P2a["Create Agent"]
        P2b["Create Task"]
        P2c["Run Crew"]
    end

    P2 -->|"Tool Invocation<br/>Requests"| P3

    subgraph P3["P3: Tool Execution"]
        P3a["P3a: Web Search<br/>(EXAAnswerTool)"]
        P3b["P3b: PDF Processing<br/>(PDFAnalysisTool)"]
    end

    P3a -->|"Query"| EXA["Exa AI API"]
    EXA -->|"Answer + Citations"| P3a
    P3b -->|"Read"| D1["D1: Temp PDF Storage"]

    P3 -->|"Retrieved Data +<br/>Extracted Text"| P4

    subgraph P4["P4: LLM Processing"]
        direction LR
        P4a["Synthesize Sources"]
        P4b["Generate Report"]
    end

    P4 -->|"LLM Prompts"| LLM["LLM Provider<br/>(OpenAI / GROQ / Ollama)"]
    LLM -->|"Generated Content"| P4

    P4 -->|"Structured Report"| P5

    subgraph P5["P5: Output Generation"]
        direction LR
        P5a["Format Report"]
        P5b["Display in UI"]
    end

    P5 -->|"Save"| D2["D2: Report File<br/>(output/research_report.md)"]
    P5 -->|"Rendered Report +<br/>Download Button"| U

    P2 -.->|"Agent Logs"| OH["Output Handler<br/>(output_handler.py)"]
    OH -.->|"Real-Time<br/>Progress"| U

    D3["D3: Environment Variables<br/>(API Keys in os.environ)"]
    P1 -->|"Set Keys"| D3
    P3a -->|"Read EXA_API_KEY"| D3
```

---

## Figure 4.4 — Use Case Diagram (Chapter 4, §4.5.1)

```mermaid
graph LR
    User["👤 User"]

    subgraph System["InSight Forge System"]
        UC1["Select LLM Provider"]
        UC2["Select Model"]
        UC3["Enter API Keys"]
        UC4["Enter Research Query"]
        UC5["Upload PDF Documents"]
        UC6["Start Research"]
        UC7["View Real-Time Progress"]
        UC8["View Research Report"]
        UC9["Download Report"]
    end

    User --- UC1
    User --- UC2
    User --- UC3
    User --- UC4
    User --- UC5
    User --- UC6
    User --- UC7
    User --- UC8
    User --- UC9

    UC6 -->|"includes"| UC7
    UC6 -->|"includes"| UC8
    UC5 -.->|"extends"| UC4
```

**Note for draw.io:** If you prefer a proper UML Use Case diagram, draw the system boundary as a rectangle, place use case ovals inside, and the stick-figure actor outside with lines connecting to each use case. Add `<<include>>` arrows from "Start Research" to "View Real-Time Progress" and "View Research Report." Add a `<<extend>>` arrow from "Upload PDF Documents" to "Enter Research Query."

---

## Figure 4.5 — Sequence Diagram (Chapter 4, §4.5.2)

```mermaid
sequenceDiagram
    participant U as User
    participant APP as app.py
    participant SB as sidebar.py
    participant RES as researcher.py
    participant OH as output_handler.py
    participant EXA as Exa AI API
    participant PDF as PDF Files
    participant LLM as LLM Provider

    U->>APP: Open Application
    APP->>SB: render_sidebar()
    SB-->>APP: {provider, model}
    APP->>APP: Validate API Keys

    U->>APP: Enter Query + Upload PDFs
    U->>APP: Click "Start Research"

    APP->>APP: Save PDFs to temp files
    APP->>RES: create_researcher(selection)
    RES->>LLM: Initialize LLM Connection
    RES-->>APP: Agent Instance

    APP->>RES: create_research_task(agent, query)
    RES-->>APP: Task Instance

    APP->>OH: capture_output(container)
    APP->>RES: run_research(agent, task)

    RES->>LLM: Send Research Prompt
    LLM->>RES: Request Tool: Web Search
    RES->>EXA: POST /answer (query)
    EXA-->>RES: Answer + Citations

    LLM->>RES: Request Tool: PDF Analysis
    RES->>PDF: Read PDF Content
    PDF-->>RES: Extracted Text (≤12,000 chars)

    RES->>LLM: Synthesize All Sources
    LLM-->>RES: Structured Markdown Report

    RES-->>APP: CrewOutput
    OH-->>APP: Restore stdout

    APP->>U: Display Rendered Report
    APP->>U: Provide Download Button
```

---

## Figure 4.6 — Class Diagram (Chapter 4, §4.5.3)

```mermaid
classDiagram
    class BaseTool {
        <<abstract>>
        +name: str
        +description: str
        +_run()*
    }

    class BaseModel {
        <<abstract>>
    }

    class PDFAnalysisTool {
        +name: str = "Analyze Uploaded PDF"
        +description: str
        +_run(pdf_path: str) str
    }

    class EXAAnswerToolSchema {
        +query: str
    }

    class EXAAnswerTool {
        +name: str = "Ask Exa a question"
        +description: str
        +args_schema: Type[BaseModel]
        +answer_url: str
        +_run(query: str) str
    }

    class StreamlitProcessOutput {
        +container: Container
        +output_text: str
        +seen_lines: set
        +clean_text(text: str) str
        +write(text: str) void
        +flush() void
    }

    BaseTool <|-- PDFAnalysisTool
    BaseTool <|-- EXAAnswerTool
    BaseModel <|-- EXAAnswerToolSchema
    EXAAnswerTool --> EXAAnswerToolSchema : uses
```

---

## Figure 4.7 — System Flowchart (Chapter 4, §4.6)

```mermaid
flowchart TD
    A([Start]) --> B["User Launches Application"]
    B --> C["Configure Sidebar:<br/>Select Provider, Model"]
    C --> D{"API Keys<br/>Provided?"}
    D -->|No| E["Display Warning Message"]
    E --> C
    D -->|Yes| F["Enter Research Query"]
    F --> G{"Upload PDFs?"}
    G -->|Yes| H["Save PDFs to Temp Files"]
    H --> I["Append PDF Paths to Task"]
    I --> J["Click Start Research"]
    G -->|No| J
    J --> K["Create Research Agent<br/>(create_researcher)"]
    K --> L["Create Research Task<br/>(create_research_task)"]
    L --> M["Execute Research Crew<br/>(run_research → crew.kickoff)"]
    M --> N{"Agent Decides<br/>Tool Usage"}
    N -->|Web Search| O["Invoke EXAAnswerTool"]
    O --> P["Query Exa AI API"]
    P --> Q["Receive Answer + Citations"]
    N -->|PDF Analysis| R["Invoke PDFAnalysisTool"]
    R --> S["Extract Text from PDFs<br/>(≤12,000 chars)"]
    Q --> T["LLM Synthesizes All Information"]
    S --> T
    N -->|Both| O
    N -->|Both| R
    T --> U["Generate Structured Report"]
    U --> V["Display Report in Streamlit"]
    V --> W["Save to output/research_report.md"]
    W --> X["Provide Download Button"]
    X --> Y([End])

    style A fill:#4caf50,color:#fff
    style Y fill:#f44336,color:#fff
    style D fill:#fff9c4,stroke:#f9a825
    style G fill:#fff9c4,stroke:#f9a825
    style N fill:#fff9c4,stroke:#f9a825
```

---

## Figure 2.1 — Comparison of Existing Systems (Chapter 2)

This is best done as a **table in your Word document** rather than a Mermaid diagram. Use the following content:

| Feature | Google Scholar | ChatGPT | Perplexity AI | Semantic Scholar | InSight Forge |
|---|---|---|---|---|---|
| Web Search | ✓ (keyword) | ✓ (Google/Bing) | ✓ (built-in) | ✓ (academic) | ✓ (Exa Neural) |
| PDF Analysis | ✗ | ✓ (upload) | ✗ | ✗ | ✓ (multi-PDF) |
| Structured Report | ✗ | Requires prompting | ✗ | ✗ | ✓ (automatic 7-section) |
| Real Citations | ✓ | Improving | ✓ | ✓ | ✓ (Exa verified) |
| Offline Mode | ✗ | ✗ | ✗ | ✗ | ✓ (Ollama) |
| Multi-Provider LLM | ✗ | ✗ (OpenAI only) | ✗ | ✗ | ✓ (OpenAI/GROQ/Ollama) |
| Real-Time Progress | ✗ | Partial (thinking) | ✗ | ✗ | ✓ (live agent logs) |
| Automated Workflow | ✗ | Requires prompts | Partial | ✗ | ✓ (fully agentic) |
| Open Source | ✗ | ✗ | ✗ | ✗ | ✓ |
| Cost | Free search | $20/month+ | Free/Paid | Free | Pay-per-use / Free |

---

## Figure 2.2 — Research Gap Visualization (Chapter 2)

```mermaid
graph TB
    subgraph Gaps["Identified Research Gaps"]
        G1["Gap 1:<br/>No Unified End-to-End<br/>Research Platform"]
        G2["Gap 2:<br/>No Intelligent Agent-Based<br/>Task Orchestration"]
        G3["Gap 3:<br/>Cannot Combine<br/>Web + PDF Sources"]
        G4["Gap 4:<br/>No Automatic Structured<br/>Report Generation"]
    end

    subgraph Solution["InSight Forge — How It Fills the Gaps"]
        S1["Unified Streamlit platform<br/>integrating all steps"]
        S2["CrewAI agent autonomously<br/>selects and invokes tools"]
        S3["EXA web search + pypdf<br/>analysis in single workflow"]
        S4["7-section Markdown report<br/>generated automatically"]
    end

    G1 --> S1
    G2 --> S2
    G3 --> S3
    G4 --> S4

    style Gaps fill:#ffcdd2,stroke:#c62828,color:#000
    style Solution fill:#c8e6c9,stroke:#2e7d32,color:#000
```
