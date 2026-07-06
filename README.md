# Data Job Market Analysis (India)

Welcome to my analysis of the data job market in India, with a specific focus on Data Analyst roles. This project was born out of a desire to navigate and understand the data landscape more effectively. It dives deep into the most in-demand skills, monthly job trends, salary distributions, and the optimal intersection of demand and compensation to help identify top career paths.

The data is sourced from [Luke Barousse's Python Course](https://lukebarousse.com/python), which provides a rich foundation of 2023 job postings, containing raw information on job titles, salaries, locations, companies, and requested tech skills. Through a series of specialized Python notebooks, I explore and visualize key structural questions defining the Indian tech market.

# The Questions

Below are the key questions investigated throughout this project:

1. What is the overall distribution of data-related job titles in the dataset?
2. Where are the most data jobs located globally, and how does India rank?
3. Which companies are the top employers for data professionals?
4. What are the skills most in demand for Data Analysts in India?
5. How are these top in-demand skills trending month-by-month?
6. How well do jobs and specific skills pay for Data Analysts in India?
7. What are the optimal skills for data analysts to learn in India? (High Demand AND High Paying)

# Tools I Used

For my deep dive into the data job market, I harnessed the power of several key tools:

- **Python:** The backbone of my analysis, allowing me to filter the data and extract critical insights. I utilized the following foundational libraries:
    - **Pandas Library:** Used to clean, structure, and aggregate the raw dataset.
    - **Matplotlib Library:** Used to build the foundational layout of my data visualizations.
    - **Seaborn Library:** Used to create modern, customized, and styled graphical representations.
- **Jupyter Notebooks:** The interactive development environment used to run scripts, embed markdown annotations, and visualize plots inline.
- **Visual Studio Code:** My preferred IDE for executing python logic and managing project directories.
- **Git & GitHub:** Essential for version control and safely pushing my codebase to an open-source repository.

# Data Preparation and Cleanup

This section outlines the workflow executed across the notebooks to prepare the data for analysis, ensuring quality, correct data types, and accurate aggregations.

## 1. Environment Setup & Data Loading
The initial steps involve importing the necessary libraries and handling data structure conversions. In the core exploratory file (`1_EDA.ipynb`), the dataset is loaded from a raw CSV format:

```python
# Importing Libraries
import ast
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt

# Loading Data
df = pd.read_csv('data_jobs.csv')

# Data Cleanup
df['job_posted_date'] = pd.to_datetime(df['job_posted_date'])
df['job_skills'] = df['job_skills'].apply(lambda x: ast.literal_eval(x) if pd.notna(x) else x)
```

## 2. Localizing the Data for India
To establish the base dataframes for this localized study, the data was filtered for postings located in India, with specialized subsets isolated specifically for the Data Analyst role.

```python
# Filter for Indian Data Analyst roles
df_In = df[df['job_country'] == 'India']
df_DA_In = df[(df['job_country'] == 'India') & (df['job_title_short'] == 'Data Analyst')]
```

# The Analysis

Each notebook within this repository targets a specific analytical question. Below is the step-by-step breakdown of the execution, visualization, and insights:

## 1. General Job & Geographic Explorations
View my notebook with detailed steps here: `1_EDA.ipynb`.

I began by evaluating the global data footprint to understand where data opportunities are concentrated and which roles dominate.

### Visualize Data
```python
# Extracting top global country counts
df_plot = df['job_country'].value_counts().reset_index().head(20)

sns.set_theme(style='ticks')
sns.barplot(data=df_plot, x='count', y='job_country', hue='count', palette='dark:b_r', legend=False)
sns.despine()
plt.title('Number of Jobs per Country')
plt.xlabel('Number of Jobs')
plt.show()
```

### Insights
*   **Dominant Roles:** Globally and locally, Data Engineer roles appear with the highest frequency, followed tightly by Data Scientists, making Data Analyst the third most common core classification.
*   **India is a Global Hub:** Outside of the United States, India represents one of the largest and most robust employment markets for data professionals in the world.

---

## 2. What are the most demanded skills for the top data roles?
View my notebook with detailed steps here: `Skill_Demand.ipynb`.

To isolate the core tech requirements, I aggregated and unnested individual skill lists across top roles to count their occurrences within India's job market.

### Visualize Data
```python
# Evaluating top skills for Data Analysts, Engineers, and Scientists
df_skills = df_In.explode('job_skills').dropna(subset=['job_skills'])
df_skills_count = df_skills.groupby(['job_skills', 'job_title_short']).size().reset_index(name='skill_count')
df_skills_count.sort_values(by='skill_count', ascending=False, inplace=True)
```

### Insights
*   **The Baseline Stack:** For Indian Data Analysts, **SQL** is the most highly demanded skill, appearing near the 50% frequency mark across job profiles.
*   **Core Toolkit:** **Excel** and **Python** hold a powerful combined demand share, solidifying them as baseline mandatory skills alongside visualization software like **Tableau** and **Power BI**.

---

## 3. How are in-demand skills trending for Data Analysts?
View my notebook with detailed steps here: `Skills_Trend.ipynb`.

I analyzed the timestamps of postings throughout 2023 to map out the monthly trend and verify whether the demand for core analyst tools fluctuates or remains steady.

### Visualize Data
```python
# Tracking monthly skill percentages for top skills
df_DA_In['job_posted_month_no'] = df_DA_In['job_posted_date'].dt.month
df_DA_In_explode = df_DA_In.explode('job_skills').dropna(subset=['job_skills'])
df_DA_In_pivot = df_DA_In_explode.pivot_table(index='job_posted_month_no', columns='job_skills', aggfunc='size', fill_value=0)

# Calculate monthly trend based on percentage ratios
df_DA_In_percent = df_DA_In_pivot.iloc[:12].div(DA_totals/100, axis=0)
df_plot = df_DA_In_percent.iloc[:, :5]
sns.lineplot(data=df_plot, dashes=False, palette='tab10')
```

### Insights
*   **SQL Persistence:** SQL maintained a commanding lead as the number one requested skill across every month of 2023 without exception.
*   **BI Battle:** Tableau maintained a clear margin of higher demand over Power BI for the majority of the year, though both experienced a strong surge in enterprise recruitment mentions during Q4.

---

## 4. How well do jobs and skills pay for Data Analysts in India?
View my notebook with detailed steps here: `Salary_Analysis.ipynb`.

*Note: All salary data in this dataset is recorded and analyzed in USD (United States Dollars). Due to standard local recruitment practices where local listings often omit explicit pay ranges, the available salary records lean strongly toward multinational corporations (MNCs), major consulting groups, and high-paying remote international contracts.*

### Visualize Data
```python
# Comparing median pay fields among top roles
sns.boxplot(data=df_In_top6, x='salary_year_avg', y='job_title_short', order=job_order, palette="pastel")
```

### Insights
*   **Role Values:** Machine Learning Engineers and Data Scientists command the highest median salaries in India, while Data Analyst salaries remain tighter and more uniform, pointing to a highly stable entry and mid-tier professional path.
*   **Demand vs. Compensation Divergence:** The highly demanded baseline skills (SQL, Excel) do not command the top salaries. Instead, advanced or specialized technical architectures like **PySpark, Scala, GitLab, Linux, and MongoDB** boast the highest median pay scales (averaging up to $160K+ USD for specialized global contracts), reflecting that advanced data engineering skills maximize salary returns.

---

## 5. What are the most optimal skills for data analysts to learn?
View my notebook with detailed steps here: `Optimal_Skills.ipynb`.

By modeling the intersection of market demand (Job Percent) and compensation (Median Salary), I mapped out the optimal training strategies.

### Visualize Data
```python
# Generating a scatter plot mapping market demand against financial premium
plt.scatter(df_DA_skills_high_demand['skill_percent'], df_DA_skills_high_demand['median_salary'])
```

### Insights
*   **The Foundation:** **SQL** and **Python** are the highest-value core investments, guaranteeing high job availability while maintaining robust, solid baseline income levels.
*   **The Specialization Fast-Track:** Learning big data framework applications like **Scala, Spark/PySpark, and Databricks** yields the highest financial return. While they are present in a lower total percentage of jobs, they present a highly lucrative engineering upskilling niche with top-tier premium payouts in India.

---

## 6. Challenges Faced & Lessons Learned
*   **Missing Salary Bias:** A major hurdle was navigating missing values in local Indian compensation metrics. Recognizing that explicit salary listings are heavily dominated by international remote work and global companies allowed for a much better contextual understanding of the market.
*   **Nested Text Data:** Unpacking complex python dictionaries and stringified arrays inside pandas columns required structured lambda mapping and exploratory parsing to ensure accurate aggregations.
*   **Strategic Upskilling:** The core takeaway of this project is the necessity of aligning learning roadmaps with market signals. Moving beyond baseline descriptive analyst tools into advanced coding infrastructure is a proven path to career growth.

---

## 7. Conclusion
This deep dive highlights that while foundational tools (SQL, Excel, Tableau) guarantee market access and massive job volume, the real economic upside for data analysts in India lies in cloud platforms, database administration, and big data engineering architectures.