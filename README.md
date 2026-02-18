# <p align="center"> OPTIMAL SKILLS TO LEARN TO BECOME A DATA ANALYST/SCIENTIST IN CANADA - 2025 </p>

## ◼️ Introduction 🐍
This project explores the Canadian data job market to identify the most valuable skills for aspiring Data Analysts and Data Scientists. 

The primary goal is to leverage Python libraries—specifically **Pandas**, **Matplotlib**, and **Seaborn**—to conduct comprehensive data analysis and visualization. By analyzing real job postings, salary trends, and skill demand, this analysis aims to provide actionable insights on which skills are most sought after and highest-paying in the Canadian market, helping guide strategic learning decisions. 📈




## ◼️ Background and Motivation 🎯
The transition into **Data Science** is an exciting but challenging journey, and understanding the current job market landscape is crucial. 💼 My motivation for this project stems from a passion for mastering **Python** and building a strong foundation as a **Data Analyst**, with the ultimate goal of evolving into a **Data Scientist**. 🚀 

Rather than learning skills in isolation, I wanted to take a data-driven approach—analyzing real market demand and salary patterns to prioritize which tools and techniques to focus on. This project serves as both a practical learning experience with essential Python libraries and a strategic roadmap for developing a competitive skill set in Canada's data job market. 🎯



## ◼️ Data Sources 🗄️


The dataset used in this analysis comes from **Luke Barousse's course** and his website, which aggregates job postings for various data roles including:
- 📊 Data Analysts
- 🔬 Data Scientists
- 💼 Related positions across Canada

The dataset contains comprehensive information about job listings with multiple attributes:
- 💰 Yearly salary
- 📍 Location & country
- 📅 Date posted
- 🛠️ Required skills
- 🏢 Company information
- And more relevant job market data

This rich dataset provides the foundation for analyzing **trends**, **skills demand**, and **salary patterns** in the Canadian data job market. 🚀

## ◼️ Dataset Structure 📂

The dataset includes the following columns, organized by category:

🧑‍💼 Job Information

- job_title_short — str

- job_title — str

- job_location — str

- job_country — str

- company_name — str

🧾 Job Details

- job_via — str

- job_schedule_type — str

- job_work_from_home — bool

- job_no_degree_mention — bool

- job_health_insurance — bool

📅 Time Information

- job_posted_date — str

📊 Salary Information

- salary_rate — str

- salary_year_avg — float64

- salary_hour_avg — float64

🔍 Skills & Requirements

- job_skills — str

- job_type_skills — str

🌍 Search Context

- search_location — str


## ◼️ Methodology 🔬
To develop this project, we formulated a set of guiding questions that helped us design clear, step-by-step methodologies for each analysis, ensuring a structured and consistent workflow.

The first step in this process was preparing the data for analysis. This included importing the necessary libraries, loading the dataset, performing data cleaning, filtering job postings for the year 2025, and creating a month column to enable time-based analysis and proper chronological ordering.


```python
# Importing Libraries
import ast # To convert the skils to a list
import pandas as pd 
import seaborn as sns
from datasets import load_dataset
import matplotlib.pyplot as plt

# Loading Data (Job postings)
df = pd.read_csv(r"C:\Users\WIN10\Desktop\Job_Analysis_2025\Job_CSV\jobs_postings.csv")


# Data clean up
df['job_posted_date'] = pd.to_datetime(df['job_posted_date'])
df['job_skills'] = df['job_skills'].apply(lambda x: ast.literal_eval(x) if pd.notna(x) else x)

# Create the column month (data will be analyzed monthly)
df['job_posted_month'] = df['job_posted_date'].dt.strftime('%b')

# Filter by the year and sorting by month
df_2025 = df[df['job_posted_date'].dt.year == 2025].copy()
month_order = ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec']

df_2025['job_posted_month'] = pd.Categorical(
    df_2025['job_posted_month'],
    categories=month_order,
    ordered=True
)
```

### ◆ Top Skills for the 5 Most Popular Data Roles

**QUESTION**: What are the most demanded skills for the top 5 most popular data roles?

**Step-By-Step Approach**

1. Clean-up skill column
2. Calculate skill count based on job title ('job_title_short' on our Dataset)
3. Calculate skill percentage
4. Plot final findings

**Example Implementation** 💻
```python
# Filter job postings for Canada
df_ca = df_2025[df_2025['job_country'] == 'Canada']

# Explode the skills column to analyze individual skills
df_skills = df_ca.explode('job_skills')

# Count skill occurrences per job role
df_skills_count = (df_skills.groupby(['job_skills', 'job_title_short'])
                   .size()
                   .reset_index(name='skill_count')
                   .sort_values(by='skill_count', ascending=False))

```

### ◆ Trend of Top Skills
**QUESTION:** How are in-demand skills trending for Data Analysts and Scientists in Canada?

**Step-By-Step Approach**
1. Aggregate skill counts monthly
2. Analyze based on the skill count to compare the frequency of each skills
3. Plot the monthly skill demand

**Example Implementation** 💻
```python
# Pivot the information for Data Analysts
df_da_pivot = (df_da_ex.pivot_table(index='job_posted_month', 
                                    columns='job_skills', 
                                    aggfunc='size', 
                                    fill_value=0))

# Identify top skills based on total frequency
df_da_pivot.loc['Total'] = df_da_pivot.sum()

# Sort columns and keep top 5 skills
sort_columns = df_da_pivot.loc['Total'].sort_values(ascending=False).index
df_da_pivot = df_da_pivot[sort_columns]
df_da_pivot = df_da_pivot.drop('Total')
df_da_pivot = df_da_pivot.iloc[:, :5]


# Pivot the information for Data Scientists
df_ds_pivot = (df_ds_ex.pivot_table(index='job_posted_month', 
                                    columns='job_skills', 
                                    aggfunc='size', 
                                    fill_value=0))

# Identify top skills based on total frequency
df_ds_pivot.loc['Total'] = df_ds_pivot.sum()
sort_columns = df_ds_pivot.loc['Total'].sort_values(ascending=False).index

# Sort columns and keep top 5 skills
df_ds_pivot = df_ds_pivot[sort_columns]
df_ds_pivot = df_ds_pivot.drop('Total')
df_ds_pivot = df_ds_pivot.iloc[:, :5]
```


### ◆ Salary Analysis

**QUESTION:** How well do jobs and skills pay for Data Analysts and Data Scientists?

**Step-By-Step Approach**
1. Evaluate median salary for top 6 data jobs
2. Find the median salary per skill for Data Analysts and Scientists
3. Visualize for highest paying skills and most demanded skills

**Example Implementation** 💻
```python
# Create the plot
fig, ax = plt.subplots(figsize=(10, 8))

roles_palette = {
    "Data Analyst": "#4C72B0",
    "Senior Data Scientist": "#DD8452",
    "Business Analyst": "#55A868",
    "Data Engineer": "#C44E52",
    "Software Engineer": "#8172B3",
    "Data Scientist": "#937860"
}

sns.violinplot(
    data=df_top6,
    x='salary_year_avg',
    y='job_title_short',
    hue='job_title_short',
    palette=roles_palette,
    order=order,
    dodge=False,
    legend=False,
    ax=ax
)

# Format axis
ax.xaxis.set_major_formatter(
    plt.FuncFormatter(lambda x, pos: f"${int(x/1000)}K")
)

ax.set_xlabel('Median Salary')
ax.set_title('Salary Distribution by Data Role')
ax.set_ylabel('')

plt.show()
```

### ◆ Optimal Skills to Learn
**QUESTION:** What is the most optimal skills to learn for Data Analysts and Data Scientists?

**Step-By-Step Approach**
1. Group skills to determine median salary and likelihood of being in posting
2. Visualize median salary vs percent skill demand
3. Determine if certain technologies are more prevalent

**Example Implementation** 💻
```python
# Extract technology data
df_tech = df_2025['job_type_skills'].drop_duplicates().dropna()

# Combine all dictionaries into a single structure
tech_dict = {}

for row in df_tech:
    row_dict = ast.literal_eval(row)  # Convert string to dictionary
    
    for key, value in row_dict.items():
        if key in tech_dict:
            tech_dict[key] += value
        else:
            tech_dict[key] = value

# Remove duplicates from skill lists
for key in tech_dict:
    tech_dict[key] = list(set(tech_dict[key]))

# Convert dictionary to DataFrame
df_technologies = pd.DataFrame(
    list(tech_dict.items()), 
    columns=['technology', 'skills']
)

# Explode skills for merging
df_technologies = df_technologies.explode('skills')

# Merge with high-demand skills
df_da_merge = pd.merge(
    df_da_highd.reset_index(),
    df_technologies,
    left_on='job_skills',
    right_on='skills'
)

df_ds_merge = pd.merge(
    df_ds_highd.reset_index(),
    df_technologies,
    left_on='job_skills',
    right_on='skills'
)
```


## ◼️ Tools Used ⚙️
- **Python**: The backbone of everything—this is where the magic happens. I used it to analyze and visualize job market data with pandas for data manipulation, matplotlib and seaborn for creating compelling visualizations, plus foundational Python skills to tie it all together.

- **Jupyter Notebooks**: Interactive environment for developing and documenting my analysis step-by-step, making it easy to experiment with code and share my findings.

- **Visual Studio Code**: My primary code editor for writing, testing, and managing Python scripts efficiently.

- **Git & GitHub**: Version control system used to track project progress and maintain a clean repository of my work.

## ◼️ Analysis 🛠️
### ◆ EDA (Exploratory Data Analysis)
#### ◇ Data Analyst Analysis
- **Top Locations** 
![Top locations DA CA](/Plots/1_EDA/top_locations_da.png)
- **Remote and Degree Requirement Jobs** 
![Degree/Remote](/Plots/1_EDA/home_degree_da.png)
- **Top companies**  
![Top companies DA CA](/Plots/1_EDA/top_companies_da.png)

#### ◇ Data Scientist Analysis
- **Top Locations** 
![Top locations DA CA](/Plots/1_EDA/top_locations_ds.png)
- **Remote and Degree Requirement Jobs** 
![Degree/Remote](/Plots/1_EDA/home_degree_ds.png)
- **Top companies**  
![Top companies DA CA](/Plots/1_EDA/top_companies_ds.png)

### ◆ Top Skills for the 5 Most Popular Data Roles

![Skill appereance on job postings (%)](/Plots/2_Skills_Count/skill_per_top_jobs.png)


### ◆ Trend of Top Skills

#### ◇ Data Analyst Analysis
![Skill Trend for Data Analyst](/Plots/3_Skill_Trend/skills_trend_da.png)

#### ◇ Data Scientist Analysis
![Skill Trend for Data Scientist](/Plots/3_Skill_Trend/skills_trend_ds.png)


### ◆ Salary Analysis
#### ◇ Median Salary for Top 6 Data Jobs
![Salary Distribution Top 6 Data Roles](/Plots/4_Salary_Analysis/salary_distribution_top_6_roles.png)

#### ◇ Most Paying vs. Most Common Skills Data Analyst
![Most paying vs. Most Common skills Salary for DA](/Plots/4_Salary_Analysis/most_pay_freq_skills_da.png)

#### ◇ Most Paying vs. Most Common Skills Data Scientist
![Most paying vs. Most Common skills Salary for DS](/Plots/4_Salary_Analysis/most_pay_freq_skills_ds.png)

### ◆ Optimal Skills to Learn

#### ◇ Data Analyst Analysis
- **Skills to Learn** 
![Skills to Learn DA](/Plots/5_Optimal_Skills/skills_to_learn_da.png)

- **Top Technologies** 
![Top technologies DA](/Plots/5_Optimal_Skills/tech_skills_da.png)

#### ◇ Data Scientist Analysis
- **Skills to Learn**
![Skills to Learn DS](/Plots/5_Optimal_Skills/skills_to_learn_ds.png)

- **Top Technologies** 
![Top technologies DS](/Plots/5_Optimal_Skills/tech_skills_ds.png)



## ◼️ Key Findings 🔎


## ◼️ Leaning Takeaways 📖


## ◼️ Conclusions 📝