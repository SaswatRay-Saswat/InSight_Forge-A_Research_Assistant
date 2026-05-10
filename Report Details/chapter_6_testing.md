# Chapter 6 – Testing

Testing is a fundamental phase in software development that ensures the system functions correctly, handles errors gracefully, and meets the defined requirements. For "InSight Forge," testing was conducted manually to verify the correctness and reliability of each module, the integration between components, and the overall system workflow. This chapter describes the testing strategy adopted, the test cases executed, and the results obtained.

## 6.1 Testing Strategy

The testing of InSight Forge was performed using a combination of the following approaches:

- **Functional Testing:** Each functional requirement defined in Chapter 3 was verified by executing the corresponding system operation and observing whether the expected output was produced. This includes testing research query input, PDF upload, API key validation, model selection, research execution, and report generation.

- **Integration Testing:** The interaction between different modules was tested to ensure seamless data flow. This includes verifying that the sidebar configuration is correctly passed to the research agent, that uploaded PDFs are properly processed and incorporated into the task, and that the generated report is correctly displayed and downloadable.

- **UI Testing:** The Streamlit interface was tested across different states — initial load, configuration changes, research in progress, and research completed — to ensure all elements render correctly and respond to user interaction.

- **Error Handling Testing:** The system was tested with invalid or missing inputs to verify graceful error handling. This includes testing with missing API keys, corrupted PDF files, unavailable Ollama instances, and empty research queries.

- **Provider Compatibility Testing:** The research workflow was tested with all three supported LLM providers (OpenAI, GROQ, and Ollama) to verify that provider switching works correctly and that each provider produces valid research output.

## 6.2 Test Cases and Results

The following table presents the test cases executed during the testing phase, along with their inputs, expected outputs, actual outputs, and status.

**Table 6.1: Test Cases and Results**

| TC-ID | Test Case Description | Input | Expected Output | Actual Output | Status |
|---|---|---|---|---|---|
| TC-01 | Research query with OpenAI provider | Research query: "Research the latest AI Agent news" + Valid OpenAI and Exa API keys | Structured 7-section research report generated and displayed | Report generated successfully with all sections and citations | ✅ Pass |
| TC-02 | Research query with GROQ provider | Research query + Valid GROQ and Exa API keys | Structured research report generated | Report generated successfully | ✅ Pass |
| TC-03 | Research query with Ollama provider | Research query + Ollama running locally with a model loaded | Research report generated using local model (no web search) | Report generated based on model's training data; web search not triggered | ✅ Pass |
| TC-04 | PDF upload and analysis | Research query + 2 uploaded PDF research papers | Report incorporating content and insights from uploaded PDFs | PDF content was analyzed and referenced in the report | ✅ Pass |
| TC-05 | Missing OpenAI API key | OpenAI selected, no API key entered | Warning: "Please enter your OpenAI API key in the sidebar to get started" | Warning displayed, research blocked | ✅ Pass |
| TC-06 | Missing Exa API key | OpenAI selected, OpenAI key entered but no Exa key | Warning: "Please enter your EXA API key in the sidebar to get started" | Warning displayed, research blocked | ✅ Pass |
| TC-07 | Ollama offline detection | Ollama provider selected, Ollama service not running | "No Ollama models found" warning with empty model dropdown | Warning displayed: "⚠️ No Ollama models found. Make sure Ollama is running locally." | ✅ Pass |
| TC-08 | Custom model input | Select "Custom" from OpenAI model dropdown, enter "gpt-4o" | Custom model string used for LLM inference | Custom model accepted and used successfully | ✅ Pass |
| TC-09 | Report download | Complete a research run, click "Download Report" button | Markdown file downloaded to user's machine | File `research_report.md` downloaded correctly | ✅ Pass |
| TC-10 | Invalid PDF file | Upload a corrupted or non-PDF file renamed to .pdf | Error message returned by PDFAnalysisTool | "PDF reading error: ..." message returned; application did not crash | ✅ Pass |
| TC-11 | Real-time output display | Start research with verbose agent, observe progress container | Live agent logs visible in scrollable container | Agent's reasoning, tool invocations, and progress displayed in real-time | ✅ Pass |
| TC-12 | Large PDF processing | Upload a PDF with 50+ pages | Text extracted and truncated to 12,000 characters | Text extracted from all pages; content truncated as expected | ✅ Pass |

**Summary of Results:**

All 12 test cases passed successfully. The system correctly handles valid inputs, produces structured research reports across all three LLM providers, and gracefully manages error conditions such as missing API keys, unavailable services, and invalid files.

**Performance Observations:**

The execution time of the research workflow varies depending on the selected LLM provider, query complexity, and network conditions. The following observations were made during testing:

| Metric | OpenAI (GPT-4o-mini) | GROQ (Qwen 2.5-32B) | Ollama (LLaMA 3.2, local) |
|---|---|---|---|
| Application Startup | < 3 seconds | < 3 seconds | < 3 seconds |
| PDF Text Extraction (per PDF) | < 2 seconds | < 2 seconds | < 2 seconds |
| Research Completion Time | 2–5 minutes | 1–3 minutes | 3–8 minutes |
| Report Quality | High (detailed, well-structured) | Good (structured, slightly less detailed) | Moderate (no web sources, relies on training data) |

Key performance observations:
- GROQ provides the fastest inference due to its optimized hardware for LLM serving.
- OpenAI produces the most detailed and well-structured reports, particularly with GPT-4o.
- Ollama performance depends heavily on the local hardware and the model size. Web search is unavailable, which limits research comprehensiveness.
- PDF extraction is consistently fast across all providers, as it is a local operation independent of the LLM.
- Real-time output display introduces negligible overhead and does not affect overall research performance.
