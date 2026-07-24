# Evaluating Prompts

A small evaluation harness for testing prompts against Claude, built around a single use case: breaking a high-level task down into sub-tasks.

The project follows a basic prompt-eval pattern — generate a dataset of test cases, run a candidate prompt against each case, grade the outputs, and persist everything for inspection.

## How it works

Everything lives in [dataset.ipynb](dataset.ipynb), which walks through the pipeline step by step:

1. **Generate a dataset** (`generate_dataset`) — asks Claude to invent 10 realistic productivity/agent tasks (e.g. "Plan and organize a company team building event for 50 people"). The result is saved to [dataset.json](dataset.json).
2. **Run the prompt under test** (`run_prompt`) — takes a test case and asks Claude to break that task into sub-tasks. This is the prompt being evaluated.
3. **Score each case** (`run_test_case`) — runs the prompt and wraps the output with its test case and a score. Grading is currently a placeholder (`score = 10` for every case) — a real grading step (e.g. a model-based judge or rubric) is a TODO.
4. **Run the full evaluation** (`run_eval`) — loads `dataset.json`, runs every test case through `run_test_case`, and collects the results.
5. **Persist results** — the full set of outputs, test cases, and scores is saved to [results.json](results.json) for review.

## Model

Uses [`claude-haiku-4-5`](https://docs.claude.com/) via the `anthropic` Python SDK, called through a minimal `chat()` helper that supports system prompts, temperature, and stop sequences.

## Project structure

```
dataset.ipynb    # the full pipeline: generate dataset -> run prompt -> grade -> save results
dataset.json     # generated evaluation dataset (task descriptions)
results.json     # prompt outputs + scores for each test case
pyproject.toml   # project metadata and dependencies (managed with uv)
```

## Setup

This project uses [uv](https://docs.astral.sh/uv/) for dependency management and requires Python >= 3.14.

```bash
uv sync
```

Create a `.env` file in the project root with your Anthropic API key:

```
ANTHROPIC_API_KEY=your-key-here
```

Then open [dataset.ipynb](dataset.ipynb) in Jupyter and run the cells in order.

## Status / next steps

- Grading is a stub (`score = 10` always) — the next step is to implement real grading, e.g. an LLM-as-judge prompt or a rubric-based scorer.
- The dataset and prompt are currently scoped to a single task type (task → sub-task breakdown); the harness is generic enough to swap in other prompts/tasks.
