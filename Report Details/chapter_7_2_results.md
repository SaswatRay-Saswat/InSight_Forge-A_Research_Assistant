# Chapter 7 – Results and Discussion

The InSight Forge system was successfully developed and tested to meet the functional and non-functional requirements defined in Chapter 3. The system demonstrates reliable performance in automating the research workflow, including web-based information retrieval, PDF document analysis, and structured report generation across all three supported LLM providers.

The Streamlit-based user interface provides a clean and intuitive environment for interacting with the system. Users can enter research queries in natural language, upload multiple PDF documents, and configure the LLM provider and model through the sidebar — all without requiring any technical expertise. The sidebar dynamically adapts to the selected provider, displaying relevant model options and API key fields for OpenAI and GROQ, and automatically discovering locally installed models for Ollama.

The research agent, implemented using the CrewAI framework, successfully performs autonomous multi-step research operations. Upon receiving a query, the agent dynamically decides whether to invoke the web search tool (EXAAnswerTool), the PDF analysis tool (PDFAnalysisTool), or both, based on the nature of the input and the available resources. This intelligent task orchestration eliminates the need for users to manually coordinate different research steps.

The web search integration through the Exa AI answer API retrieves relevant, up-to-date information with verified citations. Each citation includes a title and a URL, ensuring that every source listed in the generated report is real and accessible. This addresses one of the key limitations of general-purpose LLMs, which may produce plausible but unverifiable references.

The PDF analysis capability enables users to upload research papers and technical documents for incorporation into the research output. The system extracts textual content from uploaded PDFs and the research agent cross-references this information with web-sourced data, producing reports that reflect insights from both external and user-provided sources.

The generated research reports consistently follow the enforced 7-section Markdown template — Executive Summary, Key Findings, In-Depth Analysis, Research Gaps and Future Directions, Critique and Comparison of Research, Conclusion, and Sources. This consistency is achieved through the structured expected output template defined in the research task configuration. Reports are rendered within the Streamlit interface and made available for one-click download in Markdown format.

The real-time output display feature provides full transparency into the agent's decision-making process during research execution. Users can observe the agent's reasoning traces, tool invocation decisions, search queries, and synthesis steps as they happen, displayed in a scrollable container with ANSI codes and debug messages automatically filtered out. This transparency builds user trust and allows assessment of the research process itself.

Testing across all three LLM providers reveals distinct performance characteristics. OpenAI (GPT-4o / GPT-4o-mini) produces the most detailed and well-structured reports with thorough analysis and nuanced comparisons. GROQ provides faster inference with good quality output, making it suitable for users who prioritize speed. Ollama enables fully offline operation using locally installed models such as LLaMA 3.2, though reports rely entirely on the model's training data since web search is unavailable, resulting in less comprehensive coverage of recent developments.

Compared to traditional manual research workflows that require hours to days of effort across multiple disconnected tools, InSight Forge completes the entire research pipeline — from information gathering to structured report delivery — in approximately 2 to 8 minutes, depending on the selected provider and query complexity. The system effectively addresses the research gaps identified in Chapter 2: it provides a unified end-to-end research platform, employs intelligent agent-based task orchestration, combines web and document sources in a single workflow, and generates structured reports automatically.

Overall, the system performs reliably under normal operating conditions, handles error scenarios gracefully (such as missing API keys, corrupted PDFs, and unavailable services), and provides a user-friendly interface for conducting automated research. The results confirm that InSight Forge successfully achieves its stated objectives and demonstrates the practical application of agentic AI in automating academic research workflows.

The user interface and key outputs of the InSight Forge system are illustrated in Figures 7.1 to 7.10. These screenshots demonstrate the application's main features including the home screen, sidebar configuration for different providers, research input with PDF upload, real-time agent progress, and the generated research report with citations.

---

**[INSERT SCREENSHOT: Figure 7.1 — InSight Forge Application Home Screen]**

*Figure 7.1: InSight Forge — Application Home Screen showing the research query input area, PDF upload section, and Start Research button*

*Capture: Launch the app with `streamlit run app.py`. Full-page screenshot with expanded sidebar visible.*

---

**[INSERT SCREENSHOT: Figure 7.2 — Sidebar with OpenAI Provider Selected]**

*Figure 7.2: Sidebar Configuration — OpenAI provider with model dropdown (GPT-4o-mini selected) and API key input fields*

*Capture: Select OpenAI, expand Model Selection and API Keys sections.*

---

**[INSERT SCREENSHOT: Figure 7.3 — Sidebar with GROQ Provider Selected]**

*Figure 7.3: Sidebar Configuration — GROQ provider with GROQ-specific model list and API key fields*

*Capture: Switch to GROQ, show model dropdown and key fields.*

---

**[INSERT SCREENSHOT: Figure 7.4 — Sidebar with Ollama Provider Selected]**

*Figure 7.4: Sidebar Configuration — Ollama provider with dynamically discovered local models*

*Capture: Ensure Ollama is running locally, select Ollama, show populated model dropdown. Note: Exa key field is hidden.*

---

**[INSERT SCREENSHOT: Figure 7.5 — Research Query and PDF Upload]**

*Figure 7.5: Research Input — Query entered with uploaded PDF documents listed below*

*Capture: Type a query and upload 1-2 PDF files, screenshot before clicking Start Research.*

---

**[INSERT SCREENSHOT: Figure 7.6 — Real-Time Agent Progress]**

*Figure 7.6: Real-Time Agent Output — Live reasoning traces and tool invocations during research execution*

*Capture: Click Start Research, wait a few seconds, screenshot the scrollable progress container.*

---

**[INSERT SCREENSHOT: Figure 7.7 — Research Completed]**

*Figure 7.7: Research Completion — Status indicator showing successful completion*

*Capture: Wait for research to finish, screenshot the completion status.*

---

**[INSERT SCREENSHOT: Figure 7.8 — Generated Report (Top Section)]**

*Figure 7.8: Generated Research Report — Executive Summary and Key Findings sections*

*Capture: Scroll to the rendered report area, screenshot the top portion.*

---

**[INSERT SCREENSHOT: Figure 7.9 — Generated Report (Sources Section)]**

*Figure 7.9: Generated Research Report — Sources section with numbered citations (titles and URLs)*

*Capture: Scroll to the Sources section at the bottom of the report.*

---

**[INSERT SCREENSHOT: Figure 7.10 — Report Download]**

*Figure 7.10: Report Download — Download button for saving the report as a Markdown file*

*Capture: Screenshot the download section below the report.*
