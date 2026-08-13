# Phishing & Spam Email Detection (NLP)

An NLP pipeline that classifies emails as phishing or legitimate using real-world email corpora, built to extend my cybersecurity focus (breach-impact analysis project + cybersecurity internship) into text-based threat detection.

## Motivation
Phishing remains one of the most common initial attack vectors in real breaches. This project applies NLP and classical ML to detect phishing emails from raw text — going beyond dashboards/statistics (my previous project) into building an actual detection model.

## Dataset
Combines 6 real, publicly available email corpora via Kaggle (Al-Subaiey et al., 2024): CEAS_08, Enron, Ling, Nazario, Nigerian Fraud, and SpamAssassin — 82,486 emails total, merged from their original raw sender/subject/body fields (not the pre-cleaned combined file, to preserve real-world text noise for genuine preprocessing work).

- Verified label encoding consistency across all 6 source files before merging
- Checked for and confirmed zero duplicate emails
- Class balance: 52% phishing / 48% legitimate — note this reflects deliberate dataset curation, not real-world phishing prevalence (which is far lower)
- Two source files (Nazario, Nigerian Fraud) are phishing-only; they contribute no legitimate examples

## Preprocessing
- Lowercased, stripped punctuation/numbers
- URLs and email addresses normalized to placeholder tokens (`URL`, `EMAIL`) rather than deleted — their presence is a meaningful phishing signal
- Stopword removal, minimum token length filter
- Verified: 0 rows became empty after cleaning

## Models
| Model | Accuracy | Precision (phishing) | Recall (phishing) | F1 (phishing) |
|---|---|---|---|---|
| Naive Bayes + TF-IDF | 96% | 0.98 | 0.95 | 0.96 |
| Logistic Regression + TF-IDF | 98% | 0.98 | 0.99 | 0.98 |

**Logistic Regression is the stronger model**, particularly on recall for phishing (0.99 vs 0.95) — missing a phishing email (false negative) is a costlier error than flagging a legitimate one for review, so recall on the phishing class matters more than raw accuracy here.

## Key finding & honest limitation
Inspecting the top TF-IDF/coefficient words driving classification shows the model correctly learned phishing-specific language ("account", "click", "remove", "money"), but also partly learned **corpus-specific vocabulary** as a proxy for "legitimate" (e.g. "enron", "opensuse", "python") — an artifact of Enron/tech-mailing-list emails dominating the legitimate class. This means the model likely generalizes less well to legitimate emails from unrelated domains than the reported 98% accuracy suggests.

## Tech stack
Python, pandas, scikit-learn, NLTK, matplotlib

## Project structure
\`\`\`
data/raw/          - original source CSVs (not tracked in git)
data/processed/    - cleaned, merged dataset
notebooks/         - exploration and pipeline notebook
src/               - saved model files
visuals/           - exported charts
\`\`\`

## How to run
1. Download the dataset (see notebook for sources)
2. `pip install -r requirements.txt`
3. Run `notebooks/01_exploration.ipynb` top to bottom