# Explainability & Transparency Report: Blob Study Agent

> **Specification:** OpenGAP v0.1.0  
> **Domain:** Education / AI-Powered Study Tools & Knowledge Synthesis  
> **Target System:** Blob (AI-Powered Study Tool)  
> **Audit Status:** Qualified for HiDevs GitAgent Passport  

---

## 1. Overview & Operational Purpose

**Blob Study Agent** is an autonomous pedagogical synthesis intelligence for **Blob**, an open-source study companion application. The agent transforms unstructured study materials—such as course syllabi, lecture transcripts, and textbook chapters—into structured learning artifacts:
1. **Active-Recall Flashcards**: Bite-sized question-answer units designed for spaced repetition.
2. **Conceptual Mind Maps**: Hierarchical tree structures mapping topic relationships and dependencies.
3. **Adaptive Formative Quizzes**: Diagnostic assessments with explanations for self-testing.
4. **BYOK Privacy Framework**: Direct client-to-model inference without centralized data warehousing.

---

## 2. How the Agent Decides (Decision-Making Logic)

```
User Input (Syllabus, Lecture Notes, Textbook Excerpt, Topic Query)
  │
  ├── 1. Material Parsing & Chunking
  │      ├── Filter formatting noise and extract raw text
  │      ├── Identify structural headers, definitions, and core theorems
  │      └── Split content into semantic concept chunks (300-500 tokens)
  │
  ├── 2. Pedagogical Artifact Routing
  │      ├── Route to FLASHCARD Pipeline:
  │      │   ├── Apply minimum-information principle
  │      │   ├── Generate front prompt (question/scenario)
  │      │   └── Formulate concise, factual back response
  │      │
  │      ├── Route to MIND MAP Pipeline:
  │      │   ├── Identify central root topic
  │      │   ├── Extract primary categories and subtopics
  │      │   └── Output hierarchical node-edge adjacency tree
  │      │
  │      └── Route to QUIZ Pipeline:
  │          ├── Select high-value conceptual hinge points
  │          ├── Author diagnostic stem + correct key
  │          ├── Generate plausible distractors based on common misconceptions
  │          └── Formulate pedagogical rationale for each option
  │
  └── 3. Academic Integrity & Quality Verification
         ├── Check against cheating/direct exam answer patterns
         ├── Verify factual consistency with source input
         └── Deliver structured JSON / Markdown payload to client UI
```

---

## 3. Data Sources & Inputs Used

| Data Input | Source | Purpose | Data Handling & Privacy |
|---|---|---|---|
| **Course Notes & Syllabi** | User upload (text, PDF, pasted notes) | Primary ground-truth corpus for generating study materials | Processed ephemerally in-session; never stored centrally or used for training |
| **BYOK API Credentials** | User client settings (OpenAI, Anthropic, Google) | Authorizing direct LLM generation calls | Maintained exclusively in client local storage; never logged on server |
| **Topic Selections** | User interactive search & tap inputs | Focusing artifact generation on specific sub-modules | Ephemeral query parameter; sanitized and anonymized |
| **Quiz Responses** | Student in-app practice submissions | Calculating diagnostic comprehension scores | Evaluated locally; stored only on device SQLite / local database |

---

## 4. Known Limitations & Failure Modes

### 1. Source Material Inaccuracies & Propagation
*Limitation:* If a student uploads flawed or incomplete lecture notes, the agent will faithfully generate flashcards and quizzes reflecting those errors.  
*Mitigation:* The agent includes a source disclaimer on all decks, advising students to verify specific factual claims against accredited textbooks or instructor syllabi.

### 2. Distractor Plausibility Balance
*Limitation:* Automatically generated quiz distractors can occasionally be either too obviously wrong or ambiguously debatable.  
*Mitigation:* The pedagogical validator checks distractors against common student misconceptions while verifying that only one answer is objectively and unequivocally correct.

### 3. Mind Map Visual Overcrowding
*Limitation:* Highly detailed chapters can produce deep, tangled mind maps that become illegible on mobile phone viewports.  
*Mitigation:* The agent limits default mind map depth to 3 hierarchical tiers, supporting expandable child sub-trees on user request.

### 4. Over-Fragmentation of Complex Theories
*Limitation:* Breaking holistic philosophical or multi-step engineering proofs into isolated flashcards can obscure the big-picture synthesis.  
*Mitigation:* The agent pairs granular flashcards with umbrella mind maps and synthesis summary cards that explicitly trace multi-step reasoning.

---

## 5. Verification, Safety & Human Oversight

1. **Anti-Cheating Policy**: The agent strictly declines prompts formatted as active exam questions ("Solve this right now for my test") and redirects towards concept-level explanations.
2. **Zero Telemetry by Default**: With the Bring Your Own API Key model, user requests travel directly between the client and the model provider without intermediary logging.
3. **User Editing & Customization**: Every generated flashcard, mind map node, and quiz question can be edited, deleted, or supplemented by the student before saving to their personal study library.
4. **FERPA & GDPR Compliance**: Zero storage of student personal identification, grade histories, or academic standing records.
