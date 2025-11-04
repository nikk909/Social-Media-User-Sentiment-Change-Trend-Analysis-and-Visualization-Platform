# Social Media User Sentiment Trend Analysis & Visualization Platform

Upgrade from static snapshots to dynamic trends: track how user sentiment changes over time with interactive charts. Ideal for marketing campaign analysis, policy topics tracking, and mental health research.

## Overview (Pain Points We Solve)
- Static, single-time analyses can’t answer whether sentiment is improving or worsening over time.
- This platform streamlines the full pipeline: data collection → cleaning → sentiment classification → time-window aggregation → interactive visualization, focusing on sentiment trends and time-sliced hot words.

## Core Features
- **Parallel data collection**: Concurrent web scraping across multiple topics/time ranges from platforms like Weibo and Twitter (with timestamp and source platform).
- **Parallel text cleaning**: Batch processing for denoising, emoji normalization, deduplication; retain timestamps for time-series analysis.
- **Parallel sentiment analysis**: Multi-core classification of positive/negative/neutral sentiment (VADER/TextBlob; optional lightweight BERT with GPU support).
- **Parallel time-series trends**: Concurrent aggregation by hour/day, compute pos/neg/neu ratios, extract hot words (TF-IDF) across time windows.
- **Interactive visualization**: Plotly charts + Streamlit UI (trend lines, word clouds, optional heatmap).

## Quick Start
### 1) Install dependencies
```bash
# Python 3.9+ recommended
python -m venv .venv
. .venv/bin/activate  # Windows: .venv\Scripts\activate
pip install -U pip
pip install pandas scikit-learn textblob vaderSentiment plotly streamlit joblib
# Note: multiprocessing and concurrent.futures are built-in Python modules
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

## Parallel Design Philosophy

This platform is built with **parallel programming principles** to maximize performance and scalability across compute-intensive stages:

### Parallelization Strategy

1. **Data Collection (Multi-threaded/Multi-process)**
   - Concurrent web scraping across multiple topics/time ranges
   - Parallel API requests with connection pooling
   - Independent worker processes for different platforms (Weibo, Twitter, etc.)

2. **Text Preprocessing (Data Parallelism)**
   - Batch processing with chunked data splitting
   - Parallel cleaning operations (deduplication, normalization) across CPU cores
   - Pipeline parallelism: cleaning → tokenization → feature extraction

3. **Sentiment Analysis (Task Parallelism)**
   - Parallel sentiment classification for independent text samples
   - Multi-core CPU utilization (ProcessPoolExecutor/multiprocessing)
   - GPU acceleration support for transformer models (optional)

4. **Time-Window Aggregation (Map-Reduce Pattern)**
   - Distributed aggregation across time windows
   - Parallel TF-IDF computation per window
   - Concurrent statistical calculations (pos/neg/neu ratios)

### Implementation Approach

- **Multiprocessing**: CPU-bound tasks (sentiment analysis, TF-IDF) use process pools
- **Multithreading**: I/O-bound tasks (data collection, file I/O) use thread pools
- **Chunking & Batching**: Large datasets are split into chunks for parallel processing
- **Lock-free Data Structures**: Thread-safe queues and shared memory for inter-process communication

### Performance Benefits

- **Throughput**: Process 10x-100x more data per unit time compared to sequential processing
- **Scalability**: Linear speedup with additional CPU cores (up to optimal parallelism threshold)
- **Resource Utilization**: Efficient use of multi-core systems and distributed clusters

## Tech Stack
- Collection: Requests + BeautifulSoup / minimal Scrapy (with multiprocessing support).
- Processing: Pandas (groupby/resample/statistics) + parallel chunk processing.
- Parallelization: Python `multiprocessing`, `concurrent.futures`, `joblib`.
- Sentiment: VADER/TextBlob (CPU-friendly, parallelized); optional lightweight BERT (e.g., ALBERT) with GPU support.
- Features: scikit-learn (TF-IDF) with parallel computation.
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
- Parallel processing effectiveness depends on CPU core count; optimal performance on multi-core systems (4+ cores recommended).
- Not real-time by default: designed for "batch import + parallel analysis"; schedulers can be added later.
- Lightweight models can struggle with sarcasm/irony.
- Memory usage scales with parallelism level; large datasets may require chunked processing.

## Future Enhancements
- Better Chinese/multilingual sentiment models with few-shot adaptation.
- Stronger topic discovery (LDA/Top2Vec) and topic evolution views.
- Near-real-time incremental collection with rolling windows; multi-topic dashboards.
- Rich exports: PNG/HTML charts and one-click report generation.
- **Distributed computing**: Support for distributed task queues (Celery, Dask) and cluster deployment.
- **GPU acceleration**: Full GPU support for transformer-based sentiment models and batch processing.
- **Dynamic parallelism**: Auto-tuning parallel workers based on system load and data size.

---
First-time users: focus on “Quick Start” and “How to Use” to quickly run “dynamic sentiment trend analysis + interactive visualization.”

