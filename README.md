# Employee Task Prioritization System

A Streamlit app that ranks uploaded tasks with XGBoost's learning-to-rank model and produces a downloadable PDF summary per employee. Built during an AI/ML internship at Cybersecurity Umbrella.

**Portfolio:** [janvikgabani.github.io](https://janvikgabani.github.io)

## What it does

Upload a CSV or PDF of tasks, pick an employee, and get their work ordered by what to do first, plus a PDF to hand over.

## Method

Ranking rather than classification is the right framing here. The question is not "is this task high priority" in isolation — it is "of this person's tasks, which comes first". That is a per-employee ordering problem, so each employee's tasks form a **query group** and the model learns to rank within the group.

- **Model** — `XGBRanker` with `objective='rank:pairwise'`, learning rate 0.1, max depth 5, 200 estimators. Pairwise ranking learns from comparisons between tasks in the same group rather than from absolute labels.
- **Features** — priority encoded as Low/Medium/High → 0/1/2, and total estimated time normalised to minutes.
- **Input** — CSV directly, or PDF parsed with PyPDF2.
- **Output** — the ordered task list and a generated PDF summary via `fpdf`.

## Status

The Streamlit app body in this file is **commented out** — it was written to be dumped to `app.py` from a Colab cell and served through an ngrok tunnel, which is how it was demoed. To run it, uncomment the app section and save it as its own `app.py`.

```bash
pip install -r requirements.txt
streamlit run app.py
```

The ngrok auth token that was in the original file has been removed. Supply your own if you want to tunnel it, or just run Streamlit locally and skip ngrok entirely.

## Honest notes

- **Two features is very few** for a ranking model. With only priority and estimated time, a sorted `ORDER BY priority DESC, time ASC` would get close to the same ordering. The model earns its place only once richer features are added — due date, dependencies, client tier, how long the task has been open.
- **PDF parsing is shallow.** `extract_pdf_features` takes the first 200 characters as the task description and fills priority, time and employee with defaults. CSV is the reliable input path.

## Files

| Path | What it is |
|---|---|
| `task_prioritization_app.py` | Setup, the ranking model, PDF generation and the Streamlit UI (exported from the original Colab notebook) |

## Related

Part of the same internship as [task-summarizer](https://github.com/JanviKGabani/task-summarizer), which summarises the tasks this one ranks.
