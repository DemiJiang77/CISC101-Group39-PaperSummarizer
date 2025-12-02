# Module 03 – Guardrails

**Purpose:** Apply safety and accuracy checks to reduce hallucinations and handle edge cases.

**Responsibilities:**
- Check for missing, empty, or very short sections and mark them with internal flags.
- Enforce “no hallucination” rules:
  - Only summarize content that actually appears in the text.
  - Avoid inventing sections, citations, or numbers.
- For very short or missing sections, prepare a standard warning message the system can show later.
- Handle long papers by supporting section or group chunking when needed.
- Make sure no section is accidentally skipped or summarized twice.

**Passes to:** Module 04 – Rendering & Refinement.
