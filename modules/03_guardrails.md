# Module 03 – Guardrails

> Change Log  
> - 2025-12-02: Added `evidence_mode = "strict"` and conditional behavior to restrict summaries to explicitly supported claims.  
> - 2025-12-02: Added standardized warning messages for missing or very short sections (< 50 words).

## Purpose
Enforce safety, evidence, and hallucination rules for all section summaries and overall outputs so that the summarizer only reports what is supported by the paper.

## Inputs
- `section_summaries[section_name]` and optional `section_bullets[section_name]` from Module 02.
- Flags for:
  - Missing or empty sections.
  - Very short sections (for example, < 50 words).
- `evidence_mode`: `"strict"` or `"normal"` (default = `"normal"`).
- Global constraints from the system prompt (no invented citations, no fabricated sections, length limits, etc.).

## Strict Evidence Mode
When `evidence_mode = "strict"`:

- All summaries must **only include claims, equations, and results that appear explicitly in the provided text**.
- The module must not:
  - Infer new results that are not clearly present.
  - Add outside context, references, or background that are not mentioned in the paper.
  - Invent citations, datasets, or numerical values.
- If the text does not provide enough information to meet this standard for a given section, replace that section’s summary with a standard message such as:
  - `"The source text does not provide enough detail to summarize this section in strict evidence mode."`

When `evidence_mode = "normal"`:

- The summarizer may still rephrase and lightly generalize, but must continue to avoid obvious hallucinations and invented citations.
- Guardrails still check for unsupported claims and can downgrade or remove content that is clearly not backed by the paper.

## Section Warning Messages
For each section:

- If the section is **missing or empty**:
  - Do not output a normal summary.
  - Instead, output a standardized warning, for example:  
    - `"Section skipped: no usable text was provided."`

- If the section is **very short (for example, < 50 words)**:
  - Allow a brief summary, but attach a standardized warning, for example:  
    - `"Warning: Section is very short, so this summary may be incomplete."`

These warnings must be preserved in any user-facing outputs, including:
- The section-by-section table.
- The overall paper summary (if relevant).
- The final “Checks & Warnings” section.

## Additional Guardrail Checks
- Ensure no section is accidentally dropped or duplicated in the final outputs.
- Ensure no new section names are invented beyond the user-provided section list.
- Check that no fabricated citations, references, or numeric values were introduced during summarization.
- For very long or chunked papers, make sure any limits on context (e.g., truncated sections or partial coverage) are clearly mentioned in the warnings.

## Outputs passed to other modules
- Verified, guardrail-checked summaries and bullet lists.
- Standardized warning messages for missing or short sections.
- A list of any sections or claims that could not be fully summarized under the active evidence mode, for inclusion in the final “Checks & Warnings” output.
