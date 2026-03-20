# TikTok Customer review analysis

This project scrapes TikTok (Google Play) reviews for a chosen time window, cleans and stores them for reuse, then generates an interactive HTML report (`review.html`).

## Live HTML Pages (GitHub Pages)

- Root SPA: [index.html](https://adrianwwwang.github.io/Tiktok-customer-review/)
- Dedicated VSC view: [review_vsc.html](https://adrianwwwang.github.io/Tiktok-customer-review/review_vsc.html)
- Dedicated Cursor view: [review_cursor.html](https://adrianwwwang.github.io/Tiktok-customer-review/review_cursor.html)

## Setup

```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
python -m playwright install chromium
```

## Run

Fetch (raw JSONL; API-based recommended):

```bash
python scrape/fetch_google_play_reviews_api.py \
  --start-date 2025-09-01 --end-date 2026-03-31 \
  --max-reviews 200000 --batch-size 200 \
  --out data/reviews_raw.jsonl --locale en_US
```

Clean + store (Parquet + CSV):

```bash
python scrape/clean_reviews.py \
  --in data/reviews_raw.jsonl \
  --start-date 2025-09-01 --end-date 2026-03-31 \
  --parquet data/reviews.parquet --csv data/reviews.csv
```

Analyze + generate report:

```bash
python scrape/analyze_reviews.py \
  --in data/reviews.parquet --out review.html \
  --start-date 2025-09-01 --end-date 2026-03-31
```

Open `review.html` in your browser.

## Notes

- Scraping uses an API-based approach by default (`scrape/fetch_google_play_reviews_api.py`).
- There is also a Playwright browser automation scraper (`scrape/scrape_google_play_reviews.py`) if you need it.
- Pain points are derived via TF-IDF + NMF topic clustering on low-rating reviews (≤2★), grouped by detected language when possible.
- The report is rendered in a “Cyber-Tech” dark mode and includes a refined TF-IDF/stop-word Word Cloud from low-rating reviews (≤2★).
