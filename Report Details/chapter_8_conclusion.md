# Chapter 8 – Conclusion and Future Scope

## 8.1 Conclusion

The project "InSight Forge: An AI-Based Research Assistant" was developed with the objective of automating various stages of the research workflow using Artificial Intelligence, document processing, and web-based information retrieval. The proposed system successfully integrates user interaction, research agent coordination, information gathering, PDF analysis, and Large Language Model (LLM)-based content generation into a unified research assistance platform.

The implemented system achieves the objectives defined in Chapter 1 as follows:

- **Objective 1 (AI-powered research assistant):** The system implements an autonomous research agent using CrewAI [5] with the role of "Senior Academic Research Analyst," capable of performing multi-step research tasks without manual intervention.

- **Objective 2 (Automated web information gathering):** Web-based information retrieval is implemented using the Exa AI answer API [6], which performs neural search and returns answers with verified citations.

- **Objective 3 (Document analysis):** PDF analysis is implemented using pypdf [9], enabling the extraction and processing of textual content from user-uploaded research papers.

- **Objective 4 (Agent-based architecture):** The system uses CrewAI's agent framework to dynamically select and utilize appropriate tools (web search and PDF analysis) based on the research context.

- **Objective 5 (Advanced language model integration):** The system integrates multiple LLM providers — OpenAI [3], GROQ, and Ollama [8] — providing flexibility in model selection for language understanding and report generation.

- **Objective 6 (Structured report generation):** A consistent 7-section Markdown report template is enforced for every research run, producing publication-quality outputs that require minimal manual editing.

- **Objective 7 (User-friendly interface):** The Streamlit-based interface [7] provides an intuitive experience with simple input fields, configuration options, and one-click report download.

- **Objective 8 (Multiple model providers):** Users can switch between OpenAI, GROQ, and Ollama providers through the sidebar, choosing models based on quality, speed, cost, or privacy requirements.

- **Objective 9 (Reduced research effort):** Testing confirms that the system can produce structured research reports in 2–8 minutes, compared to hours or days required for manual research workflows.

The modular architecture adopted in the project improves maintainability, flexibility, and scalability. Testing confirms that the system operates correctly under normal conditions and handles error scenarios gracefully. The project demonstrates the practical application of agentic AI and Large Language Models in automating academic research workflows.

## 8.2 Limitations of the System

Although InSight Forge successfully automates several stages of the research workflow, certain limitations are present in the current implementation:

- **Dependency on Internet Connectivity:**
  The system relies on internet connectivity for web-based information retrieval (Exa AI), cloud-based LLM inference (OpenAI, GROQ), and API communication. Unstable or unavailable internet connections may affect system performance. However, the Ollama provider enables fully offline operation when internet access is not available.

- **Dependency on External APIs and Services:**
  The functionality depends on external APIs for web search and language model processing. Limitations include API response delays, service availability issues, rate limits imposed by providers, and API key requirements. Changes to external API interfaces may require corresponding updates to the system.

- **Variation in Generated Content Quality:**
  The quality of generated research reports depends on the clarity of user queries, the relevance of retrieved web information, the quality of uploaded PDF documents, and the capabilities of the selected language model. Smaller or less capable models may produce shallow or generic analysis. Generated outputs may require manual verification for critical applications.

- **Limited Document Format Support:**
  The system currently supports PDF document analysis using the pypdf library. Only text-based PDFs are compatible; PDFs containing primarily images, scanned content, or complex layouts (tables, mathematical equations) may result in incomplete or inaccurate text extraction. The system does not currently support Word documents, presentation files, spreadsheet files, or scanned image-based documents.

- **PDF Content Truncation:**
  Extracted PDF content is truncated to 12,000 characters per file to stay within the context window limits of smaller LLMs. For longer papers (30+ pages), significant portions of the document may not be analyzed.

- **Processing Time Variability:**
  Research execution time varies depending on internet speed, API response time, query complexity, size of uploaded PDFs, and the performance characteristics of the selected language model. Typical execution ranges from 2 to 8 minutes.

- **Absence of Advanced Citation Management:**
  While the system generates citations with titles and URLs from the Exa AI API, it does not support advanced citation management features such as selection of citation formats (APA, IEEE, MLA), export to reference management tools (Zotero, Mendeley, BibTeX), or automatic in-text citation formatting. Users must manually format citations according to their required style.

- **No Access to Peer-Reviewed Academic Databases:**
  The system uses Exa AI for web search, which searches the open web. It does not directly access peer-reviewed academic databases such as IEEE Xplore, PubMed, Scopus, or Web of Science. Important research papers behind paywalls or restricted to institutional access may be missed.

- **Absence of Research Storage and History Management:**
  The current system has no user account support, research history management, or project storage. Each research run overwrites the previous report file. Users cannot track or compare past research sessions.

- **Limited Collaborative Functionality:**
  The system is designed for single-user operation and does not support multi-user collaboration, shared research workspaces, or real-time collaborative editing.

Despite these limitations, InSight Forge successfully fulfils its primary objective of automating research tasks through AI-driven processing and integrated workflow coordination. Many of the identified limitations can be addressed through the future enhancements described below.

## 8.3 Future Enhancements

1. **Support for Additional Document Formats:**
   The current implementation only supports PDF document analysis. Future versions can be extended to support additional formats such as Word documents (.docx), presentation files (.pptx), plain text files (.txt), and spreadsheet documents (.xlsx). This would improve flexibility and allow analysis of a wider range of research materials.

2. **Advanced Citation and Reference Generation:**
   Future versions can incorporate automatic citation formatting in standard academic styles (APA, IEEE, MLA, Chicago). The system could generate bibliography entries in BibTeX or RIS format for direct import into reference management tools such as Zotero, Mendeley, or EndNote. This would significantly streamline the integration of generated reports into academic writing workflows.

3. **Enhanced Research Filtering and Source Evaluation:**
   The system currently relies on Exa AI to return relevant sources without additional filtering. Future enhancements could include relevance scoring of retrieved sources, source credibility assessment based on domain authority, publication date filtering to prioritize recent research, and the ability to exclude or prioritize specific types of sources. These features would give users greater control over the quality and recency of the research data.

4. **Improved Document Analysis Capabilities:**
   Future enhancements can include more advanced document processing techniques such as OCR-based text extraction for scanned documents and table and figure extraction from PDF files for more comprehensive analysis.

5. **Expanded and Updated AI Model Support:**
   Future versions can further expand support for additional cloud-based AI providers, improved local AI model integration, and domain-specific research models fine-tuned for particular fields (e.g., medical research, legal analysis, engineering surveys). This would provide greater flexibility and improved output quality for specialized research areas.

6. **Multi-User and Collaborative Research Support:**
   Future developments may include collaborative research functionalities such as multi-user access, shared research workspaces, collaborative report editing and generation, and research project management features. These improvements would enhance the applicability of the system in academic and team-based research environments.

7. **Research Storage and History Management:**
   The system can be enhanced by introducing user account support, research history management with timestamped reports, research project storage, and the ability to compare and build upon previous research sessions.

8. **Enhanced User Interface and Visualization:**
   Future improvements to the user interface may include interactive report editing, visualization of research findings through charts and knowledge graphs, improved mobile responsiveness, and customizable report templates. These enhancements would improve the overall user experience and accessibility of the system.
