# Cathay Pacific Passenger Reviews: Sentiment Annotation, Rating Distribution, and Automatic Title Generation

An annotated dataset of **1,500 Cathay Pacific passenger reviews** scraped from [Skytrax](https://www.airlinequality.com/), together with the full preprocessing and exploratory analysis pipeline. The data is prepared for supervised NLP tasks: **abstractive summarization**, **review title generation**, and **sentiment classification**.

This is a group final project from the Information Systems program at Institut Teknologi PLN (academic year 2025/2026). The dataset structure follows the approach of [Syed et al. (2023), *Data in Brief*](#references).

---

## Table of Contents

- [Overview](#overview)
- [Dataset](#dataset)
- [Repository Structure](#repository-structure)
- [Methodology](#methodology)
- [Key Findings](#key-findings)
- [Getting Started](#getting-started)
- [Limitations](#limitations)
- [Ethics Statement](#ethics-statement)
- [Team](#team)
- [References](#references)

---

## Overview

Customer reviews are a rich source of opinion data, but individual reviews are often long, repetitive, or bury the main point under too much detail. Summarization models trained on general domains (news, product reviews) tend to lose performance on airline content, a problem known as **domain shift**. This project provides a clean, domain-specific, labeled resource focused on a single airline so that NLP researchers do not have to start from scratch.

**What this repository provides:**

- Two labeled datasets built from the same 1,500 reviews
- A reproducible Jupyter notebook covering preprocessing, annotation, and visualization
- Nine figures that describe the structure and sentiment of the data
- A written paper describing the dataset (in Indonesian)

---

## Dataset

Both datasets come from the same pool of Cathay Pacific reviews, share the same preprocessing pipeline, and are split by intended use.

| Specification | Details |
| --- | --- |
| Subject | Artificial Intelligence, Data Science |
| Specific area | NLP: abstractive summarization and sentiment analysis on airline reviews |
| Data type | Text, tables |
| Data format | Raw, labeled, analyzed |
| Source | Skytrax, https://www.airlinequality.com/ |
| Size | 1,500 reviews per dataset |
| Language | English |
| File format | `.xlsx` |

### 1. `review_titles_data`

Pairs each review with its original title and an automatically computed sentiment polarity. Intended for **title generation** and **abstractive summarization**.

| Column | Description |
| --- | --- |
| `Title` | Original review title written by the customer |
| `Review` | Original review text |
| `title_clean` | Preprocessed title (lowercased, punctuation removed, stop words removed, lemmatized) |
| `review_clean` | Preprocessed review text |
| `polarity` | TextBlob polarity score, from -1.0 (most negative) to 1.0 (most positive) |

### 2. `annotated_sentiment_dataset`

Adds structured labels derived from the customer's own rating. Intended for **sentiment classification**.

| Column | Description |
| --- | --- |
| `review` | Full review text |
| `title` | Review title |
| `rating` | Overall rating given by the customer, from 1 (worst) to 10 (best) |
| `recommended` | Whether the reviewer recommends the airline (`1` = yes, `0` = no) |
| `sentiment` | Sentiment class derived from `rating` (see mapping below) |

**Sentiment label mapping**

| Rating | Sentiment class | Label |
| --- | --- | --- |
| 1 to 4 | Negative | `0` |
| 5 to 6 | Neutral | `1` |
| 7 to 10 | Positive | `2` |

**Class distribution**

| Total | Negative (0) | Neutral (1) | Positive (2) |
| --- | --- | --- | --- |
| 1,500 | 423 | 185 | 892 |

> **`sentiment` vs. `polarity`:** `sentiment` is a ground-truth category derived from the customer's rating. `polarity` is a continuous score computed by TextBlob from the review text. They measure different things and should not be confused.

### Data Access

The datasets are also available on Google Drive:
[Cathay Pacific dataset folder](https://drive.google.com/drive/folders/1lUP2izXff9XQl_T2aUQcsX3b1zART2zs?usp=sharing)

---

## Repository Structure

```
.
├── README.md
├── ProjectPBD_CathayPacificAirways.ipynb    # Preprocessing, annotation, and visualization
├── data/
│   ├── raw_cathay_pacific_reviews.xlsx      # Raw scraped data
│   ├── review_titles_data.xlsx              # Output: titles + polarity
│   └── annotated_sentiment_dataset.xlsx     # Output: rating, recommendation, sentiment
├── figures/
│   ├── figure1_wordcloud.png
│   ├── figure2_character_distribution.png
│   ├── figure3_word_distribution.png
│   ├── figure4_rating_distribution.png
│   ├── figure5_sentiment_class_distribution.png
│   ├── figure6_customer_recommendation.png
│   ├── figure7_sentiment_polarity.png
│   ├── figure8_bivariate_distribution_plot.png
│   └── figure9_correlation_heatmap.png
└── paper/
    └── Makalah_Final_Project_Cathay_Pacific_ID.docx
```

> The notebook reads and writes files in its working directory. If you move the data files into `data/`, update the paths in the notebook accordingly.

---

## Methodology

### 1. Data Collection

Reviews were collected from the Skytrax airline review portal in two steps:

1. A browser **Web Scraper extension** extracted the main fields from Cathay Pacific review pages (title, review text, overall rating, recommendation).
2. **Python** (`requests` and `BeautifulSoup`) was then used to fill in columns that the extension could not capture reliably.

The merged data was checked for duplicates and missing values, cleaned, validated, and saved as `.xlsx`. Reviews with a missing title or other required field were removed. The final dataset spans multiple routes and cabin classes, and includes recent reviews that reflect post-pandemic service.

### 2. Preprocessing

The `clean_text()` function in the notebook applies the following steps to both titles and reviews:

1. Lowercase the text
2. Remove URLs
3. Remove numbers
4. Remove punctuation
5. Strip extra whitespace
6. Tokenize
7. Remove English stop words (NLTK)
8. Lemmatize (NLTK `WordNetLemmatizer`)
9. Rejoin tokens into clean text

### 3. Annotation

- `sentiment` is assigned from the customer rating using the fixed mapping above.
- `recommended` is converted from `yes`/`no` to `1`/`0`.
- `polarity` is computed with `TextBlob(text).sentiment.polarity`, a lexicon-based method that averages word-level scores from the `pattern.en` dictionary.
- **No manual annotation** was performed on individual reviews.

### 4. Analysis and Visualization

Nine figures are generated by the notebook:

| Figure | Description |
| --- | --- |
| 1 | Word clouds for reviews and titles |
| 2 | Character-length distribution of reviews and titles |
| 3 | Word-count distribution of reviews and titles |
| 4 | Rating distribution |
| 5 | Sentiment class distribution |
| 6 | Recommendation distribution |
| 7 | TextBlob polarity distribution with KDE curve |
| 8 | Bivariate (joint) distribution of rating and recommendation |
| 9 | Correlation heatmap of rating, recommendation, and sentiment |

---

## Key Findings

- **Review length:** Reviews are right-skewed, with most between 200 and 1,000 characters (about 70 to 110 words). Titles are short, typically 4 to 5 words.
- **Rating pattern:** The distribution is bimodal. Ratings of 10, 9, and 1 are the most frequent (299, 255, and 172 reviews), which suggests customers with very good or very bad experiences are the most likely to write.
- **Sentiment balance:** About 59% of reviews are positive and about 28% are negative, so both ends of the spectrum are well represented for model training.
- **Recommendation:** 962 reviewers (64.1%) recommend Cathay Pacific and 538 (35.9%) do not.
- **Polarity:** The mean TextBlob polarity is **0.176**, a moderately positive tendency. Most reviews fall between 0 and 0.4.
- **Correlations:** Sentiment and rating are strongly correlated (0.94), followed by sentiment and recommendation (0.89), and rating and recommendation (0.87).
- **Frequent topics:** Word clouds are dominated by *flight*, *service*, *seat*, *crew*, *cabin*, *food*, and mentions of business and economy class.

---

## Getting Started

### Prerequisites

- Python 3.8+
- Jupyter Notebook, JupyterLab, or Google Colab

### Installation

```bash
git clone https://github.com/<your-username>/<your-repo-name>.git
cd <your-repo-name>

pip install pandas numpy nltk textblob matplotlib seaborn wordcloud scipy openpyxl jupyter
```

Download the required NLTK resources:

```python
import nltk
nltk.download("stopwords")
nltk.download("wordnet")
```

### Running the Notebook

1. Place `raw_cathay_pacific_reviews.xlsx` in the same folder as the notebook (or update the path in the notebook).
2. Open `ProjectPBD_CathayPacificAirways.ipynb`.
3. Run all cells from top to bottom. The notebook will:
   - clean the raw data and build the two output datasets,
   - save `review_titles_data.xlsx` and `annotated_sentiment_dataset.xlsx`,
   - generate and save Figures 1 to 9 as `.png` files.

### Loading the Datasets

```python
import pandas as pd

titles_df = pd.read_excel("review_titles_data.xlsx")
sentiment_df = pd.read_excel("annotated_sentiment_dataset.xlsx")

print(titles_df.shape)      # (1500, 5)
print(sentiment_df.shape)   # (1500, 5)
```

---

## Limitations

- **Rating-derived labels:** Sentiment classes come from a fixed rating threshold, not from manual review of the text. A review's wording can disagree with its rating.
- **TextBlob is lexicon-based:** It matches words against a dictionary and does not understand context, so it can misread sarcasm or ambiguous phrasing.
- **Single airline:** The dataset is intentionally narrow. Models trained on it may not generalize to other airlines without adaptation.
- **Class imbalance:** Positive reviews outnumber neutral and negative ones, so consider class weighting or resampling for classification.
- **Title length artifact:** There is a sharp spike at 36 to 38 characters in title length, which may reflect a platform input limit or template phrasing.

---

## Ethics Statement

Data was collected from the Skytrax review portal solely for academic research. All reviews on the platform are publicly accessible. No personal identifying information beyond what customers voluntarily wrote in their public reviews was collected or stored. The dataset does not include reviewer account information, email addresses, or other contact details.

---

## Team

Information Systems, Faculty of Energy Telematics, Institut Teknologi PLN

- Jaquinta Rahmadias
- Fatmalia
- Amanda Damayanti
- Khoirun Nisak
- Najwa Nadhira
- Petique Bulan Nanindah
- Muhammad Nabil Virzatullah

**Supervising lecturer:** Dr. Puji Catur Siswi Praptini, ST., MTI

---

## References

- Syed, A. A., Gaol, F. L., Boediman, A., Matsuo, T., & Budiharto, W. (2023). A data package for abstractive opinion summarization, title generation, and rating-based sentiment prediction for airline reviews. *Data in Brief, 50*, 109535.
- Bird, S., Klein, E., & Loper, E. (2009). *Natural Language Processing with Python: Analyzing Text with the Natural Language Toolkit.* O'Reilly Media, Inc.
- Skytrax Airline Quality: https://www.airlinequality.com/

---

## License

Add your license here (for example MIT for the code and CC BY 4.0 for the data), or remove this section if not needed. Note that the review text originates from Skytrax users, so check the source site's terms before redistributing the data publicly.
