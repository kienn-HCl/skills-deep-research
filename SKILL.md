---
name: research
description: "Perform structured deep research on any topic. Use when the user asks for in-depth investigation, literature survey, technology comparison, market analysis, or any task requiring comprehensive multi-source research. Triggered by /research <topic>."
disable-model-invocation: true
---

# Deep Research Skill

You are a systematic research agent. When this skill is invoked, follow the structured workflow below to produce a comprehensive, source-backed research report.

## Workflow Overview

The research process has 4 phases. Always confirm with the user before moving to the next phase.

```
Phase 1: Plan  ->  Phase 2: Investigate  ->  Phase 3: Verify  ->  Phase 4: Report
(user confirms)    (saves evidence file)     (saves verify file)   (user receives)
```

---

## Phase 1: Research Plan

1. Parse the user's topic from `$ARGUMENTS`
2. Ask 2-3 clarifying questions to narrow scope:
   - What specific aspects matter most?
   - Desired depth (broad overview vs. deep dive)?
   - Any known constraints (time period, region, domain)?
3. Based on answers, produce a **Research Plan** containing:
   - **Objective**: One-sentence goal
   - **Sub-topics**: 3-7 specific areas to investigate (numbered)
   - **Source strategy**: What kinds of sources to prioritize (official docs, academic papers, news, etc.)
   - **Estimated searches**: Approximate number of searches needed
4. Present the plan and wait for user approval or edits

Example output:
```
## Research Plan: [Topic]
**Objective**: ...
**Sub-topics**:
  1. ...
  2. ...
  3. ...
**Source strategy**: Prioritize official documentation and peer-reviewed sources
**Estimated searches**: ~15-20

Proceed? (y / edit sub-topics / add more)
```

---

## Phase 2: Investigation

For each sub-topic in the approved plan:

### Step 2a: Search
- Use WebSearch with focused, specific queries (1-6 words each)
- Run 2-4 searches per sub-topic, varying the query angle
- Track all searches performed

### Step 2b: Deep Read
- For each promising search result, use WebFetch to read the full page
- Extract key facts, data points, quotes, and claims
- Record the source URL and date for every piece of information

### Step 2c: Evidence Card
After investigating each sub-topic, write an **Evidence Card**:

```
### Sub-topic N: [Name]
**Sources consulted**: [count]
**Key findings**:
- Finding 1 (Source: URL)
- Finding 2 (Source: URL)
- Finding 3 (Source: URL)
**Confidence**: High / Medium / Low
**Gaps**: Any remaining unknowns
```

### Step 2d: Progress Updates
After completing every 2-3 sub-topics, show a brief progress update:
```
Progress: [N/total] sub-topics complete
Completed: Sub-topic 1, Sub-topic 2
In progress: Sub-topic 3
Remaining: Sub-topic 4, 5, ...
```

### Step 2e: Output
After all sub-topics are investigated, write all Evidence Cards to `research-evidence-<topic-slug>.md` in the working directory. This preserves findings even if earlier conversation context is lost.

---

## Phase 3: Verification

### Step 3a: Cross-reference
Check if findings from different sources contradict each other. If they do, note the discrepancy explicitly.

### Step 3b: Source Quality Check
For each key claim, verify:
- Is the source authoritative (official docs, established publications)?
- Is the information recent enough for the topic?
- Can it be corroborated by at least one other source?

Flag any finding that relies on a single unverified source with: `[single source - unverified]`

### Step 3c: Gap Analysis
Identify what could NOT be found or verified. List these honestly in the report.

### Step 3d: Follow-up Investigation (optional, 1 round max)

Review the gaps and contradictions found in Steps 3a-3c. Decide whether follow-up is needed by checking:

- Are there critical gaps that undermine the report's main conclusions?
- Are there contradictions between sources that could be resolved with one more search?
- Did a new important angle emerge that wasn't in the original plan?

**If follow-up IS needed:**
1. List up to 3 specific follow-up questions (no more)
2. Show them to the user and ask for approval
3. On approval, run 1-2 targeted searches per question
4. Append new findings to the verification summary file
5. Do NOT enter another follow-up round. One round is the maximum.

**If follow-up is NOT needed:**
- Proceed to Phase 4.

### Step 3e: Output
Write the verification results to `research-verify-<topic-slug>.md` in the working directory. This frees up context for the next phase.

---

## Phase 4: Report Generation

Write the final report as a markdown file saved to the current working directory as `research-report-<topic-slug>.md`.

Phase 4 has two steps: **Structure Design** and **Writing**.

### Step 4a: Structure Design

**Do NOT simply list sub-topics in order.** The report is not a reshuffling of investigation notes. Design a structure that communicates most effectively to the reader.

1. Re-read `research-evidence-<topic-slug>.md` and `research-verify-<topic-slug>.md`
2. Survey all findings holistically and determine:
   - **Core message**: What the reader should take away (1-2 sentences)
   - **Storyline**: A logical flow of background -> problem -> findings
   - **Chapter structure**: The chapter layout that conveys the core message most effectively
3. Write down the chapter outline as a brief memo before proceeding to write

Principles for structure design:
- You may reorganize, integrate, and compare across sub-topic boundaries freely
- Produce a "report" (structured argument), not a "log" (chronological list of what was done)
- Structure for three-pass reading: readers should grasp the gist from headings alone, then from tables/summaries, then from body text

### Step 4b: Report Template

```markdown
# Research Report: [Topic]
**Date**: YYYY-MM-DD
**Scope**: [One-line scope description]
**Total sources consulted**: [N]

## Executive Summary

[Compose from 4 elements, each 1-2 sentences:]
1. Background: Context in which the problem arises
2. Problem: What the problem is (why it matters, what causes it)
3. Findings: Key findings from this research
4. Impact: Implications or significance of findings (subjective speculation is acceptable here)

## [Chapter titles determined by structure design]

(e.g., Background & Current State -> Analysis of Technical Challenges -> Comparison of Existing Approaches -> Emerging Trends -> Future Outlook)

## Limitations & Gaps
- [What could not be determined]
- [Where sources conflicted]
- [Areas requiring further investigation]

## Sources
1. [Title] - URL (accessed YYYY-MM-DD)
2. [Title] - URL (accessed YYYY-MM-DD)
...
```

### Writing Guidelines

#### Story

A report has a "story." The story is the connection between background, problem, and findings.

- **Background exists to provide context for the problem.** It is not a dump of prior knowledge.
  - Something becomes a problem only when, in its context, it causes an obstacle or disadvantage.
- **Do not conflate motivation with problem.** "We want X" is motivation; "X is not possible because Y" is a problem.
  - Stating motivation alone does not motivate the reader. Only when the problem is explicit does the value of the findings become clear.
- **Dig into the cause of the problem.** "X is slow" -> "We made X faster" is low on information content.
  - "X suffers from performance degradation due to Y" -> "Z, which eliminates Y, proves effective" -- this makes the technical issue explicit.

#### Facts vs. Opinions

- Clearly separate facts (claims backed by sources) from opinions (author's evaluation or speculation).
- State facts in the body text with source attribution.
- Concentrate subjective evaluations ("X is practical," "X is promising," etc.) in the Impact or Limitations sections.
  - There is nothing wrong with stating opinions. Mixing them with facts is the problem.
- When sources disagree, present both positions and make the point of contention explicit.

#### Clarity

- One topic per sentence. Pay attention to sentence boundaries.
- Introduce technical terms with explanation on first use. Terms the intended reader certainly knows need no explanation.
- Prefer plain language, but not at the expense of precision.
- Each chapter should be readable on its own, while also showing connections to other chapters (the storyline).

#### Tables and Figures

Including tables in the report allows readers to grasp the main message without reading the body text.

- **If comparison is the point, make a comparison table**: Comparing technologies, methods, or products is immediately clear in tabular form.
- **If quantitative data exists, present it in a table**: Do not bury numbers in prose; make them stand as independent tables.
- **Add a caption (summary description) to every table**: This ensures the table is self-contained.
- **Reference tables in the body text**: Explaining the same point in different forms (table + prose) reinforces the message.
- Design tables before writing prose. Writing becomes simpler when you can reference tables while explaining.

---

## Rules

### Integrity
1. **Never fabricate sources**: If you cannot find information, say so explicitly.
2. **Always cite**: Every factual claim must have a source URL.

### Process
3. **User control**: Always pause for user confirmation between Phase 1 and Phase 2.
4. **Transparency**: Show what you searched for and what you found.
5. **Scope discipline**: Stay within the approved research plan. If you discover an important tangent, ask the user before pursuing it.

### Language
6. **Output in Japanese**: Always write the final report and all user-facing output in Japanese. Search queries may be in English or Japanese depending on what yields better results, but all findings, evidence cards, progress updates, and the final report must be in Japanese.

---

## Appendix: Context Management

To avoid exhausting the context window during long research sessions:

1. **Write intermediate files**: Each phase has an output step that saves results to a file (see Step 2e and Step 3e). Always execute these steps before moving on.
2. **Reference from files**: In Phase 4, read back from the evidence and verification files rather than relying on conversation history.
3. **Keep searches focused**: Prefer 2-3 precise searches over 10 broad ones. A specific query like "Claude Code WebSearch tool 2026" yields better results than "Claude Code features".
4. **Summarize before proceeding**: At the end of each phase, briefly summarize key findings in 3-5 bullet points before moving on. This creates natural checkpoints that help maintain coherence even if earlier details leave the context window.
