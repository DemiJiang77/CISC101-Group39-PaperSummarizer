# Module 02 – Section Loop

> Change Log  
> - 2025-12-02: Added `summary_level` variable and conditional behavior for "short" vs "detailed" summaries (paragraph + bullet list).

## Purpose
Summarize each paper section in order while respecting all constraints (length, audience, terminology, hallucination rules).

## Inputs
- `sections`: ordered list of section names (e.g., Abstract, Introduction, …).
- `section_text[section_name]`: full text for each section.
- `audience`: "expert" or "general".
- `summary_level`: "short" or "detailed". Default to "short" if not specified.
- Global constraints inherited from the system prompt (e.g., per-section word limits, no invented content, no invented citations).

## Core Loop Behavior
For each section in `sections`:

1. **Check availability**
   - If no text or only whitespace is available, set a "missing/empty" flag for this section and pass it to Guardrails (Module 03) instead of producing a normal summary.
   - If the section text length is very short (for example, < 50 words), mark the section as "very short" but still attempt a brief summary.

2. **Extract key ideas**
   - Identify the main purpose, methods, and key results or claims that appear in the section text.
   - Preserve important terminology exactly as it appears in the paper.

3. **Apply audience style**
   - If `audience = "expert"` → keep technical terms and emphasize methods, assumptions, limitations, and precise results.
   - If `audience = "general"` → use simpler wording and focus on high-level goals, main findings, and why they matter.

4. **Apply summary level**
   - If `summary_level = "short"`:
     - Generate **one compact 1–2 sentence summary** of the section’s main idea.
     - Do not include a bullet list for this mode.
   - If `summary_level = "detailed"`:
     - First, generate a **short paragraph** that explains the section in a coherent way.
     - Then generate a **bullet list of 3–5 key points**, each capturing an important fact, result, definition, or insight from the section.
   - In both modes, only use information that is explicitly supported by the section text. Do not add new claims, results, or citations.

5. **Respect length and consistency**
   - Keep each section’s paragraph summary within the word limit from the system prompt as closely as possible.
   - Reuse the same terminology for important concepts across all sections to keep the summaries consistent.

6. **Store results**
   - Save the output for each section in a structured form that later modules can reuse:
     - `section_summaries[section_name]` → the paragraph or compact summary.
     - `section_bullets[section_name]` → the bullet list (for `"detailed"` mode) or an empty/omitted list (for `"short"` mode).
   - Pass these stored results, along with any flags for missing or short sections, to Module 03 (Guardrails) for checking.

## Outputs passed to other modules
- Per-section summaries and optional bullet lists.
- Flags for missing/empty or very short sections.
- A set of extracted key terms that can later support the mini-glossary and checks & warnings.
