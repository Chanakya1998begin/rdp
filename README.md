# rdp — trapX research/Colab notebooks

## Notebooks

### `Counter_Fibo_Advisory.ipynb` (recommended)

End-to-end LLM advisory for **every** counter-fibo 100% completion event in a trapX day-log. Runs the same engineered pipeline as the production agent (CHA-168 / CHA-169 / CHA-170):

| Stage | What |
|---|---|
| 1 | Install Ollama + start server |
| 2 | Clone trapX to get the skill file |
| 3 | Mount Google Drive (where day-logs live) |
| 4 | Discover **all** counter-fibo 100% events in the day-log (not just 11:20) |
| 5 | For each event: extract journey dataset from log (signal trajectory, trn_oi trajectory, squeeze cascade, composition at completion) |
| 6 | Build LLM payload in the same shape the production agent sends |
| 7 | Call LLM — Ollama default; Anthropic if `ANTHROPIC_API_KEY` is set in Colab Secrets |
| 8 | Parse 3-line response → deterministic score-sign correction → render multi-line `🤖 LLM advisory:` block |

Default day path: `/content/drive/MyDrive/VM-4-logs/May_14/trapx_20260514_090139.log` — change `DAY_DIR` and `LOG_FILE` in Stage 3 for other dates.

[Open in Colab](https://colab.research.google.com/github/Chanakya1998begin/rdp/blob/main/Counter_Fibo_Advisory.ipynb)

### `Test_ollama.ipynb` (legacy)

Early experiment that dumped the entire day-log into the LLM prompt and asked about the 11:20 bar. Superseded by `Counter_Fibo_Advisory.ipynb` (kept for reference).

[Open in Colab](https://colab.research.google.com/github/Chanakya1998begin/rdp/blob/main/Test_ollama.ipynb)
