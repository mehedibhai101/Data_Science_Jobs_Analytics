# 📊 Excel Formulas: Data Science Jobs Analytics

This document outlines the advanced Excel formulas used to analyze the data science job market. These formulas leverage dynamic arrays and nested conditional logic to provide insights into salary benchmarks, remote work trends, and platform distribution.

## 💼 Role-Based Analysis

These formulas dynamically extract job roles and calculate specific financial benchmarks for each title.

* **Job Titles:** Generates a unique, de-duplicated list of all job titles present in the dataset.
  * **Formula:** `=UNIQUE(job[job_title_short])`

* **Median Salary:** Calculates the middle salary value for a specific role and country, excluding missing data (0 values).
  * **Formula:** `=MEDIAN(IF((job[job_title_short]=A2)*(job[Country]=country)*(job[salary_year_avg]<>0), job[salary_year_avg]))`

* **Job Count:** Counts the number of active postings for a specific role that include valid salary information.
  * **Formula:** `=COUNTIFS(job[job_title_short], A2, job[Country], country, job[salary_year_avg], "<>0")`


## 🌍 Geographical & Temporal Trends

Logic used to pivot the data by country and time (month) to identify where and when the market is most active.

* **Countries:** Extracts and alphabetically sorts all unique countries represented in the data.
  * **Formula:** `=SORT(UNIQUE(job[Country]))`

* **Monthly Job Count:** Tracks hiring volume trends across the calendar year.
  * **Formula:** `=COUNTIFS(job[posted_month], A2, job[job_title_short], title, job[Country], country, job[salary_year_avg], "<>0")`


## 🏠 Work Environment & Requirements

These sections use boolean logic to determine the prevalence of remote work and educational expectations.

* **Job Type (Remote vs. On-site):**

 | Type | Formula |
 |---|---|
 | Remote | =COUNTIFS(job[job_work_from_home], TRUE, job[job_title_short], title, job[Country], country, job[salary_year_avg], "<>0") |
 | On-site | =COUNTIFS(job[job_work_from_home], FALSE, job[job_title_short], title, job[Country], country, job[salary_year_avg], "<>0") |
 | Total | =SUM(B1:B2) |
 | % Share | =B1/$B$3 |

* **Degree Mentioned:**

 | Status | Formula |
 |---|---|
 | Mentioned | =COUNTIFS(job[Remote Job], FALSE, job[job_title_short], title, job[Country], country, job[salary_year_avg], "<>0") |
 | Not Mentioned | =COUNTIFS(job[Remote Job], TRUE, job[job_title_short], title, job[Country], country, job[salary_year_avg], "<>0") |
 | Total | =SUM(B1:B2) |
 | % Share | =B1/$B$3 |


## 🚀 Platform & Sourcing

Cleaning and aggregating data from various job boards and aggregators.

* **Platforms:** A list of every unique source where jobs were posted.
  * **Formula:** `=UNIQUE(job[job_via])`

* **Job Count:** Counts the number of active postings for a specific platform that include valid salary information.
  * **Formula:** `=COUNTIFS(job[job_via],A4,job[job_title_short],title,job[Country],country,job[salary_year_avg],"<>0")`

* **Platforms (Sorted):** Sorted list of the platforms according to job posting count.
  * **Formula:** `=SORT(A2:B594,2,-1)`

* **Top Platform (Cleaned):** Removes the "via " prefix from platform names for cleaner data visualization.
  * **Formula:** `=SUBSTITUTE(C2, "via ", "")`


## 🧠 Explanation of Complex Logics

* **Conditional Median (The "Array" IF):**
Standard Excel MEDIAN doesn't support multiple criteria. To solve this, we use an IF statement inside the MEDIAN function:
`=MEDIAN(IF((Criteria1)*(Criteria2)*(Non-Zero), Value_Range))`
This creates an array of values in memory that only contains salaries for the specific job and country you've selected, effectively acting like a `MEDIANIFS` function.

* **Boolean Filtering:**
The formulas for Remote vs. On-site rely on logical `TRUE/FALSE` checks within the job_work_from_home column. This is more efficient than text-matching "Yes/No" and allows for faster calculation in large datasets.
String Manipulation for UI:
The `SUBSTITUTE` formula in the Platforms section is a "data cleaning" step. Raw data often comes in as "via LinkedIn" or "via Indeed." By stripping the "via " string, the dashboard labels become more professional and take up less horizontal space in charts.

* **Dynamic Array Spilling:**
Using `=UNIQUE()` and `=SORT()` ensures the dashboard is "future-proof." If you add new jobs or countries to the raw data table, these lists will automatically expand (or "spill") downwards without needing to manually update the formulas.
