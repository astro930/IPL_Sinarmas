<h1> [Restoring IPL Cashflow Health at Sinarmas Land: A Hypothesis-Driven Analysis of Non-Performing Invoices] </h1>

## 1. Project Overview

Sinarmas Land (SML) manages five townships in Greater Jakarta (BSD City, Grand Wisata, Kota Wisata, Deltamas, and NavaPark). Besides unit sales, SML earns recurring income from the Iuran Pemeliharaan Lingkungan (IPL), a monthly estate maintenance fee paid by unit owners to fund security, cleaning, and public facility upkeep. A growing share of IPL invoices is left unpaid. This is a cashflow risk for Estate Management and increases reliance on parent-company subsidies.

This project analyzes billing, unit, cluster, collection, and dispute data to find which segments drive unpaid invoices and to translate the findings into prioritized actions. It follows a hypothesis-driven approach: 18 hypotheses were written before exploring the data, filtered for testability, and then tested on cleaned data. Each test reports the effect size, a unit-level bootstrap confidence interval, Holm-corrected p-values, and the effect that remains after controlling for confounders.

Core metric: Non-Performing Invoice (NPI) rate = invoices with status Unpaid or Overdue ÷ invoices issued × 100% (lower is better).

Problem statement (SMART):
The Head of Township Management needs to reduce the NPI rate of new IPL invoices across Sinarmas Land's 5 townships from 37.24% to ≤ 34.8% (proposed target) within the next 2 quarters. The plan prioritizes an autodebit program for units not yet enrolled and focused collection on vacant units whose owners live out of town or abroad. The overall baseline NPI rate is 35.44%.

Key Objectives:
- Objective 1: Identify the unit and area characteristics and segments that contribute most to IPL arrears, as a - basis for Collection priorities.
- Objective 2: Estimate the revenue held up by prolonged arrears (nominal value and aging of receivables).
- Objective 3: Define a standard recording scheme for payment methods for the Finance team.
- Objective 4: Evaluate billing data quality (duplicate invoices, water meter input, inconsistent statuses) as a basis for recommendations to the IT/ERP team.

## 2. Data Sources
Link Dataset Raw & Clean: https://drive.google.com/drive/folders/17PVwfVs8ozYrzaie_KbrgyavIaloQ3sY?usp=sharing

Raw data
- clusters_raw.csv – Cluster dimension: township, cluster category, launch phase, facility tier, zone type, and management office model (111 rows, 8 columns).
- units_raw.csv – Unit and owner dimension: owner domicile, vacancy status, contact number and verification, unit size, base IPL fee, and handover date (28,881 rows, 11 columns).
- ipl_billings_raw.csv – Monthly IPL invoices: water usage and meter input method, fees, late penalty, total amount, payment status/method/date, billing channel, reminders, and autodebit enrollment (807,427 rows, 15 columns).
- collection_actions_raw.csv – Collection action log: action type (phone call, SMS, letter), agent, and owner response (126,772 rows, 7 columns).
- payment_disputes_raw.csv – Invoice disputes: channel, reason, resolution status, days to resolve, and adjusted amount (23,928 rows, 9 columns).

Cleaned data
- ipl_clean.csv – Main analysis table: 768,978 primary invoices (one invoice per unit per month) joined with units and clusters.
- billings_clean.csv – All 807,427 invoices, including duplicate copies, with duplicate flags and exclusion reasons, standardized payment method, anomaly flags, is_npi, and arrears age.
- units_clean.csv – Units with repaired contact numbers (leading zero restored) and the punya_kontak / kontak_valid flags.
- clusters_clean.csv, actions_clean.csv, disputes_clean.csv – Unchanged in content; saved so the cleaned set is complete.
- unit_prioritas.csv – Collection work list (one row per unit, by tier). Owner names and phone numbers are excluded.

## 3. Technologies Used
- Programming Language: Python (e.g., Pandas, NumPy)
- Visualization: Matplotlib, Seaborn, Plotly
- Interactive Dashboard: Tableau
- Version Control: Git
- Others: Jupyter Notebook

## 4. Project Structure

```
├── README.md          <- Project overview, data sources, findings, and recommendations.
|
├── data
│   ├── raw            <- Raw CSV snapshots extracted from the `sinarmasland` MySQL database (5 tables).
│   └── cleaned        <- Cleaned tables, the joined analysis table (ipl_clean.csv), and the Collection work list.
│
├── notebooks          <- Jupyter notebooks, e.g. `1.0-ala-ipl-npi-hypothesis-analysis.ipynb`
│                         (end-to-end: problem, hypotheses, cleaning, testing, EDA, recommendations).
│
├── reports            <- Generated analysis as PowerPoint, PDF, etc.
|   ├── slide          <- Final presentation deck.
│   └── figures        <- Charts exported from the notebook (NPI per cohort, hypothesis summary, etc.).
│
├── requirements.txt   <- Python dependencies (pandas, numpy, matplotlib, seaborn, scipy, sqlalchemy, pymysql).
│
└── src                <- Helper code (e.g. archived database extraction script).

```

## 5. Summary of Finding
### 5.1 Business Insight
1. More than one in three invoices is unpaid, and the problem is not improving.

The baseline NPI rate is 35.44% (272,545 of 768,978 primary invoices). The brief estimated "more than a quarter", so the real figure is higher.
By issuance month, NPI rose from 20.03% (July 2022) to a stable 35–38% range since 2023. The average of the first 6 mature cohorts (Jul–Dec 2022) is 30.14%; the last 6 (Jun–Nov 2024) is 37.24%.

2. A large and aging receivable.

Gross NPI value is Rp224.64 billion. Excluding the water component that needs validation, the analysis value is Rp176.34 billion.
88.0% of the value is more than 90 days past due, and Rp93.5 billion (53%) is more than one year old. No payment was observed more than 22 days after the due date, so old arrears should not be assumed recoverable in cashflow planning.

3. Four characteristics remain associated with NPI after controlling for the others:

Factor	NPI (risk group vs comparison)	Raw gap	Controlled gap
No autodebit	39.71% vs 9.93%	+29.78 pts	+26.59 pts
Vacant unit	58.43% vs 28.11%	+30.32 pts	+23.28 pts
Owner out of town / abroad	49.93% vs 27.88%	+22.04 pts	+10.14 pts
New Phase cluster (2021–2023)	40.07% vs 32.83%	+7.24 pts	+7.00 pts
Only 14.34% of invoices use autodebit, yet invoices without autodebit account for 96% of NPI value. This is the largest factor on both percentage and value.
Vacant units carry the highest risk, but occupied units still hold 62.5% of NPI value (Rp110.24 billion) because they are far more numerous.
Invoices with all four factors have an NPI rate of 67.10%; invoices with none have 4.93%. The pattern is consistent across all 5 townships and every year.

4. Some apparent drivers disappear after control.

Missing or unverified contact numbers, paper billing, and Regional Hub management showed raw gaps of 3–12 points. These shrink to about 0 points once vacancy, domicile, autodebit, and cluster phase are held equal. They reflect who the owners are, not independent causes.
Due-date length (H9) and manual water meter input (H16) are not supported by the data.

5. Arrears are concentrated in units, not areas.

NPI rates differ little between townships (34.70–36.29%), and 50% of NPI value is spread across 40 of 111 clusters.
At the unit level, 6,771 units (23.4%) hold 50% of NPI value (Rp88.2 billion). These priority units are mostly vacant (49.7%) and owned by out-of-town or overseas owners (64.9%).

6. Collection coverage is low, and its effect cannot yet be measured.

Only 30.4% of NPI invoices appear in the collection log, and Rp121.2 billion (69%) of NPI value has never been followed up.
All actioned invoices are still Overdue at the snapshot date, so the effectiveness of phone, SMS, and letter actions cannot be tested (H14).
Owners who respond and commit ("Janji Bayar", "Lunas Setelah Kontak") pay next month's invoice more often than non-responders.

7. Billing data quality issues affect accuracy and residents' trust.

38,449 duplicate invoice copies (invoice numbers 9xxxx, June–September 2023) inflated NPI by 1.52 points. 12,568 duplicate pairs were paid twice (± Rp11.07 billion) and need bank reconciliation.
Payment method is recorded in 9 spellings for 4 actual methods: BCA Virtual Account, Cash, Credit Card, and Tokopedia.
3,824 negative and 3,789 implausibly high (> 97.8 m³) water readings all come from manual input.
Duplicate-invoice and abnormal-water disputes are cancelled 91.2% and 76.7% of the time, so many residents' complaints stem from system errors.


### 5.2 Actionable Recommendation
Actor	Action	Time	Expected Impact / KPI

Head of Township Management	Approve a pilot of the autodebit campaign and focused collection, with a control group, interim targets, program owner, budget, and monthly review	Month 1; review every quarter-end	New-invoice NPI, new NPI value, actual cash received, program cost, and net benefit. The ≤ 34.8% target is confirmed after the pilot.
Finance & Collection	Run an autodebit campaign for 24,754 eligible units, prioritizing vacant units and out-of-town/overseas owners	Q1–Q2	Unit autodebit adoption from 14.3% to 35.7%; new-invoice NPI down about 2.5 points (base scenario); about Rp0.46 billion/month of new NPI potentially prevented

Collection	Contact 3,743 Tier 1 priority units (verified contact) first through digital channels and follow up every "promise to pay"; set up a recovery/installment program for arrears older than 1 year	Q1–Q2	30/60/90-day collection rate and actual cash collected from Rp88.2 billion held by priority units

Customer Service	Verify contacts for 3,028 Tier 2 priority units, then the 4,978 units without a phone number	Q1 (first 6 weeks)	Share of units with verified contact (baseline 69.0%), response rate. Improves reachability; not expected to reduce NPI on its own.

Finance	Set 4 standard payment method codes as master data with input validation; reconcile the 12,568 Paid + Paid pairs, 3,795 NPI invoices and 5,953 Paid invoices that were fully cancelled in disputes; confirm the Unpaid/Overdue definitions and late-fee policy	Q1	Unknown payment methods = 0; unreconciled value; number of out-of-sync invoices. Refunds only after matching with bank statements.

IT/ERP	Enforce one invoice per unit per month; validate manual meter input outside 0–97.8 m³; sync collection and dispute status to billing; store status history and the extraction date	Q1–Q2	New duplicate invoices = 0; water readings outside the rule; illogical statuses or dates
Estate Operations	Investigate the 3,789 high water readings with meter logs or photos, and accelerate migration to automatic meters	Q1–Q2

## 6. Contact
- Name: Astro Lihardo A
- Email: astrogalingging@gmail.com
