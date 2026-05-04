# InSight_Forge-A_Research_Assistant
AI-based Research Assistant.
It is AI-based Research Assistant that helps with research by analysing various research papers and providing insights on them.


An AI-powered research assistant that automates **information gathering, document analysis, and report generation** using Large Language Models (LLMs) and intelligent tools.

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

## 🧠 How It Works

1. User enters a research topic
2. (Optional) Uploads PDF documents
3. The agent decides which tool to use:

   * Web search (EXA API)
   * PDF analysis
4. Data is processed using an LLM
5. A structured research report is generated
6. Output is displayed and saved as a Markdown file

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

## 🔧 Installation

### 1. Clone the Repository

```bash
git clone https://github.com/SaswatRay-Saswat/InSight_Forge-A_Research_Assistant.git
cd InSight_Forge-A_Research_Assistant
```

### 2. Create Virtual Environment

```bash
python -m venv venv
source venv/bin/activate   # (Linux/Mac)
venv\Scripts\activate      # (Windows)
```

### 3. Install Dependencies

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

## 📜 License

This project is for academic and educational purposes.

---

## 👨‍💻 Author

**Saswat Ray**
B.Tech Major Project

---

## ⭐ Acknowledgements

* CrewAI framework
* OpenAI / Groq / Ollama
* EXA API

---
