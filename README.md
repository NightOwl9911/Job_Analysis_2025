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
This section presents the key findings derived from the data, focusing on understanding the current landscape of data-related roles in Canada. The analysis begins with an Exploratory Data Analysis (EDA) to uncover general patterns and trends in the job market before diving into more advanced insights.

The EDA focuses on identifying where opportunities are concentrated, how flexible these roles are, and which companies are actively hiring. Specifically, the analysis examines the top locations with the highest number of job postings, the prevalence of remote work and degree requirements, and the leading companies offering positions for both Data Analysts and Data Scientists.

By comparing these aspects across roles, this section provides a clearer picture of the job market structure and helps highlight where opportunities are most accessible. 📊🚀
#### ◇ Data Analyst Analysis
- **Top Locations** 
![Top locations DA CA](/Plots/1_EDA/top_locations_da.png)

The analysis shows that Toronto and Vancouver have the highest concentration of Data Analyst job postings in Canada. However, remote opportunities stand out as the most in-demand overall, surpassing any single physical location.
- **Remote and Degree Requirement Jobs** 
![Degree/Remote](/Plots/1_EDA/home_degree_da.png)

The analysis shows that remote opportunities for Data Analysts are relatively limited, representing a small portion of total job postings. Combined with the previous findings, this suggests that Data Analyst roles in Canada are primarily location-based and distributed across major cities rather than concentrated in remote positions.
- **Top companies**  
![Top companies DA CA](/Plots/1_EDA/top_companies_da.png)

The analysis shows that several companies are actively offering Data Analyst positions in Canada. While beBee Careers appears to have the highest number of postings, further inspection reveals that it functions as a job aggregator, connecting candidates with opportunities rather than directly hiring. Excluding such platforms, Mercor emerges as the leading company offering Data Analyst roles directly, making it the most prominent employer in this analysis.

#### ◇ Data Scientist Analysis
- **Top Locations** 
![Top locations DA CA](/Plots/1_EDA/top_locations_ds.png)

The analysis shows that Toronto stands out as the leading city for Data Scientist job opportunities in Canada, offering the highest number of postings. This makes it the most attractive location for professionals seeking Data Scientist roles in the country.

- **Remote and Degree Requirement Jobs** 
![Degree/Remote](/Plots/1_EDA/home_degree_ds.png)

The landscape shifts for Data Scientists compared to Data Analysts. Data Scientist roles are more likely to require on-site work, with fewer remote opportunities available. Additionally, a higher proportion of postings require a formal degree, indicating stricter educational requirements for this role.

- **Top companies**  
![Top companies DA CA](/Plots/1_EDA/top_companies_ds.png)
Similar to the Data Analyst role, beBee Careers appears as the top source of job postings; however, as a job aggregator, it does not directly offer positions. Excluding such platforms, Mercor stands out as the leading company providing Data Scientist job opportunities in Canada.


### ◆ Top Skills for the 5 Most Popular Data Roles

This analysis focuses on identifying the most in-demand skills across the top five most common data roles in the Canadian job market. By examining the frequency of skills listed in job postings, we aim to understand which technical competencies are most valued for each role.

Comparing these roles allows us to highlight both shared and role-specific skills, providing clearer insights into the key requirements for positions such as Data Analyst and Data Scientist. This helps identify which skills are essential to learn depending on the career path within the data field. 📊🚀

![Skill appereance on job postings (%)](/Plots/2_Skills_Count/skill_per_top_jobs.png)
The analysis reveals that Python and SQL consistently rank as the most in-demand skills across all data roles, making them essential for anyone pursuing a career in the data industry. In several roles, both skills appear in over 50% of job postings, meaning they are required in at least one out of every two positions. This also suggests that some postings may omit these skills, assuming them as fundamental requirements.

Additionally, cloud platforms such as Azure and AWS are present across all roles, highlighting their growing importance in the industry and reinforcing their value as key skills to learn.

### ◆ Trend of Top Skills
This analysis explores how the demand for key skills evolves over time in the Canadian data job market. By tracking job postings from January to June, we analyze the monthly trends of the most in-demand skills for both Data Analysts and Data Scientists.

Using a time-series approach, this section highlights how the relevance of specific skills changes over time, allowing us to identify consistent demand as well as emerging or declining trends. Additionally, the analysis provides a general overview of job posting activity across both roles, offering a broader perspective on market dynamics. 📈🚀

#### ◇ Data Analyst Analysis
![Skill Trend for Data Analyst](/Plots/3_Skill_Trend/skills_trend_da.png)

The trend analysis confirms that Python and SQL are the most consistently in-demand skills for Data Analysts, reinforcing their importance as core competencies when pursuing roles in this field.

Job postings also peak during the early months of the year, particularly between January and February, which may be influenced by new hiring cycles and budget allocations within companies.

Additionally, tools such as Excel and Tableau remain consistently present, highlighting the importance of analytical and visualization skills for Data Analysts.

#### ◇ Data Scientist Analysis
![Skill Trend for Data Scientist](/Plots/3_Skill_Trend/skills_trend_ds.png)

Similar to Data Analysts, Python and SQL remain the most in-demand skills for Data Scientists, with Python showing slightly higher demand across job postings.

Additionally, cloud platforms such as AWS and Azure play a more prominent role in Data Scientist positions, highlighting the importance of cloud and infrastructure knowledge in this field. Programming skills are also reinforced with the presence of R, further emphasizing the technical nature of the role.

Unlike Data Analyst roles, analytical and visualization tools are less prominent, indicating that Data Scientist positions are more focused on programming, modeling, and cloud-based technologies.


### ◆ Salary Analysis

This section explores the salary landscape of the data job market in Canada, focusing on both job roles and individual skills. The analysis begins by examining the distribution of salaries across the top six data roles using a violin plot, providing insights into variability, median values, and overall earning potential.

In addition, the analysis compares high-paying skills with the most in-demand (common) skills for both Data Analysts and Data Scientists. This allows us to identify not only which skills are frequently required, but also which ones offer the greatest financial return.

By combining these perspectives, this section provides a clearer understanding of how skill demand aligns with salary potential, helping guide more strategic learning decisions. 💰📊
#### ◇ Median Salary for Top 6 Data Jobs
![Salary Distribution Top 6 Data Roles](/Plots/4_Salary_Analysis/salary_distribution_top_6_roles.png)

The salary distribution shows that Data Scientist roles offer the highest earning potential among the analyzed data positions, with higher median salaries and a wider range of compensation.

In contrast, Data Analyst roles tend to have the lowest salary range, which is likely due to being an entry point into the data field. Additionally, Data Analyst salaries appear more consistent, with less variability compared to other roles, aside from a few higher-end outliers.

#### ◇ Most Paying vs. Most Common Skills Data Analyst
![Most paying vs. Most Common skills Salary for DA](/Plots/4_Salary_Analysis/most_pay_freq_skills_da.png)

The analysis shows a clear distinction between high-paying skills and the most in-demand skills for Data Analysts. While specialized technologies such as C, Cassandra, or Kafka are associated with higher salaries, they appear less frequently in job postings.

In contrast, more common skills like SQL and Python are consistently in high demand, even if they do not always correspond to the highest salaries. This suggests that while niche skills can increase earning potential, mastering core skills remains essential for entering and growing within the field.

#### ◇ Most Paying vs. Most Common Skills Data Scientist
![Most paying vs. Most Common skills Salary for DS](/Plots/4_Salary_Analysis/most_pay_freq_skills_ds.png)

Similar to Data Analysts, the analysis shows that some specialized skills are associated with higher salaries for Data Scientists. However, a key difference is that several high-paying skills also appear frequently in job postings, such as BigQuery and Java, indicating a stronger alignment between demand and compensation in this role.

It is also important to note that these results may be influenced by data limitations, as missing values could affect the representation of certain skills.

### ◆ Optimal Skills to Learn

This analysis aims to identify the most strategic skills to learn by evaluating the relationship between skill demand and salary potential. Using a scatter plot, we compare how frequently each skill appears in job postings against its associated salary, allowing us to pinpoint skills that offer both high demand and strong financial returns.

Beyond individual skills, the analysis also explores broader technology categories—such as programming languages, cloud platforms, and analytical tools—to understand which types of skills are most valuable in the data job market.

By combining these perspectives, this section highlights the optimal skills to focus on, helping guide more informed and efficient learning decisions. 📊🚀
#### ◇ Data Analyst Analysis
- **Skills to Learn** 
![Skills to Learn DA](/Plots/5_Optimal_Skills/skills_to_learn_da.png)

The analysis reinforces previous findings, with SQL clearly standing out as the most optimal skill for Data Analysts. Positioned in the top-right of the scatter plot, it combines both high demand and strong salary potential, making it a key skill to learn when pursuing a career in data analysis.

- **Top Technologies** 
![Top technologies DA](/Plots/5_Optimal_Skills/tech_skills_da.png)

The analysis shows that Data Analysts should focus on a combination of programming skills and analytical tools. Core programming languages such as SQL and Python stand out due to their strong demand and salary potential, making them essential skills.

Additionally, analytical tools like Excel, Power BI, and Tableau play an important role. Although each tool individually may not show extremely high frequency, their combined presence across job postings highlights their relevance, suggesting that mastering at least one—or several—of these tools can significantly improve job opportunities.

While cloud-related skills are present, their lower frequency indicates they are less critical for Data Analyst roles compared to programming and analytical tools.


#### ◇ Data Scientist Analysis
- **Skills to Learn**
![Skills to Learn DS](/Plots/5_Optimal_Skills/skills_to_learn_ds.png)

For Data Scientists, SQL remains an essential skill, but Python stands out as particularly valuable, combining both high demand and strong earning potential.

The analysis also highlights a greater presence of cloud technologies and programming skills, along with some analytical tools, indicating a broader and more technical skill set compared to Data Analysts.

Overall, this suggests that the Data Scientist role builds upon the foundations of data analysis, requiring a more advanced and diverse set of skills.

- **Top Technologies** 
![Top technologies DS](/Plots/5_Optimal_Skills/tech_skills_ds.png)

As highlighted in the analysis, Data Scientist roles place a stronger emphasis on programming and cloud-related skills compared to analytical tools. Skills such as Python, SQL, and cloud platforms (AWS, Azure, BigQuery) show higher frequency in job postings, indicating their importance in the field.

In contrast, analytical tools play a less prominent role, reinforcing the idea that Data Scientists require a more technical and engineering-oriented skill set.


## ◼️ Key Findings 🔎
- Python and SQL are the most essential skills across all data roles, consistently appearing as the most in-demand and widely required competencies.

- Cloud technologies (AWS, Azure, BigQuery) are increasingly important, particularly for Data Scientist roles, highlighting the growing need for infrastructure and data engineering knowledge.

- Data Analyst roles are more business-oriented, requiring a combination of programming skills and analytical tools such as Excel, Power BI, and Tableau.

- Data Scientist roles are more technical, with a stronger emphasis on programming, cloud platforms, and advanced data processing tools.

- Salary potential varies significantly by role, with Data Scientists and Senior roles offering the highest compensation, while Data Analyst positions provide more consistent but lower salary ranges.

- High-paying skills are not always the most in-demand, especially for Data Analysts, where niche technologies offer higher salaries but appear less frequently in job postings.

- The optimal strategy is to combine high-demand and high-paying skills, focusing first on core skills (Python, SQL) before expanding into specialized or high-value technologies.

## ◼️ Leaning Takeaways 📖
- This project allow me to develop skills that are fundamental to get a job as a Data Analyst/Scientist as in it, I was able to clean, transform and analyze a real-world dataset.

- While doing this project, I was able to get the importance of data-driven decision making in personal decisions such which skills to learn to become a Data Analyst/Scientist (not only in business contexts as per usual).

- I also gained a deeper understanding of how different data roles require different skill sets, helping me better define my own career path in the data field.

- Overall, this project helped me think more like a data analyst—asking questions, validating assumptions, and extracting actionable insights from data.

## ◼️ Conclusions 📝

This analysis provides a comprehensive view of the Canadian data job market, highlighting the skills that offer the best combination of demand and salary potential.

For those starting their journey, focusing on core skills such as Python and SQL is essential, as they form the foundation for both Data Analyst and Data Scientist roles.

As professionals progress, expanding into cloud technologies and advanced programming skills becomes increasingly important, particularly for more technical roles like Data Scientist.

Additionally, the findings emphasize that strategic learning is key—rather than focusing solely on high-paying or trending skills, the most effective approach is to build a balanced skill set that aligns with both market demand and long-term career goals.

Ultimately, this project demonstrates how data analysis can be used as a powerful tool to guide career decisions, turning raw job market data into actionable insights. 🚀