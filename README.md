# rdp — trapX research/Colab notebooks

## Notebooks

### `Counter_Fibo_Advisory.ipynb` (recommended)

End-to-end LLM advisory for **every** counter-fibo 100% completion event in a trapX day-log. Runs the same engineered pipeline as the production agent (CHA-168 / CHA-169 / CHA-170) and produces a **labelled 10-stage trace** per event — matching the production `verbose_trace.py` output exactly.

#### Setup stages (cells 1-5)

| Stage | What |
|---|---|
| 1 | Install Ollama + start server |
| 2 | Clone trapX to get the skill file |
| 3 | Mount Google Drive + point at day folder |
| 4 | Load helper functions |
| 5 | Discover all counter-fibo 100% events in the day-log |

#### Per-event 10-stage trace (cell 6.2)

For each event, the loop emits the same 10 stages as the production CHA-170 verbose trace tool, with `[YYYY-MM-DD HH:MM:SS.ffffff]` IST timestamps on every line and per-stage `duration_ms`:

| Stage | Section |
|---|---|
| 1 | Trigger event + inputs at the bar |
| 2 | Snapshot context build (geometry + window facts) |
| 3 | Journey dataset (parsed from log; same shape as production DB-sourced) |
| 4 | Merged user_message JSON (first 1200 chars previewed) |
| 5 | Skill file load (section headers shown) |
| 6 | Live LLM call (model, tokens, latency, raw response) |
| 7 | Response parse (verdict / score / action) |
| 8 | Score-sign enforcement (rubric + decision) |
| 9 | Render multi-line `🤖 LLM advisory:` block |
| 10 | Audit record (full payload + parsed response) |

Each event's full trace is also written to `{DAY_DIR}/llm_advisory_e2e_trace_<pivot>_<runtime>.log` so you can review later.

#### Usage

```python
# Cell 3.2 — change for other dates
DAY_DIR  = "/content/drive/MyDrive/VM-4-logs/May_14"
LOG_FILE = f"{DAY_DIR}/trapx_20260514_090139.log"

# Cell 6.1 — backend
BACKEND = "ollama"           # or "anthropic"
MODEL   = "llama3.2:3b"      # or "claude-sonnet-4-5"
EVENT_INDICES = None         # e.g. [1, 3] for events #1 and #3; None = all
```

For Anthropic (Claude) instead of Ollama:
1. Sidebar (lock icon) → Add new secret: `ANTHROPIC_API_KEY` = `sk-ant-...`
2. Change `BACKEND = "anthropic"` and `MODEL = "claude-sonnet-4-5"`

#### Model-quality table — pick based on how reliable you need the verdict

| Model | Cost | Follows 3-line contract? | Use for |
|---|---|---|---|
| `llama3.2:3b` (Ollama, default) | free | ❌ often emits markdown analysis instead of the 3-line shape — falls back to salvage parser | quick smoke-test that the pipeline runs end-to-end |
| `qwen2.5:7b` (Ollama) | free | ✓ much better instruction-following | recommended for free local runs |
| `claude-sonnet-4-5` (Anthropic) | paid | ✓ same model the production agent uses | recommended for actual research-grade verdicts |

The notebook degrades gracefully when the LLM breaks the contract:

* **Strict parse fails** → a **salvage parser** scans the response for directional keywords (BUY/SELL/REAL V/TRAP/MIXED/bullish/bearish), the first numeric in [-1, +1], and the "Recommendation:" paragraph — synthesises a usable verdict.
* **Salvage also fails** → the renderer prints `❓[UNPARSED]` with the raw response excerpt so you see what the LLM actually said.
* Stage 7 of the trace flags `⚠️ SALVAGE PARSE` when the fallback fires.

[Open in Colab](https://colab.research.google.com/github/Chanakya1998begin/rdp/blob/main/Counter_Fibo_Advisory.ipynb)

### `Test_ollama.ipynb` (legacy)

Early experiment that dumped the entire day-log into the LLM prompt and asked about the 11:20 bar. Superseded by `Counter_Fibo_Advisory.ipynb` (kept for reference).

[Open in Colab](https://colab.research.google.com/github/Chanakya1998begin/rdp/blob/main/Test_ollama.ipynb)
