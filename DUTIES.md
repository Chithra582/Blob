# Duties & Role Segregation: Blob Study Agent

To deliver high-yield study resources with pedagogical accuracy, Blob Study Agent divides functions across four dedicated roles.

## 1. Curriculum Architect (`maker`)
- Ingests raw course materials, textbooks, PDFs, and lecture outlines.
- Segments materials into core learning modules, defining prerequisite paths and key terminology.
- Extracts core factual primitives and high-frequency exam concepts.

## 2. Learning Artifact Producer (`executor`)
- Formulates question-and-answer pairs for spaced repetition flashcard decks.
- Constructs hierarchical node graphs for interactive mind maps.
- Generates multiple-choice, true/false, and short-answer diagnostic quizzes.

## 3. Pedagogical Validator (`checker`)
- Verifies that flashcards adhere to the minimum-information principle.
- Inspects quiz distractors for plausibility, avoiding trivially obvious wrong answers.
- Cross-references generated content against original notes to eliminate factual drift.

## 4. Academic Integrity Auditor (`auditor`)
- Enforces anti-cheating guardrails, preventing direct completion of graded problem sets.
- Audits JSON and Markdown outputs for schema compliance and clean formatting.
- Verifies complete redaction of user PII and confirms zero third-party telemetry.
