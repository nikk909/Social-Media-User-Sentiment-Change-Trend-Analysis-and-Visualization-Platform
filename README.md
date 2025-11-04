# Social Media User Sentiment Trend Analysis & Visualization Platform

Upgrade from static snapshots to dynamic trends: track how user sentiment changes over time with interactive charts. Ideal for marketing campaign analysis, policy topics tracking, and mental health research.

## Overview (Pain Points We Solve)
- Static, single-time analyses can’t answer whether sentiment is improving or worsening over time.
- This platform streamlines the full pipeline: data collection → cleaning → sentiment classification → time-window aggregation → interactive visualization, focusing on sentiment trends and time-sliced hot words.

## Core Features
- Data collection: crawl public posts by topic/time range from platforms like Weibo and Twitter (with timestamp and source platform).
- Text cleaning: denoise, normalize emojis, deduplicate; retain timestamps for time-series analysis.
- Sentiment analysis: classify positive/negative/neutral (VADER/TextBlob; optional lightweight BERT).
- Time-series trends: aggregate by hour/day, compute pos/neg/neu ratios, extract hot words (TF-IDF).
- Interactive visualization: Plotly charts + Streamlit UI (trend lines, word clouds, optional heatmap).

## Quick Start
### 1) Install dependencies
```bash
# Python 3.9+ recommended
python -m venv .venv
. .venv/bin/activate  # Windows: .venv\Scripts\activate
pip install -U pip
pip install pandas scikit-learn textblob vaderSentiment plotly streamlit
```

### 2) Prepare data (choose one)
- Option A: use the built-in collectors (coming later) to export `data/raw.jsonl`.
- Option B: prepare CSV/JSONL manually with at least: `raw_text`, `post_time`, `platform`.

### 3) Run the pipeline (example)
```bash
# Preprocessing (cleaning/dedup/time normalization)
python tools/preprocess.py --in data/raw.jsonl --out data/clean.jsonl

# Sentiment (VADER by default)
python tools/sentiment.py --in data/clean.jsonl --out data/sent.jsonl --backend vader

# Time-window aggregation + hot words
python tools/aggregate.py --in data/sent.jsonl --window 1D --out data/agg.csv
```

### 4) Launch the app
```bash
streamlit run app.py
```
- In the browser, select topic/time range/window size to view trend lines and word clouds.

## How to Use (Beginner Friendly)
1. Enter topic keywords and time range; import or collect data.
2. Click “Start Analysis” to run cleaning, sentiment classification, and aggregation.
3. Explore the UI:
   - Trend lines (pos/neg/neu ratios over time)
   - Word clouds per time window or sentiment
   - Optional heatmap
4. Hover to see exact values; export images/data if enabled.

## Tech Stack
- Collection: Requests + BeautifulSoup / minimal Scrapy.
- Processing: Pandas (groupby/resample/statistics).
- Sentiment: VADER/TextBlob (CPU-friendly); optional lightweight BERT (e.g., ALBERT).
- Features: scikit-learn (TF-IDF).
- Visualization: Plotly (interactive) + Streamlit (rapid UI).

## Sample Screens (Expected)
- Trend lines: three curves for pos/neg/neu ratios with zoom and hover tooltips.
- Word clouds: switch across time windows or sentiment classes.
- Optional heatmap: sentiment intensity across windows or subtopics.

> Note: No images included yet. After running with your data, take screenshots from the UI.

## Use Case Examples
- Marketing: input a product’s release time window, compare pre/post-release curves, and inspect negative peaks via hot words to locate issues.
- Policy: monitor a policy topic’s sentiment trajectories and heat to inform communication strategy and timing.
- Mental health research: long-term tracking for a specific community/topic to identify negative peaks and triggers for targeted intervention.

## Known Limitations
- Single-topic analysis initially; no parallel multi-topic comparison.
- Not real-time by default: designed for “batch import + analysis”; schedulers can be added later.
- Lightweight models can struggle with sarcasm/irony.

## Future Enhancements
- Better Chinese/multilingual sentiment models with few-shot adaptation.
- Stronger topic discovery (LDA/Top2Vec) and topic evolution views.
- Near-real-time incremental collection with rolling windows; multi-topic dashboards.
- Rich exports: PNG/HTML charts and one-click report generation.

---
First-time users: focus on “Quick Start” and “How to Use” to quickly run “dynamic sentiment trend analysis + interactive visualization.”

