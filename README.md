# Social Media Sentiment Trend Analysis Platform

## Problem Statement

Traditional static sentiment analysis only provides single-point snapshots, unable to capture how public sentiment changes over time. This limitation hinders trend understanding in areas such as marketing campaign effectiveness, policy impact, and public mental health research.

## Project Goal

Develop a lightweight platform that integrates the full pipeline from social media data collection to interactive visualization, focusing on tracking dynamic sentiment trends (positive / negative / neutral) over time and identifying hot topics across different time periods.

## Core Features

- **Data Collection**: Parallel data collection from multiple social media platforms (e.g., Weibo, Twitter)
- **Text Preprocessing**: Cleaning, deduplication, and normalization
- **Sentiment Analysis**: Classification of text into positive/negative/neutral categories
- **Time Window Aggregation**: Statistics of sentiment ratios by time windows and extraction of hot keywords
- **Interactive Visualization**: Display sentiment trend curves and word clouds

## Parallel Computing Applications

This platform uses parallel computing to improve processing efficiency, primarily applied in the following four areas:

### 1. Data Collection
Multi-threaded web scraping and parallel API requests to collect data from multiple platforms simultaneously.

### 2. Text Preprocessing
Data parallel strategy: split data into chunks and execute cleaning, deduplication, and normalization operations in parallel across multiple CPU cores.

### 3. Sentiment Analysis
Task parallelism using process pools to classify independent text samples simultaneously; optional GPU acceleration for transformer models.

### 4. Time Window Aggregation
Map-Reduce pattern for distributed computation of sentiment ratios, with parallel TF-IDF computation across time windows.

## Tech Stack

- **Data Collection**: Requests + BeautifulSoup, with multi-threading support
- **Data Processing**: Pandas, with parallel chunk processing support
- **Parallel Computing**: Python `multiprocessing`, `concurrent.futures`, `joblib`
- **Sentiment Analysis**: VADER/TextBlob (with parallel support); optional BERT models (with GPU support)
- **Feature Extraction**: scikit-learn TF-IDF (with parallel computation support)
- **Visualization**: Plotly (interactive charts) + Streamlit (rapid UI development)

## Future Enhancements

- Predictive analysis of sentiment trends after hot events
- Extraction of the most common attitude words from the public regarding specific events
