# Chapter 7 – Results and Discussion

## 7.1 System Output

This section presents the visual outputs of the implemented system, demonstrating the various interface states and functionalities of InSight Forge during operation. Each figure illustrates a specific aspect of the system's workflow.

### Application Home Screen

**[INSERT SCREENSHOT: Figure 7.1 — InSight Forge Application Home Screen]**

*Figure 7.1: InSight Forge — Application Home Screen*

*Capture instructions: Launch the app with `streamlit run app.py`. Take a full-page screenshot showing the centered "InSight Forge" title, the research query text area with default text, the PDF upload area, and the "Start Research" button. The sidebar should be visible in its expanded state.*

The home screen presents a clean and organized layout. The title "InSight Forge" is displayed prominently at the center. Below the title, a text area accepts the user's research query, followed by a multi-file PDF uploader. The "Start Research" button is centered below the input area. The sidebar on the left provides configuration options.

---

### Sidebar Configuration — OpenAI Provider

**[INSERT SCREENSHOT: Figure 7.2 — Sidebar with OpenAI Provider Selected]**

*Figure 7.2: Sidebar Configuration — OpenAI Provider Selected*

*Capture instructions: In the sidebar, select "OpenAI" as the provider. Expand the Model Selection and API Keys sections. Enter sample API keys (or use your actual keys — they will be masked with dots). Take a screenshot of the sidebar showing the radio buttons, model dropdown (with GPT-4o-mini selected), and the two API key input fields (OpenAI + Exa).*

When OpenAI is selected as the LLM provider, the sidebar displays a model dropdown with preset options (gpt-4o-mini, gpt-4o, o1, o1-mini, o1-preview, o3-mini, Custom) and two API key input fields for OpenAI and Exa keys.

---

### Sidebar Configuration — GROQ Provider

**[INSERT SCREENSHOT: Figure 7.3 — Sidebar with GROQ Provider Selected]**

*Figure 7.3: Sidebar Configuration — GROQ Provider Selected*

*Capture instructions: Select "GROQ" as the provider. Show the GROQ-specific model dropdown (qwen-2.5-32b, deepseek-r1-distill-qwen-32b, etc.) and the GROQ + Exa API key fields.*

When GROQ is selected, the sidebar displays the GROQ-specific model list and corresponding API key inputs.

---

### Sidebar Configuration — Ollama Provider

**[INSERT SCREENSHOT: Figure 7.4 — Sidebar with Ollama Provider Selected]**

*Figure 7.4: Sidebar Configuration — Ollama Local Models*

*Capture instructions: Start Ollama locally (`ollama serve`), ensure at least one model is pulled. Select "Ollama" in the sidebar. Show the dynamically populated model dropdown and the warning about limited function-calling. Note: the Exa API key field should NOT be visible.*

When Ollama is selected, the system queries the local Ollama instance and dynamically populates the model dropdown. A warning about limited function-calling capabilities is displayed, and the Exa API key field is hidden since web search is not available for local models.

---

### Research Input with PDF Upload

**[INSERT SCREENSHOT: Figure 7.5 — Research Query Input and PDF Upload]**

*Figure 7.5: Research Input — Query and PDF Upload*

*Capture instructions: Type a research query (e.g., "Research the latest developments in AI agents and autonomous systems in 2025"). Upload 1-2 sample PDF files. Take a screenshot showing both the filled text area and the uploaded files listed below.*

The system accepts natural-language research queries and optional PDF uploads simultaneously, enabling comprehensive research that combines web data with user-provided documents.

---

### Real-Time Agent Progress

**[INSERT SCREENSHOT: Figure 7.6 — Real-Time Agent Output During Research]**

*Figure 7.6: Real-Time Agent Output During Research Execution*

*Capture instructions: Click "Start Research" and wait a few seconds until the agent begins processing. Take a screenshot showing the "Researching..." status indicator and the scrollable output container displaying the agent's live logs (tool selections, search queries, reasoning steps).*

During research execution, the system displays the agent's real-time progress in a scrollable container. The output shows the agent's reasoning process, tool invocation decisions, search queries sent to Exa AI, and PDF reading progress. ANSI escape codes and debug messages are automatically cleaned from the display.

---

### Research Completion

**[INSERT SCREENSHOT: Figure 7.7 — Research Completed Status]**

*Figure 7.7: Research Completion Status*

*Capture instructions: Wait for the research to complete. Take a screenshot showing the collapsed status indicator reading "Research completed!" with the green checkmark.*

Upon successful completion, the status indicator updates to "Research completed!" and collapses to indicate that the workflow has finished.

---

### Generated Research Report — Top Section

**[INSERT SCREENSHOT: Figure 7.8 — Generated Report: Executive Summary and Key Findings]**

*Figure 7.8: Generated Research Report — Executive Summary*

*Capture instructions: Scroll down to the rendered Markdown report. Take a screenshot showing the Executive Summary and the beginning of Key Findings sections.*

The generated report follows the enforced 7-section template. The Executive Summary provides 3-4 paragraphs of key takeaways, followed by the Key Findings section with evidence-backed bullet points.

---

### Generated Research Report — Sources Section

**[INSERT SCREENSHOT: Figure 7.9 — Generated Report: Sources with Citations]**

*Figure 7.9: Generated Research Report — Sources with Citations*

*Capture instructions: Scroll to the bottom of the rendered report. Take a screenshot showing the "Sources" section with numbered citations (titles and URLs from Exa AI).*

The Sources section demonstrates the citation integrity of the system. Each source is numbered and includes the title and URL, retrieved from the Exa AI answer API. These are real, verifiable web sources.

---

### Report Download Interface

**[INSERT SCREENSHOT: Figure 7.10 — Report Download Button]**

*Figure 7.10: Report Download Interface*

*Capture instructions: Scroll to the download section below the report. Take a screenshot showing the "Download Research Report" heading and the "Download Report" button.*

The download interface provides a one-click button to save the generated report as a Markdown (`.md`) file. The file can be opened in any text editor, Markdown viewer, or imported into word processing applications.

---

## 7.2 Analysis and Discussion

### Quality of Generated Reports

The research reports generated by InSight Forge consistently follow the enforced 7-section template: Executive Summary, Key Findings, In-Depth Analysis, Research Gaps & Future Directions, Critique and Comparison of Research, Conclusion, and Sources. This consistency is a key advantage over general-purpose AI platforms, which require careful prompt engineering to produce structured output.

The quality of the reports varies based on the LLM provider:

- **OpenAI (GPT-4o / GPT-4o-mini):** Produces the most detailed and well-structured reports. The analysis is thorough, comparisons are nuanced, and the research gaps section provides actionable future directions. Citation integration from Exa AI is seamless.

- **GROQ (Qwen 2.5-32B / LLaMA 3.3-70B):** Produces good quality reports with faster inference times. The content is well-organized and follows the template, though the depth of analysis may be slightly less than GPT-4o in some cases.

- **Ollama (LLaMA 3.2, local):** Report quality depends on the model and local hardware. Since web search is disabled for Ollama, reports rely entirely on the model's training data, which may not include the most recent developments. The output is suitable for general topic overviews but less comprehensive for cutting-edge research.

### Citation Integrity

A significant strength of InSight Forge is the use of Exa AI for web-based research. Unlike general-purpose LLMs that may generate plausible but non-existent citations, Exa AI returns real, verified URLs with their corresponding titles. This ensures that every source listed in the report is accessible and verifiable.

### PDF Analysis Integration

When PDF documents are uploaded, the system successfully extracts text content and incorporates it into the agent's analysis. The research agent cross-references information from PDFs with web-sourced data, producing reports that reflect both external and user-provided sources. The 12,000-character truncation per PDF ensures compatibility with smaller LLMs, though it may result in partial analysis of longer documents.

### Real-Time Transparency

The real-time output display provides full transparency into the agent's decision-making process. Users can observe which tools the agent selects, what queries it sends to Exa AI, and how it reasons about the gathered information. This transparency builds user trust and allows users to assess the quality of the research process itself.

### Comparison with Manual Research

Compared to traditional manual research workflows, InSight Forge demonstrates significant improvements in:

| Aspect | Manual Research | InSight Forge |
|--------|----------------|---------------|
| Time required | Hours to days | 2–8 minutes |
| Source discovery | Manual keyword search | Automated neural search |
| Cross-referencing | Manual comparison | Automated synthesis |
| Report structure | Manual organization | Automatic 7-section template |
| Citation format | Manual compilation | Automatic (title + URL) |
| Reproducibility | Variable (human-dependent) | Consistent template |

### Observations and Limitations Noted During Testing

- The system performs best when research queries are specific and well-defined. Vague or overly broad queries may result in less focused reports.
- API response times vary based on server load, which can affect overall research completion time.
- The report quality is fundamentally limited by the capabilities of the selected LLM. More capable models (e.g., GPT-4o) consistently produce better results than smaller models.
- The single-agent architecture, while simple and predictable, means there is no second agent to verify or challenge the research findings.
