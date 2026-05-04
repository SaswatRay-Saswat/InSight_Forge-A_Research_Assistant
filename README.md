# InSight_Forge-A_Research_Assistant

An AI-powered research assistant that automates **information gathering, document analysis, and report generation** using Large Language Models (LLMs) like Groq, OpenAI, Ollama and intelligent tools.

---

## 🚀 Overview

InSight Forge is designed to simplify the research process by combining:

* 🌐 Real-time web research
* 📄 PDF document analysis
* 🤖 AI-driven content generation

The system uses a **single intelligent agent with multiple tools** to generate structured research reports efficiently.

---

## ✨ Features

* 🔎 **Automated Research** using web APIs
* 📑 **PDF Analysis** for extracting insights from documents
* 🧠 **LLM-based Report Generation**
* ⚙️ **Multiple Model Support** (OpenAI / Groq / Ollama)
* 🖥️ **Interactive UI** built with Streamlit
* 💾 **Markdown Report Export**

---

## 🌟 Highlights

-  Intelligent tool-based agent using CrewAI  
-  Combines real-time web research with document analysis  
-  Supports both online data and PDF-based inputs  
-  Dynamic tool selection based on user query  
-  Structured & readable report generation as a markdown file  
-  Secure API key handling   
-  Clean and interactive Streamlit interface  

---

## 🏗️ System Architecture

```
User Input (Topic / PDF)
        ↓
Streamlit UI (app.py)
        ↓
Research Agent (CrewAI)
        ↓
Tools Layer
   ├── EXAAnswerTool (Web Search)
   └── PDFAnalysisTool (Document Analysis)
        ↓
LLM (OpenAI / Groq / Ollama)
        ↓
Output Handler
        ↓
Generated Research Report (.md)
```

---

## 🧠 Usage & System Workflow

1. User enters a research query. PDF documents can be uploaded, if needed.
2. User, then, selects a LLM provider.
3. The agent decides which tool to use:
   * Web search (EXA API)
   * PDF analysis
4. Data is processed
5. A structured research report is generated and displayed and also saved as a Markdown file

---

## 📚 Code Explanation

### Main Application (`app.py`)
- Handles Streamlit UI rendering  
- Takes user input (topic + PDFs)  
- Triggers the research workflow  
- Displays generated output  

---

### Research Module (`researcher.py`)
- Core logic of the system  
- Initializes LLM (OpenAI / Groq / Ollama)  
- Creates a single AI agent  
- Assigns tools (EXA + PDF analysis)  
- Defines and executes research tasks  

---

### Sidebar Module (`sidebar.py`)
- Handles model selection  
- Manages API key input  
- Provides configuration settings  

---

### Output Handler (`output_handler.py`)
- Formats generated content  
- Saves output as Markdown (`.md`)  
- Handles display of results  

---

### Key Design Insight
- Uses **single agent + multiple tools**
- NOT a multi-agent system  
- Tools are selected dynamically based on task  

---

## 🗂️ Project Structure

```
InSight_Forge/
│
├── app.py                    # Main Streamlit application
├── requirements.txt         # Dependencies
│
├── source/
│   ├── components/
│   │   ├── researcher.py    # Core agent logic
│   │   ├── sidebar.py       # UI configuration
│   │
│   ├── utils/
│   │   ├── output_handler.py # Output formatting & saving
│
├── research_report.md       # Generated output file
```

---

## ⚙️ Technologies Used

| Category      | Technology             |
| ------------- | ---------------------- |
| Language      | Python                 |
| Framework     | Streamlit              |
| AI Framework  | CrewAI                 |
| LLM Providers | OpenAI / Groq / Ollama |
| APIs          | EXA Search API         |
| Tools         | PDF Processing         |

---

## 📋 Prerequisites

- Python **3.10 or higher**
- pip (Python package manager)
- API Keys:
  - OpenAI or Groq
  - Exa API
  - Ollama (Optional; for local LLM support)

---

## 🔧 Installation & Setup

### 1️⃣ Clone Repository
```bash
git clone https://github.com/SaswatRay-Saswat/InSight_Forge-A_Research_Assistant.git
cd InSight_Forge-A_Research_Assistant
```

### 2️⃣ Create Virtual Environment
```bash
python -m venv .venv
source .venv/bin/activate   # Linux/Mac
.venv\Scripts\activate      # Windows
```

### 3️⃣ Install Dependencies
```bash
pip install -r requirements.txt
```

---

## 🔑 Environment Variables

Create a `.env` file and add your API keys:

```
OPENAI_API_KEY=your_key_here
GROQ_API_KEY=your_key_here
EXA_API_KEY=your_key_here
```

---

## ▶️ Usage

Run the application:

```bash
streamlit run app.py
```

Then:

1. Enter a research topic
2. Upload PDFs (optional)
3. Click generate
4. View and download the report

---

## 📊 Output Example

The system generates:

* Structured research content
* Well-formatted Markdown report
* Saved as `research_report.md`

---

## ⚠️ Limitations

* Depends on external APIs
* LLM responses may vary
* Requires internet connection

---

## 🚀 Future Improvements

* 📚 Citation and reference generation
* 🌍 Multi-language support
* 🎤 Voice input
* 📊 Visualization (charts/graphs)
* 🔗 Integration with academic databases

---

## 🧠 Key Concept

> This project uses a **single AI agent with multiple tools**, not a multi-agent system.

---

## 🤝 Contributing

Contributions are welcome!
Feel free to fork the repository and submit pull requests.

---

## ⭐ Acknowledgements

* CrewAI framework
* OpenAI / Groq / Ollama
* Exa API

---
