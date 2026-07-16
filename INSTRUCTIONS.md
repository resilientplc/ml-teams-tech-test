# ML Engineer — Take-Home Technical Test

## Overview

We know you're busy, so we've designed this test to take approximately **2.5–3 hours**. Please don't spend significantly more than that — we're not looking for a production system, and a clear, well-reasoned partial solution tells us more than an over-engineered one.

This test is a simplified reflection of work we do day-to-day: ingesting and transforming data, building models to detect suspicious caller behaviour, and thinking carefully about how those models perform.

Please write your solution in **Python**. If you use AI to assist you, make sure you understand the overall solution, including any AI contributions, and are able to defend all parts of it in a code review.   

---

## Submission

- Use a **git repository** and make **more than one commit** — we'd like to see your thought process, not just the end result
- Include a **README** explaining how to run your solution end-to-end
- Send us a link to your **private repository** (adding `mbeveridge-resilient` as a collaborator) or send a zipped-up copy of your whole local repository to `m.beveridge@smartnumbers.com`.

Please reach out if you have any questions.

---

## Part 1 — Data Pipeline

### Context

In the ML team, a typical task involves ingesting raw data from multiple sources, transforming and enriching it, and producing a clean dataset for downstream use — whether that's a report, a model training job, or a monitoring dashboard.

### Task

Build a program that reads two input files and produces a single CSV file. The input data files are provided in the `data` directory of this repo.

### Input: `data/calls.json`

A JSON document containing records of incoming phone calls. Each call has the following fields:

| Field | Type | Description | Example |
|---|---|---|---|
| `type` | string | Always `"call"` | `"call"` |
| `id` | string | UUID | `"8f1b1354-..."` |
| `attributes.date` | string | UTC datetime in RFC 3339 format | `"2020-10-12T07:20:50Z"` |
| `attributes.number` | string | Phone number in E.164 format — may be absent | `"+44123456789"` |
| `attributes.duration_seconds` | int | Length of the call in seconds — may be null | `183` |
| `attributes.num_calls_last_30_days` | int | Number of calls from this number in the last 30 days — may be null | `7` |
| `attributes.time_of_day` | string | Period of day: `morning`, `afternoon`, `evening`, or `night` | `"evening"` |
| `attributes.label` | int | Ground truth: `1` = suspicious, `0` = not suspicious | `0` |

> **Note:** The data contains a number of real-world quality issues. Part of the task is identifying and handling these appropriately. We'd like to see your decisions documented.

### Input: `data/operators.json`

A JSON document containing phone operator prefix mappings. A prefix of `"3000"` means any national number whose first four digits fall in the range `3000–3999` belongs to that operator:

- `+443642728615` → national number `3642728615` → prefix `3642` → in range `3000–3999` → **EE**
- `+448423666777` → national number `8423666777` → prefix `8423` → in range `8000–8999` → **Orange**

### Output

Produce a CSV file at `output/calls.csv` with the following columns:

```
id,date,number,operator,duration_seconds,num_calls_last_30_days,time_of_day,label
```

Rules:

- `date` should be formatted as `YYYY-MM-DD`
- `number` should be `Withheld` if absent
- `operator` should be `Unknown` if no matching prefix is found
- Rows should be ordered by ascending date
- Rows with malformed or unparseable dates should be excluded from the output — document how many were dropped and why
- Duplicate call IDs should be deduplicated — document your strategy for which record to keep

---

## Part 2 — Classifier

### Task

Using the CSV produced in Part 1, train a binary classifier to predict `label` (1 = suspicious call, 0 = not suspicious). We are deliberately leaving the choice of algorithm and approach open. 

**Do not worry about achieving good prediction performance** — the data provided is a small synthetic dataset so that will likely not be possible. We just want to see your approach and reasoning in attempting to build a model (however good or bad it is). 

### What we're looking for

**Feature choices** — which columns do you use, and how do you prepare them? Not all columns in the CSV will necessarily make good features.

**Handling missing values** — some rows have nulls in `duration_seconds` and `num_calls_last_30_days`. Please document your imputation or exclusion strategy and why you chose it.

**Train/test methodology** — use an appropriate evaluation methodology and be prepared to explain the tradeoffs of your choice.

**Metric selection** — choose an evaluation metric (or metrics) appropriate for this problem and justify your choice.

**Reporting** — include a short written summary in the README covering:
  - Your evaluation approach
  - Your evaluation results
  - Anything you'd change or investigate with more time

### Constraints

- Your solution should run on a standard laptop without a GPU
- Keep dependencies reasonable — standard scientific Python stack is fine (`scikit-learn`, `pandas`, `numpy`, etc.)

---

## What we assess

| Area | What we look for |
|---|---|
| Code quality | Clarity, structure, appropriate use of abstractions |
| Data handling | Awareness and documentation of quality issues |
| ML reasoning | Metric choice, methodology, honest evaluation |
| Communication | README quality, documented decisions |
| Testing | Relevant test coverage of core logic |

We review your submission together in a follow-up interview, where we'll ask you to walk us through your approach and discuss the decisions you made.
