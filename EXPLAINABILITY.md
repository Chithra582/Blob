# EXPLAINABILITY.md

This document explains the internal mechanisms, data lineage, operational boundaries, and governance framework of **Blob Study Agent** (`blob-study-agent`) in accordance with the **OpenGAP v0.1.0** specification for the **HiDevs GitAgent Passport** clearance pipeline.

> **Agent Name:** Blob Study Agent (`blob-study-agent`)  
> **Specification:** OpenGAP v0.1.0  
> **Category / Domain:** Education / AI-Powered Study Tools & Knowledge Synthesis  
> **Compliance Standard:** OpenGAP Checkpoint 2 (Explainability & Decision Governance), FERPA, GDPR  

---

## How the Agent Decides

Blob Study Agent operates as an autonomous pedagogical synthesis intelligence for **Blob**, an open-source study companion application. The agent transforms unstructured study materials—such as course syllabi, lecture transcripts, and textbook chapters—into structured active-learning artifacts: active-recall flashcards, visual concept mind maps, and formative diagnostic quizzes.

### 1. Decision Architecture

The decision and synthesis process flows through a deterministic, five-stage pedagogical pipeline:

```
User Input (Syllabus, Lecture Notes, Textbook Excerpt, Topic Query)
    │
    ▼
[Stage 1: Ingestion & Text Sanitization]
    │  - Normalizes formatting, strips non-printable characters, cleans OCR/PDF artifacts
    │  - Detects language and estimates token count
    │  - Verifies minimum substantive content threshold (>= 50 words)
    ▼
[Stage 2: Semantic Chunking & Concept Graphing]
    │  - Splits raw text into semantic units (300–600 tokens) preserving theorem/definition boundaries
    │  - Extracts core entities: definitions, formulas, historical dates, parent-child hierarchies
    │  - Constructs internal concept adjacency list for topical dependency tracking
    ▼
[Stage 3: Pedagogical Artifact Routing]
    │  ├── Route A: Flashcard Pipeline (Active Recall)
    │  │     - Applies minimum-information principle (one atomized concept per card)
    │  │     - Authors front prompt (retrieval cue / scenario)
    │  │     - Generates concise, factual back response with context note
    │  │
    │  ├── Route B: Mind Map Pipeline (Visual Topology)
    │  │     - Identifies primary root concept and 3–5 top-level branches
    │  │     - Computes hierarchical depth (maximum 3 tiers for viewport clarity)
    │  │     - Generates structured node-and-edge JSON adjacency tree
    │  │
    │  └── Route C: Quiz Pipeline (Formative Diagnostic)
    │        - Pinpoints high-yield conceptual hinge points and common misconceptions
    │        - Authors unambiguous question stem and single correct answer key
    │        - Synthesizes 3 plausible distractors targeting specific cognitive pitfalls
    │        - Formulates constructive pedagogical rationale for every option
    ▼
[Stage 4: Guardrail, Quality & Integrity Interception]
    │  - Anti-Cheating Gate: Scans for active exam prompts ("Solve question 4 on my test")
    │  - Factuality Verification: Cross-checks generated assertions strictly against source text
    │  - Distractor Disambiguation: Validates that only one answer is unequivocally correct
    ▼
[Stage 5: Client Delivery & Storage]
    │  - Delivers typed JSON / Markdown schema via tRPC router to Expo client
    │  - Caches study deck locally in device SQLite database
    ▼
Interactive Study Interface (Active Recall / Canvas / Quiz)
```

### 2. Classification Rubrics & Synthesis Criteria

#### Flashcard Synthesis Rubric
- **Minimum-Information Principle**: Each card tests exactly one atomized fact, formula, or relationship. Cards with multi-part compound questions are split into discrete sub-cards.
- **Prompt Clarity**: Front cues utilize question formats (`"What is..."`, `"Why does..."`, `"Compare X and Y"`) rather than open-ended essays, fostering active retrieval rather than passive recognition.
- **Back Brevity**: Answers are kept under 60 words, prioritizing key terms and operational definitions with an optional 1-line contextual explanation.

#### Mind Map Adjacency Rubric
- **Hierarchical Depth Limit**: Default tree depth is capped at 3 tiers (Root $\rightarrow$ Domain Categories $\rightarrow$ Sub-concepts $\rightarrow$ Detail Leaves) to prevent visual overcrowding on mobile viewports.
- **Relational Labeling**: Edges express explicit semantic relationships (`contains`, `depends on`, `leads to`, `contradicts`, `exemplifies`).
- **Node Conciseness**: Node labels are restricted to 2–5 words for instant visual scanning.

#### Quiz Diagnostic Rubric
- **Stem Objectivity**: Question stems provide complete context without trick phrasing or negative polarity traps (avoiding `"All of the following EXCEPT"` unless pedagogically essential).
- **Distractor Plausibility**: Incorrect choices are authored around documented student misconceptions, common mathematical sign errors, or related historical events—never absurd or humorous throwaways.
- **Pedagogical Rationale**: Every option includes an explanation detailing why it is correct or why the specific misconception occurred.

### 3. Confidence Scoring & Quality Evaluation

For each generated study artifact batch, the agent computes an internal quality and factuality score $Q \in [0.0, 1.0]$:

$$Q = 0.40 \cdot S_{\text{grounding}} + 0.35 \cdot S_{\text{clarity}} + 0.25 \cdot S_{\text{distractor}}$$

- **Source Grounding ($S_{\text{grounding}}$)**: Percentage of card assertions directly substantiated by the input text. If external unverified facts are detected, this score drops.
- **Clarity & Brevity ($S_{\text{clarity}}$)**: Penalizes cards exceeding 60 words or questions with ambiguous syntax.
- **Distractor Quality ($S_{\text{distractor}}$)**: Verifies pairwise Levenshtein and semantic distance between quiz options to prevent near-identical choices.

Batches scoring $Q \ge 0.85$ are immediately rendered; batches scoring between $0.70$ and $0.84$ trigger a secondary refinement pass; batches below $0.70$ prompt the user to provide more detailed or clearer study material.

### 4. Thresholding & Refusal Decision Criteria

The agent enforces strict refusal boundaries:
- **Active Exam & Cheating Refusal**: Prompts that exhibit exam-taking patterns (e.g., `"Quick, what is the answer to test question 5"`, screenshots of live exam software, or time-pressured test queries) are refused. The agent responds: *"Blob is a preparatory study companion designed for long-term learning. I cannot assist with active examinations or tests."*
- **Insufficient Input Threshold**: If user-submitted notes contain fewer than 50 words or consist entirely of formatting boilerplate, the agent halts synthesis and requests substantive material.
- **Unsupported Binary/Corrupt Payloads**: Malformed files, password-protected PDFs, or unreadable binary blobs trigger a clean error with guidance on supported formats (plain text, markdown, valid PDF).

### 5. Fallback Decision Mechanism

Blob Study Agent incorporates multi-tiered fallback mechanisms to ensure high availability:
- **Model Fallback Cascade**: If the primary preferred model (`gemini-2.0-flash`) encounters API timeouts or quota limits, the agent automatically falls back to secondary configurations (`gpt-4o`, `claude-3-5-sonnet`) without dropping the session.
- **Deterministic Heuristic Fallback**: If external LLM APIs are completely unreachable or offline, the agent shifts to a local, deterministic regex-based rule extractor. This extracts bolded definitions, bulleted lists, and section headers into simple flashcard pairs (`Term` $\rightarrow$ `Definition`) directly on the client, ensuring uninterrupted study functionality.
- **Local Storage Cache Fallback**: Generated decks and mind maps are persisted in local client storage (SQLite / Zustand persist). If a network disconnect occurs during a study session, the user can continue reviewing, quizzing, and modifying cached materials offline.

### 6. Human-in-the-Loop Governance

Blob enforces total learner sovereignty over all AI-generated content:
- **Zero Autonomous Publishing**: No flashcard deck or quiz is committed to the user's permanent library without learner review.
- **Full In-Place Editing**: Every generated card front/back, mind map node label, and quiz question can be edited, reworded, or deleted by the student.
- **Custom Additions**: Learners can append their own manually written cards and notes to any AI-generated deck.
- **Kill-Switch & Discard**: Learners can discard any generation run with a single click, immediately purging the session from active memory.

---

## The Data It Uses

Blob Study Agent operates under a strict, privacy-by-design architecture tailored for educational compliance.

### 1. Ingested Input Data

The agent processes only data explicitly supplied by the student:
- **Course Notes & Syllabi**: User-uploaded text files, markdown documents, extracted PDF text, or pasted lecture transcripts used as the sole ground-truth corpus for study material synthesis.
- **Topic & Subject Queries**: User-typed keywords or chapter titles used to focus generation on specific curriculum units.
- **Student Quiz Submissions**: In-app option selections submitted during self-testing, used exclusively to compute real-time diagnostic scores and highlight review recommendations.

### 2. Configuration & Reference Data

- **BYOK API Credentials**: User-provided API keys (Google Gemini, OpenAI, or Anthropic) used to authorize direct model inference.
- **Deck Customization Preferences**: User-configured settings including target card count (e.g., 10, 20, 50 cards), difficulty level (Foundational, Intermediate, Advanced), and preferred question formats.
- **Local UI Theme & Navigation State**: Client-side state managed via Zustand and persisted to local browser/device storage.

### 3. Base Model & Inference Lineage

- **Foundation Models**: Leverages frontier foundational models including Google Gemini (`gemini-2.0-flash`), OpenAI (`gpt-4o`), and Anthropic (`claude-3-5-sonnet`).
- **No Proprietary Model Training**: The agent does not train, fine-tune, or adapt proprietary weights using user data. All reasoning is executed through in-context few-shot prompting and structured schema validation.
- **Direct Client-to-Provider Relay**: Under the BYOK model, inference calls are dispatched directly from the client to the model provider's API endpoints using the user's personal credentials, completely bypassing intermediary logging servers.

### 4. Data Privacy, Storage, and Retention

- **0-Byte Central Server Storage**: Blob does not operate a centralized database storing student study notes, personal reflections, or learning histories.
- **Zero Telemetry on Intellectual Property**: Student lecture notes, proprietary course slides, and research summaries are never logged, retained, or utilized for LLM pre-training.
- **Client-Side Credential Storage**: User API keys are stored exclusively in the client device's secure local storage (Expo SecureStore / React Native Encrypted Storage). Keys are never transmitted to Blob backend developers or third-party brokers.
- **FERPA & GDPR Compliance**: Because the system stores zero student institutional records, student IDs, grade books, or academic disciplinary records, it satisfies the strict data minimization mandates of the Family Educational Rights and Privacy Act (FERPA) and General Data Protection Regulation (GDPR Article 5 & Article 28).

---

## Limitations

Understanding the operational boundaries and technical constraints of Blob Study Agent is essential for effective educational deployment.

### 1. Source Material Dependency & Error Propagation
- **Limitation**: The agent treats the user's uploaded notes as ground truth. If a student's lecture notes contain factual errors, miscalculated formulas, or obsolete dates, the agent will faithfully generate flashcards and quiz questions reflecting those errors.
- **Mitigation**: Every generated deck includes an explicit pedagogical disclaimer advising students to cross-reference auto-generated materials with certified textbooks, peer-reviewed literature, and instructor syllabi.

### 2. Complex Proofs & Holistic Theory Fragmentation
- **Limitation**: The minimum-information principle excels at factual recall, terminology, and discrete mechanisms. However, breaking holistic philosophical arguments or multi-page mathematical proofs into atomized flashcards risks losing the overarching conceptual synthesis.
- **Mitigation**: The agent pairs granular flashcard decks with hierarchical mind maps and umbrella synthesis cards that explicitly trace the sequence of multi-step proofs and broader theoretical frameworks.

### 3. API Quotas & Token Window Constraints
- **Limitation**: Massive documents (such as whole 800-page textbooks) exceed single-prompt context limits and can trigger client-side rate limits or excessive API billing against the user's BYOK key.
- **Mitigation**: The agent implements client-side chunking heuristics, advising students to process materials chapter-by-chapter or module-by-module. Built-in token estimation warns users before dispatching large generation batches.

### 4. Distractor Nuance & Plausibility Balance
- **Limitation**: Generating four distinct, plausible multiple-choice options for highly advanced, nuanced topics can occasionally produce distractors that are either too easily eliminated or subtly debatable in edge-case contexts.
- **Mitigation**: The quiz generation pipeline incorporates strict verification rules requiring clear, unambiguous distinction between the correct key and all distractors, accompanied by a detailed rationale for each choice.

### 5. Academic Integrity & Non-Proctored Scope
- **Limitation**: Blob Study Agent is an asynchronous, self-directed learning tool. It does not possess proctoring capabilities, identity verification, or accredited examination grading authority.
- **Mitigation**: The agent declines prompts framed as active exam assistance and directs learners toward foundational understanding rather than short-cut answer retrieval.

---

## Summary & Compliance Checklist

| Checkpoint 2 Requirement | Corresponding Section | Status |
| :--- | :--- | :---: |
| **How the agent decides** | [How the Agent Decides](#how-the-agent-decides) | **Covered** |
| - Decision architecture & 5-stage pipeline | Section 1 | Verified |
| - Classification rubrics & synthesis criteria | Section 2 | Verified |
| - Confidence scoring & quality evaluation | Section 3 | Verified |
| - Thresholding & refusal decision criteria | Section 4 | Verified |
| - Fallback decision mechanism | Section 5 | Verified |
| - Human-in-the-loop governance & student control | Section 6 | Verified |
| **The data it uses** | [The Data It Uses](#the-data-it-uses) | **Covered** |
| - Ingested input data (notes, syllabi, queries) | Section 1 | Verified |
| - Configuration & BYOK credentials | Section 2 | Verified |
| - Base model lineage & inference relay | Section 3 | Verified |
| - Data privacy, 0-byte egress & FERPA/GDPR | Section 4 | Verified |
| **Its limitations** | [Limitations](#limitations) | **Covered** |
| - Source material dependency & error propagation | Section 1 | Verified |
| - Complex theory fragmentation & proofs | Section 2 | Verified |
| - API quotas & token window constraints | Section 3 | Verified |
| - Distractor nuance & plausibility balance | Section 4 | Verified |
| - Academic integrity & non-proctored scope | Section 5 | Verified |
