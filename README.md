BLIP-OLIVE – Human-Based Evaluation (Statistical Analysis)

This repository contains the data and the code used to analyse the human-based evaluation of  BLIP-OLIVE , an explainable (XAI) system for olive-tree disease diagnosis in smart agriculture. Domain experts (farmers / agronomists) rated the system through a Google Forms questionnaire; the notebook turns the raw answers into descriptive statistics, non-parametric significance tests and a first-pass qualitative coding.

Repository structure

```
Git-SDD/
├── T-Shirt Sign Up2.csv          Raw questionnaire export (Google Forms)
├── Test_statistique_.ipynb       Analysis notebook (Google Colab)
└── README.md
```

> The CSV keeps the name of the original Google Forms export (`T-Shirt Sign Up2.csv`) and contains no T-shirt data: it holds the BLIP-OLIVE evaluation responses.

Dataset

Each row is one respondent; the first column is the submission timestamp, followed by 16 questions grouped in three sections.

| Section | Columns | Content | Scale |
|---|---|---|---|
|  1 – Quality of the explanations  | 1–8 | Clarity and Readability, Symptom–disease consistency, Usefulness of treatment recommendations, Diagnosis and decision-making support, Completeness, Coherence, Trustworthiness, Absence of Irrelevant Information | Likert 1–5 |
|  2 – Comparison with BLIP-2 and adoption  | 9–12 | Overall Added Value, Practical Decision Support, Intention to Use, Recommendation Intention | Likert 1–5 |
|  3 – Qualitative feedback  | 13–16 | Human-like Assistance, Clarity and Technical Complexity, Impact on Current Practice, Generalization to Other Crops | Free text |

Likert answers are stored as `5 (Very High)`, `4 (High)`, …; the notebook extracts the leading digit and converts it to a number.

Analysis pipeline

The notebook is organised in three parts, one per questionnaire section.

Section 1 and Section 2 (quantitative)

1. Load the CSV and select the columns of the section (`iloc`, since the Forms column names are very long).
2. Convert the Likert answers to numeric values.
3. Keep only  complete cases  (a respondent must answer all criteria in the test; blank or incomplete rows are excluded).
4. Descriptive statistics per criterion: N, mean, SD, median, Q1, Q3, IQR, share of favourable ratings (4–5).
5. Frequency distribution of the ratings 1–5.
6.  Friedman test  across the criteria (H0: the criteria have equivalent distributions).
7.  Kendall's W  as effect size.
8. Friedman  mean ranks .
9.  Post-hoc Wilcoxon signed-rank tests  on all pairs of criteria, with  Holm correction  for multiple comparisons.
10. Table of the significant pairwise differences.

 Section 3 (qualitative) 

1. Clean the free-text answers and count the valid responses per question.
2. Print the original answers.
3. First-pass orientation coding with simple regular expressions ( Positive/Yes ,  Negative/No ,  Conditional/Mixed ,  Other/Needs manual coding ). It is only a coding aid: the original answers must still be read and coded manually.

  Requirements

- Python 3.9+
- `pandas`, `numpy`, `scipy`, `statsmodels`

```bash
pip install pandas numpy scipy statsmodels
```

How to run

The notebook was written for Google Colab.

1. Upload `T-Shirt Sign Up2.csv` to your Google Drive (`MyDrive`) and open `Test_statistique_.ipynb` in Colab.
2. Run the first cells to install the dependencies and mount Drive.
3. Run the remaining cells in order.

To run it  locally , skip the `drive.mount` cell and set `file_path` to the local path of the CSV (it is defined at the top of each of the three parts):

```python
file_path = "T-Shirt Sign Up2.csv"
```

  Output files

The notebook saves its results as CSV files (in `/content/` on Colab, or in the working directory):

| File | Content |
|---|---|
| `descriptive_statistics.csv` | Descriptive statistics, Section 1 |
| `likert_frequencies.csv` | Rating frequencies, Section 1 |
| `friedman_test_results.csv` | Friedman test and Kendall's W, Section 1 |
| `friedman_mean_ranks.csv` | Mean ranks, Section 1 |
| `wilcoxon_holm_results.csv` | Post-hoc pairwise tests with Holm correction, Section 1 |
| `significant_pairwise_results.csv` | Significant pairs only, Section 1 |
| `section2_descriptive_statistics.csv` | Descriptive statistics, Section 2 |
| `section2_friedman_mean_ranks.csv` | Mean ranks, Section 2 |
| `section2_wilcoxon_holm_results.csv` | Post-hoc tests with Holm correction, Section 2 |
| `section3_qualitative_analysis.csv` | Qualitative answers with orientation coding |

Notes

- Non-parametric tests are used because the data are ordinal (Likert) and the sample is small.
- Respondents' answers are anonymous: the only identifier in the file is the submission timestamp.
