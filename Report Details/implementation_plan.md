# 📘 Report Overhaul — Implementation Plan (v2 — Final)

## Approach

Each chapter will be generated as a **separate new markdown file** inside `report_chapters/` in your project directory (`/home/raizel/manual-literature-review/report_chapters/`). Your original report file remains untouched. You copy-paste from these into your final Word/Docs document.

---

## Your Doubts — Resolved

### 1. Ollama 3.2 Clarification

**Ollama** is the *platform/software* for running AI models locally. It doesn't have a version "3.2" in that sense (its versions are like 0.1.x, 0.2.x, etc.).

**LLaMA 3.2** is a *model by Meta* that you run *on* Ollama.

So the correct way to write it in your report is:

> *"...local models such as LLaMA 3.2 running on the Ollama platform"*

NOT ~~"Ollama 3.2"~~. I'll fix this throughout the report.

### 2. Performance Evaluation + Test Results

✅ **Merging them is fine.** The merged section will be:

```
6.3 Test Cases and Results
    (Table with test cases + pass/fail status)
    (Summary paragraph on overall results)
    (Performance observations: timing across providers, PDF extraction speed, etc.)
```

This keeps the chapter tight and avoids thin sections.

### 3. Chapter 7 Structure

✅ **Removing Introduction, merging Analysis + Discussion.** New structure:

```
Chapter 7 — Results and Discussion

7.1 System Output
    (Screenshot placeholders with captions)
7.2 Analysis and Discussion
    (Merged: quality analysis + provider comparison + observations)
```

### 4. Chapter 6 — No Introduction Subheading

✅ **Agreed.** The chapter will start with 3–4 introductory lines (no subheading), then go directly into:

```
Chapter 6 — Testing

(3-4 line opening paragraph — no subheading)
6.1 Testing Strategy
6.2 Test Cases and Results
```

### 5. Chapter 8 — Corrections

- **Future Enhancement #9:** Noted — it was a mistype, I'll ignore it.
- **Future Enhancement #2 ("Advanced Citation and Reference Generation"):** I'll verify against your codebase. Your system currently generates a "Sources" section with numbered citations (title + URL) from Exa AI. The enhancement would be adding export in BibTeX/APA/IEEE format for tools like Zotero/Mendeley. I'll write it accordingly.
- **Future Enhancement #3 ("Enhanced Research Filtering and Source Evaluation"):** Your system currently doesn't filter or rank sources — it trusts whatever Exa AI returns. The enhancement would be adding relevance scoring, source credibility assessment, and date filtering. I'll write it accordingly.
- **"Absence of Advanced Citation Management" limitation:** Your system includes citations (title + URL) in the report's Sources section via Exa AI, but it does NOT support: citation format selection (APA/IEEE/MLA), export to reference managers (Zotero/Mendeley/BibTeX), or in-text citation formatting. I'll write the limitation to reflect this accurately.

### 6. References

✅ All references will be:
- **Free/open access** — no paid journals or paywalled papers
- **With full URLs** so you can visit them
- **IEEE format** with all details (authors, title, publication, year, URL)

---

## Final Execution Plan

### Phase 0: References & Citation Map
- Master reference list (20+ IEEE-format entries, all free/open, with URLs)
- Citation placement guide: exactly which `[X]` goes where in every chapter

### Phase 1: Chapter 1 — Introduction (Trim & Improve)
- Cut verbosity (~7 → 4–5 pages)
- Make objectives specific to InSight Forge tech stack
- Add project name explanation
- Add `[DIAGRAM: Figure 1.1 — Traditional vs AI-Assisted Research Workflow]` placeholder + Mermaid code
- Insert citation markers

### Phase 2: Chapter 2 — Literature Review (Major Upgrade)
- Add technology review (LLMs, AI Agents, Neural Search)
- Add comparison table placeholder
- Remove repetition across §2.1/2.2/2.3
- Trim motivation to ~1 page
- Insert 10+ citations
- Add `[DIAGRAM: Figure 2.1]` and `[DIAGRAM: Figure 2.2]` placeholders + Mermaid code

### Phase 3: Chapter 3 — System Analysis (Fix Overlap)
- Compress §3.2 (2 pages → 3 sentences referencing Ch 2)
- Compress §3.3 (2 pages → 1 paragraph)
- Convert requirements to numbered tables
- Expand feasibility with real cost data
- Insert citations

### Phase 4: Chapter 4 — System Design (Complete All Missing)
- Complete §4.5 UML (Use Case, Sequence, Class diagrams)
- Complete §4.6 System Flowchart
- Add §4.7 Data Storage Design
- **7 Mermaid diagrams** with placeholders
- Insert citations

### Phase 5: Chapter 5 — Implementation (Critical Fixes)
- Fix all wrong file names to match actual codebase
- Add `sidebar.py` and `output_handler.py` descriptions
- Fix "Ollama 3.2" → "LLaMA 3.2 on Ollama"
- Add **8 code snippets** from actual source files
- Trim verbose tech descriptions
- Insert citations

### Phase 6: Chapter 6 — Testing (Write from Scratch)
- Opening paragraph (3–4 lines, no subheading)
- §6.1 Testing Strategy
- §6.2 Test Cases and Results (12 test cases in table + performance observations)

### Phase 7: Chapter 7 + Chapter 8
**Chapter 7 — Results and Discussion:**
- §7.1 System Output (10 screenshot placeholders with captions)
- §7.2 Analysis and Discussion

**Chapter 8 — Conclusion & Future Scope (Fixes):**
- Map conclusion to Ch 1 objectives
- Complete limitation: "Absence of Advanced Citation Management"
- Add limitation: "No peer-reviewed database access"
- Complete Future Enhancement #2 (Citation management export)
- Complete Future Enhancement #3 (Source filtering & evaluation)
- Remove placeholder #9

---

## Execution Order

I'll generate them in the most efficient order:

```
Phase 0 (References) → Phase 4 (Ch 4 — most complex, diagrams)
→ Phase 5 (Ch 5 — critical fixes) → Phase 6 (Ch 6 — new)
→ Phase 7 (Ch 7+8 — new + fixes) → Phase 1 (Ch 1 — trim)
→ Phase 2 (Ch 2 — upgrade) → Phase 3 (Ch 3 — fix)
```

> [!NOTE]
> I'm doing Ch 4–7 first because they have the most work (new content, diagrams, code). Chapters 1–3 are primarily trimming/restructuring existing content, which is faster.

---

## Expected Output Files

```
/home/raizel/manual-literature-review/report_chapters/
├── 00_references.md              # IEEE reference list + citation URLs
├── 00_citation_map.md            # Where each [X] goes in every chapter
├── chapter_1_introduction.md
├── chapter_2_literature_review.md
├── chapter_3_system_analysis.md
├── chapter_4_system_design.md
├── chapter_5_implementation.md
├── chapter_6_testing.md
├── chapter_7_results.md
├── chapter_8_conclusion.md
└── diagrams/
    └── all_mermaid_diagrams.md    # All Mermaid code in one file for easy rendering
```

---

> [!IMPORTANT]
> **Ready to start?** Confirm and I'll begin with Phase 0 (References) → Phase 4 (System Design).
