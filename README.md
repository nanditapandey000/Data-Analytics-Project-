Week 1: Data Gathering, Cleaning and Preprocessing

Cleaning and preprocessing of a public finance dataset: African Development Bank Group projects in the Finance sector (credit lines, SME support, capital-market and trade-finance projects).

Dataset: finance1.csv (source: IATI Data Portal, published via Kaggle)
Goal: turn a raw, messy file into a clean, correctly typed, analysis-ready dataset, and document every decision.
Project files
File	Purpose
Week1_Data_Cleaning_Colab.ipynb	Step-by-step Google Colab notebook (main deliverable for presenting)
finance_cleaned.csv	Final cleaned dataset
Week1_Data_Cleaning_Report.pdf	Written report: methods, rationale, results
clean_finance.py	Same cleaning pipeline as a single Python script
make_report.py	Script that builds the PDF report from the pipeline output
finance1.csv	Original raw data (input)
How to run (Google Colab)
Open colab.research.google.com and choose File, then Upload notebook. Select Week1_Data_Cleaning_Colab.ipynb.
Upload finance1.csv using the folder icon in the left sidebar.
In Step 2, check that DATA_PATH matches your file location:
Colab upload: "/content/finance1.csv"
Google Drive: mount Drive with the optional cell, then use "/content/drive/MyDrive/finance1.csv"
A direct URL also works.
Click Runtime, then Run all.
finance_cleaned.csv is saved and downloaded at the end.
How to run (local Python)

Requirements: Python 3.9+, pandas, numpy, matplotlib (and reportlab for the report).

bash
pip install pandas numpy matplotlib reportlab
python clean_finance.py
python make_report.py

Edit the SRC and OUT paths at the top of clean_finance.py to match your machine.

Cleaning pipeline
Step	What is done	Why
1. Load	Read with cp1252 encoding	The file is not UTF-8; a default read fails
2. Explore	Shape, types, missing values, category counts	Understand the data before changing it
3. Structural cleaning	Remove 18 blank rows and 1 footer row (355 to 336 rows)	Export layout artefacts, not projects
4. Text cleaning	snake_case column names, trimmed spaces, unified dashes, standardised country names (for example "Ex Zaire" to "DR Congo")	Consistent labels
5. Type conversion	Amount text to float, date text to datetime, text labels to category	Needed for maths, time analysis, memory
6. Duplicates and validation	Checked full-row and project-code duplicates, positive amounts, no future dates, project-code format	Data integrity
7. Missing values	56 missing sovereign labels set to "Unknown"; constant sector column dropped	Keeps all projects without guessing
8. Outliers	34 values (10.1%) flagged with the IQR rule, log10 transform, winsorized copy capped at the 99th percentile	Large amounts are genuine, so they are kept, not deleted
9. Scaling	Min-max (0-1) of the amount; z-score of the log amount	Ready for distance-based or linear models
10. Feature engineering	start_year, start_decade, is_multinational	Support later exploratory analysis
Results
	Raw	Cleaned
Rows	355	336
Columns	9	16
Missing cells	225	0
Data types	all text	float, datetime, category, int, bool, text
Skewness of the commitment amount: 2.80 before, -0.37 after the log transform.
Status: Completion 210, Implementation 95, Approved 26, Cancelled 5.
Sovereign: Sovereign 167, Non Sovereign 113, Unknown 56.
Columns in finance_cleaned.csv
Column	Description
project_code	Unique project ID
title	Project name (English or French)
commitment_ua	Commitment amount in Units of Account (UA)
country	Beneficiary country (or "Multinational")
start_date	Project start date
status	Completion, Implementation, Approved or Cancelled
financing_source	Funding window (for example African Development Bank, African Development Fund)
sovereign	Sovereign, Non Sovereign or Unknown
is_outlier_iqr	True if the amount is outside the IQR fences
log_commitment	log10 of the amount
commitment_winsorized	Amount capped at the 99th percentile
commitment_minmax	Amount scaled to 0-1
log_commitment_zscore	Standardised log amount (mean 0, std 1)
start_year, start_decade	Derived from start_date
is_multinational	1 if country is "Multinational", else 0
Limitations
The 56 Unknown sovereign labels could be filled in if the original AfDB portal records are consulted.
Amounts are in UA (a basket-based unit of account) and are not adjusted for inflation, so figures from the 1970s and 2020s are not directly comparable.
The data covers one sector only.
Some project codes (7) use legacy or grant formats (for example G-... or six-digit numbers). They are valid and were kept.
