# Lending Club Credit Risk & Profitability Analytics

Executive dashboard and credit risk analytics framework built using SQLite (ETL/Data Validation) and Power BI (PBIP project format with TMDL DAX modeling).

The objective of this project is to analyze loan performance, credit loss distributions, and net profitability across risk grades, providing strategic recommendations for underwriting optimization.

---

## Executive Report

![Credit Risk & LGD Executive Report](assets/dashboard.png)

---

## Key Business Insights

- **Profitability vs. Default Trade-Off (Grades A–G)**: While Grade B generates the highest cumulative net profit, returns become sharply negative starting in Grade E, with Default Rates climbing toward 40% in Grades F and G.
- **Loss Given Default (LGD) Severity**: Across 268,559 charged-off loans, the portfolio experienced a cumulative net loss exceeding $1.95B, with an aggregate LGD rate of 46.71%. Grade G reaches the highest severity with an LGD of 50.13%.
- **Term & Debt Burden Exposure**: Loans with 60-month terms and high debt burdens (DTI > 20%) present the highest default concentration at 18.26%, compared to 8.78% for short-term (36 months), low-DTI loans.

---

## Technical Stack & Architecture

- **Database & ETL**: SQLite for raw ingestion, aggregations, and baseline metric reconciliation.
- **Semantic Model & PBIP**: Power BI Developer Mode (`.pbip`) utilizing Tabular Model Definition Language (`TMDL`) for modular DAX version control.
- **Data Architecture**: Star Schema model with a central `fact_loans` table connected to normalized dimension tables (`dim_borrower`, `dim_loan_contract`, `dim_credit_bureau`, `dim_loan_status`, `dim_date`).

---

## Core DAX & Business Logic

To reflect actual realized profitability and eliminate bias from ongoing credit, **open/current loans are explicitly isolated from terminal metrics**:

* **Net Profit**: Calculated exclusively on matured loans (`Fully Paid` vs. `Charged Off`).
* **Loss Given Default (LGD %)**: Cumulative net loss divided by the total funded amount of charged-off loans.
* **Return %**: Realized net profit expressed as a percentage of disbursed capital.

```dax
// Core Net Profit Measure (Excludes Current/In-flight Loans)
Net Profit = 
CALCULATE(
    SUM(fact_loans[total_pymnt]) - SUM(fact_loans[funded_amnt]),
    dim_loan_status[loan_status_clean] IN {"Fully Paid", "Charged Off"}
)
Project Structure
Plaintext
├── assets/                                    # Executive report screenshots & documentation
├── lending_club_risk_analytics.Report/        # PBIP Visual presentation & layout configs
├── lending_club_risk_analytics.SemanticModel/ # Data model, TMDL tables & DAX definitions
│   └── definition/
│       ├── tables/
│       │   ├── _Medidas.tmdl                 # Centralized DAX measures
│       │   └── fact_loans.tmdl               # Fact table definitions
│       └── model.tmdl                        # Relationships and model metadata
├── .gitignore                                 # Excludes raw databases and heavy caches
└── lending_club_risk_analytics.pbip          # Power BI Project entrypoint
How to Run Locally
Clone the repository:

Bash
git clone [https://github.com/arypalacio-coder/lending-club-credit-risk-analytics.git](https://github.com/arypalacio-coder/lending-club-credit-risk-analytics.git)
Open lending_club_risk_analytics.pbip in Power BI Desktop (Developer Mode enabled).

Connect the semantic model to your SQLite database or processed source to refresh the report visuals.
