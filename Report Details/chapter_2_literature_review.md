# Chapter 2 – Literature Review

## 2.1 Introduction to Existing Research Tools

The rapid growth of digital information has led to the development of a wide range of research tools designed to assist users in information retrieval, analysis, and knowledge synthesis. These tools play a crucial role in academic, professional, and industrial environments. Over time, research tools have evolved from simple search engines to advanced AI-powered systems capable of understanding natural-language queries and generating meaningful responses.

Traditional research tools include search engines and academic databases such as Google Scholar, which allow users to search for scholarly articles, journals, and conference papers. While these platforms provide access to extensive academic content, they require users to manually read, interpret, and extract relevant information from multiple sources — a process that is time-consuming and cognitively demanding.

With advancements in Artificial Intelligence, modern research tools have incorporated Natural Language Processing (NLP) techniques to improve user interaction. AI-powered systems such as ChatGPT [3] and Perplexity AI [12] represent a new generation of research assistants that can understand natural language queries and generate human-like responses. Specialized platforms such as Semantic Scholar [11] use machine learning to enhance search relevance and provide citation analysis and paper recommendations.

Despite these advancements, most existing tools operate as independent systems, each addressing a specific aspect of the research process. This lack of integration highlights the need for a unified system that combines information retrieval, document analysis, and report generation within a single framework.

## 2.2 Large Language Models in Research

Large Language Models (LLMs) have fundamentally transformed the landscape of AI-assisted research. These models are based on the Transformer architecture, introduced by Vaswani et al. [1], which uses self-attention mechanisms to process sequential data more effectively than previous recurrent neural network approaches.

The development of GPT-3 (Generative Pre-trained Transformer 3) by Brown et al. [2] demonstrated that large-scale language models trained on diverse internet text can perform a wide variety of NLP tasks through few-shot and zero-shot learning, without task-specific fine-tuning. This capability established the foundation for using LLMs as general-purpose research assistants.

GPT-4 [3], released by OpenAI, further advanced these capabilities with improved reasoning, longer context windows, and more accurate text generation. These models can summarize research papers, answer complex questions, compare methodologies, and generate structured academic content — tasks that are directly relevant to the research workflow.

The open-source LLM movement has also gained significance. Meta's LLaMA (Large Language Model Meta AI) [14] demonstrated that high-quality language models can be made available for open research and local deployment. This enables users to run AI models on their own hardware, addressing concerns about data privacy, cost, and dependency on cloud services. Platforms such as Ollama [8] simplify the process of downloading and running these open-source models locally.

In the context of research assistance, LLMs provide the core intelligence for understanding user queries, analyzing collected information, synthesizing findings from multiple sources, and generating coherent, structured reports. However, standalone LLMs lack the ability to autonomously search the web, read documents, or execute multi-step research workflows — capabilities that require agent-based architectures.

## 2.3 AI Agents and Autonomous Systems

The concept of AI agents extends beyond simple question-answering by enabling systems to autonomously plan, reason, and act. An AI agent is a software entity that perceives its environment, makes decisions, and takes actions to achieve specified goals.

A significant development in this field is the ReAct (Reasoning and Acting) paradigm, proposed by Yao et al. [4], which demonstrates that language models can effectively interleave reasoning traces with concrete actions. In the ReAct framework, an LLM-based agent generates a thought (reasoning about what to do), takes an action (such as searching the web or reading a document), observes the result, and then reasons about the next step. This iterative process enables complex, multi-step task execution.

CrewAI [5] is an agent orchestration framework that implements these concepts for practical applications. It provides abstractions for defining AI agents with specific roles, goals, and backstories; equipping agents with custom tools; defining structured tasks with expected outputs; and orchestrating sequential or parallel execution through "crews." CrewAI enables developers to build autonomous research pipelines where the agent decides which tools to use and in what order, based on the given task.

In InSight Forge, the CrewAI framework is used to create a "Senior Academic Research Analyst" agent that autonomously decides when to search the web (using the Exa AI tool) and when to read uploaded PDFs (using the PDF Analysis tool), then synthesizes all gathered information into a structured report.

## 2.4 Web Search and Neural Search APIs

Traditional web search engines like Google and Bing use keyword-based retrieval to find relevant web pages. While effective for general information discovery, they are not specifically designed for research-quality content retrieval with structured citation support.

Exa AI [6] represents a different approach through neural search — using AI models to understand the semantic meaning of queries rather than relying solely on keyword matching. The Exa answer API accepts a natural-language question and returns a synthesized answer along with verified source citations (title and URL). This citation-first design ensures that every piece of information returned is traceable to a real, accessible source.

In InSight Forge, Exa AI serves as the web research component. The research agent sends queries to the Exa answer API and incorporates the returned answers and citations into the final report. This approach provides more focused, research-quality results compared to general-purpose search engines, while ensuring citation integrity.

## 2.5 Study of Existing Systems

Several existing systems address various aspects of the research workflow. A comparative analysis of the major systems is presented below.

**Google Scholar** enables users to search for scholarly articles across various domains. It is highly effective for retrieving relevant sources but does not provide automated analysis, summarization, or report generation. Users must manually review and compile information from retrieved documents.

**ChatGPT** [3] allows natural language interaction and can generate summaries, answer questions, and produce structured content when carefully prompted. Its deep research mode performs multi-step web searches. However, it operates as a standalone tool, requires manual prompt engineering for consistent structured output, and is locked to a single provider (OpenAI).

**Perplexity AI** [12] functions as an AI-powered answer engine that provides responses with inline citations from web sources. It is effective for quick factual queries but does not support PDF analysis, structured multi-section report generation, or local model deployment.

**Semantic Scholar** [11] uses machine learning to enhance academic search with features like citation analysis, paper recommendations, and influence metrics. While valuable for discovering research, it does not provide automated synthesis or report generation.

**[INSERT TABLE: Figure 2.1 — Comparison of Existing Research Systems]**

*Figure 2.1: Comparative Analysis of Existing Research Systems*

```
The comparison table content is available in diagrams/all_mermaid_diagrams.md
under "Figure 2.1 — Comparison of Existing Systems"
```

## 2.6 Limitations of Existing Systems

The study of existing systems reveals several common limitations:

- **Fragmented Workflow:** Existing tools address individual components of the research process (search, reading, writing) but do not combine them into a unified pipeline. Users must switch between multiple platforms to complete a single research task.

- **Lack of Intelligent Task Orchestration:** Most systems do not possess the ability to dynamically select and utilize tools based on the query context. They perform fixed operations rather than adapting their approach based on the research requirements.

- **Inability to Combine Multiple Data Sources:** Existing tools typically focus on either web-based search or document processing, but rarely provide a mechanism to merge insights from both sources in a cohesive manner.

- **Limited Structured Report Generation:** While AI tools can generate text, they often do not produce well-organized outputs following a consistent academic template without extensive prompt engineering. Output structure varies between runs.

- **Provider Lock-in and Cost:** Most platforms are locked to a single AI provider with subscription-based pricing. Users cannot switch between providers based on their needs or budget.

## 2.7 Research Gap Identification

Based on the study of existing systems and their limitations, the following research gaps are identified:

1. **Lack of an integrated, end-to-end research automation platform** that combines web search, document analysis, and report generation in a single automated workflow.

2. **Absence of intelligent agent-based task orchestration** in research tools, where an AI agent autonomously decides which tools to invoke and in what sequence based on the research context.

3. **Inability to effectively combine web-sourced and document-sourced data** within a single research analysis, cross-referencing external information with user-provided papers.

4. **Limited support for automatic structured report generation** following a consistent academic template without requiring manual prompt engineering.

**[INSERT DIAGRAM: Figure 2.2 — Research Gap Visualization]**

*Figure 2.2: Identified Research Gaps and How InSight Forge Addresses Them*

```
Mermaid code for Figure 2.2 is available in diagrams/all_mermaid_diagrams.md
```

## 2.8 Motivation for the Proposed System

The motivation for developing InSight Forge arises from the challenges outlined above and the opportunities presented by recent AI advancements:

- **Reducing research time and effort:** The manual research process takes hours to days. An automated system that handles information gathering, analysis, and report generation can compress this to minutes.

- **Leveraging agentic AI frameworks:** The emergence of CrewAI [5] and the ReAct paradigm [4] enables building systems where AI agents autonomously plan and execute multi-step research workflows.

- **Exploiting neural search capabilities:** Exa AI's citation-first search [6] provides a reliable mechanism for web-based research with verifiable sources, addressing the citation reliability issues present in general-purpose LLMs.

- **Providing provider flexibility:** Supporting multiple LLM providers (including local models via Ollama [8]) gives users control over cost, quality, speed, and data privacy — a capability absent in most existing platforms.

- **Meeting the demand for accessible research tools:** An open-source, easily deployable research assistant with a simple web interface makes advanced research capabilities accessible to users regardless of institutional resources or technical expertise.

These factors collectively motivate the development of InSight Forge as a unified, agent-based research automation system.
