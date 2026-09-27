# Automated RCA Engine — Multi-Agent Review Analysis

A multi-agent system that scrapes e-commerce customer reviews and automatically surfaces recurring product defects — turning unstructured customer feedback into structured, actionable Root Cause Analysis (RCA) before issues escalate into batch-level returns.

## Problem

D2C brands generate a constant stream of customer reviews, but reading through hundreds of them manually to spot patterns is slow and inconsistent. By the time a defect (a bad zipper batch, a sizing drift on one SKU) is noticed through returns data, it's often already shipped to hundreds of customers. The goal was to catch these patterns at the review stage — earlier, cheaper, and before the return.

## What it does

- Scrapes and processes **500+ customer reviews weekly** across **10 product lines**
- Uses a multi-agent pipeline (built with **CrewAI**) where specialized agents handle scraping, classification, and pattern-clustering separately, then hand off findings
- Surfaces **4-5 recurring, high-confidence defect patterns per cycle** — e.g., zipper failures on a specific product line, sizing mismatches on a specific SKU range — instead of a flat list of complaints
- Outputs a structured RCA summary a merchandising or QC team can act on directly, rather than raw review text

## Why multi-agent instead of a single LLM call

A single prompt asked to "find issues in these reviews" tends to produce generic, low-signal output (e.g., "some customers were unhappy"). Splitting the task across agents — one to classify review topics, one to cluster recurring complaints, one to filter for statistical recurrence rather than one-off noise — produced sharper, more specific findings that matched what a human analyst would flag.

## Architecture

```
Review Sources → Scraping Agent → Classification Agent → Clustering Agent → RCA Summary
                                                                  ↓
                                                    (recurrence threshold filter)
```

- **Scraping Agent** — pulls new reviews per product line on a weekly cadence
- **Classification Agent** — tags each review by defect category (fit, material, function, delivery, etc.)
- **Clustering Agent** — groups tagged reviews by product line + category, flags clusters that cross a recurrence threshold
- **Output** — a structured summary: defect, affected product line(s), review count, example excerpts

## Stack

Python · CrewAI (multi-agent orchestration) · OpenAI API · Pandas for aggregation

## What I'd improve next

- Add a severity-weighting step (a review mentioning a safety issue should outrank one mentioning color mismatch, even at lower volume)
- Feed confirmed recurring defects back into a lightweight tracker so the same issue isn't re-surfaced every week once a team has already actioned it
- Test agent handoff reliability at higher review volume (current design is validated at ~500/week; hasn't been stress-tested beyond that)

---
*Built as part of independent AI/analytics consulting work, Jan 2024–present.*
