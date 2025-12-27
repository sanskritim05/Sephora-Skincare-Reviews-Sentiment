# Sephora Skincare: What’s Actually Worth It?

This project uses Sephora skincare reviews to rank products and brands in a simple Streamlit dashboard.

There are two main parts:
- **`sephora_sentiment_worth_it.ipynb`**: builds the dataset and scoring.
- **`app.py`**: shows the results in an interactive app.

---

## What the notebook does (`sephora_sentiment_worth_it.ipynb`)

1. **Loads the data**
   - Reads all review files from `data/reviews_*.csv`
   - Reads product metadata from `data/product_info.csv`
   - Merges them using `product_id`

2. **Cleans review text**
   - Lowercases text, removes HTML/links, removes extra symbols/whitespace

3. **Trains a sentiment model**
   - Uses star rating as a label:
     - 1–2 = negative, 3 = neutral, 4–5 = positive
   - Model: **TF-IDF + Logistic Regression**
   - Evaluates on a train/test split

4. **Predicts sentiment for all reviews**
   - Adds predicted sentiment to each review
   - Creates `is_positive` (1 if predicted positive, else 0)

5. **Aggregates to product level**
   - Calculates per product:
     - number of reviews
     - average rating
     - positive review rate
     - average price (if available)

6. **Applies Bayesian smoothing**
   - Prevents products with few reviews from ranking unrealistically high/low

7. **Builds the Worth It Score**
   - Combines:
     - Bayesian-adjusted rating
     - Bayesian-adjusted positive rate
     - a small value boost based on price

8. **Saves outputs**
   - `outputs/product_rankings.csv` (used by the app)
   - `models/sentiment_model.joblib` (saved model)

---

## What the app does (`app.py`)

The Streamlit app reads `outputs/product_rankings.csv` and shows two tabs:

### Products tab
- Filter by brand, minimum reviews, and search by product name
- Sort by worth it score, rating, review count, or price
- View:
  - quick stats
  - top 10 chart
  - product table + CSV download
  - product details + score explanation

### Brands tab
- Ranks brands using review-weighted averages across their products
- View:
  - top brands chart
  - brand table + CSV download
  - brand details and top products for that brand

---

## How to run the project

### 1) Install dependencies
```bash
pip install -r requirements.txt
```
## 2) Put the dataset in the right folders
Your project should include the following files:
data/
├── product_info.csv
├── reviews_0-25000.csv
├── reviews_25000-50000.csv
└── reviews_*.csv
(The review files may be split into multiple chunks depending on the dataset.)

## 3) Run the notebook (creates the files the app needs)
Open and run the notebook:
sephora_sentiment_worth_it.ipynb

Running the notebook will generate the following files:
outputs/
└── product_rankings.csv
models/
└── sentiment_model.joblib

## 4) Run the Streamlit app
streamlit run app.py

