# Overview
Welcome to my analysis of the data job market, focusing on Data Analyst roles. This project right here was created out of a desire to navigate and understand the job market more effectively.


# The Questions
Below are the questions I want to answer In my project

1. What are the skills most in demand for the top 3 data roles
2. How are in demand skills trending for data analysts
3. How well do job and skills pay for data analysts
4. What are the optimal skills for data analysts to learn

# Tools I used

Python: The backbone of my analysis, allowing me to analyze the data and find critical insights. I also used the following python libraries:              
 -Pandas Library: This was used to analyze the data                  
 -Matplotlib library: To visualize the data
 -Seaborn Library: Helped me create more advanced Visuals




# The Analysis

## 1. What are the most demanded skills for the top 3 popular data roles?
To find the most demanded skills for the top 3 most popular data roles. I filtered out those positions by which ones were the most popular, and got the top 5 skills for these top 3 roles. This query highlights the most popular job titles and their top skills, showing which skills I should pay attention to depending on the role I'm targeting.

View my notebook with detailed steps here:
[skill.count.ipynb](project\skills.count.ipynb)

### Visualize Data

``` python

job_titles = sorted(df_skills_count['job_title_short'].unique().tolist()[:3])

fig, ax = plt.subplots(len(job_titles), 1, figsize=(8, 5 * len(job_titles)))  # stacked vertically


if len(job_titles) == 1:
    ax = [ax]

for i, job_title in enumerate(job_titles):
    df_plot = df_skills_perc[df_skills_perc['job_title_short'] == job_title].head(5)
    df_plot.plot(kind='barh', x='job_skills', y='skills_percent', ax=ax[i], legend=False, title=job_title)
    ax[i].set_xlabel("Skill Count")
    ax[i].set_ylabel("")
    ax[i].set_xlim(0,30)
    ax[i].invert_yaxis()

fig.suptitle('likelihood of skills requested in US job postings', fontsize=15)
plt.tight_layout(h_pad=0.5)
plt.show()
```

### Results
![Visualization of top skills](project\output.png)


### Insights
-Python is a versatile skill, highly demanded across all the three roles, but most prominently for Data Scientists and Data engineers.
-SQL is the most requested skill for Data Analysts and Data Scientists, with it in over half the job postings for both roles. For, Data Engineers, Python is the most sought-after skill, appeairng in 68% of job postings.
-Data Engineers require more specialized technical skills(AWS, Azure) compared to Data Analysts and Data Scientists who are expected to be proficient in more general data management ad analysis tools( Excel, Tableau)

## 2. How are in demand skills trending for Data Analysts

### Visualize Data

``` Pythom


DA_Total = df_DA_US.groupby('job_posted_month_no').size()


df_da_us_perc = df_da_us_pivot.div(DA_Total / 100, axis=0).reset_index()


df_da_us_perc = df_da_us_perc[pd.to_numeric(df_da_us_perc['job_posted_month_no'], errors='coerce').notna()]
df_da_us_perc['job_posted_month_no'] = df_da_us_perc['job_posted_month_no'].astype(int)


df_da_us_perc['job_posted_month'] = df_da_us_perc['job_posted_month_no'].apply(
    lambda x: pd.to_datetime(x, format='%m').strftime('%b')
)


df_da_us_perc = df_da_us_perc.set_index('job_posted_month').drop(columns='job_posted_month_no')

df_plot= df_da_us_perc.iloc[:, :5]

sns.lineplot(data=df_plot, dashes=False, palette='tab10')
sns.set_theme(style='ticks')
sns.despine()

plt.title('Trending top skills for Data Analysts in the US')
plt.ylabel('Likelihood in Job Posting')
plt.xlabel('')
plt.legend().remove()

from matplotlib.ticker import PercentFormatter
ax = plt.gca()
ax.yaxis.set_major_formatter(PercentFormatter(xmax=100))


for i in range(5):
    plt.text(
        x=len(df_plot) - 1 + 0.3,                      # X position slightly to the right
        y=df_plot.iloc[-1, i],                         # Last Y value of each line
        s=df_plot.columns[i],                          # Label = column name
        fontsize=9,
        va='center'
    )
```

![Trending Top Skills For Data Analysts](project\3.png)
*Graph visualizing the trending top skills for data analysts*

### Insights:
-SQL remains the most consistently demanded skill throughout the year, although it shows a gradual decrease in demand.
-Excel experienced a significant increase in demand starting around september, surpassing both Python and Tableau by the end of the year.
-Both Python and Tableau show relatively stable ddemand throughout the year with some fluctuations but remain essential skills for Data Analysts. Power BI, while less demanded compared to others, shows a slight upward trend towards the year's end.

### 3. How well do jobs and skills pay for Data Analysts?

#### Visualize Data

```Python
fig,ax= plt.subplots(2,1)

sns.set_theme(style='ticks')

sns.barplot(data= df_DA_top_pay, x='median', y= df_DA_top_pay.index, hue='median', ax=ax[0], palette='dark:b_r')
ax[0].legend().remove()

ax[0].set_title('Top 10 Highest paid skills for Data Analyst')
ax[0].set_ylabel('')
ax[0].set_xlabel('')
ax[0].xaxis.set_major_formatter(plt.FuncFormatter(lambda x, _: f'${int(x/1000)}k'))

sns.barplot(data=df_DA_skills, x='median', y=df_DA_skills.index, hue='median', ax=ax[1], palette='light:b')
ax[1].legend().remove()

ax[1].set_title('Top 10 Most In Demand Skills for Data Analytics')
ax[1].set_ylabel('')
ax[1].set_xlabel('Median Salary USD')
ax[1].xaxis.set_major_formatter(plt.FuncFormatter(lambda x, _: f'${int(x/1000)}k'))

plt.tight_layout()
plt.show()
```
#### Results

[Salary Distribution Of Data Jobs](project\4.png)
*Box Plot Visualizing thr salary distributions for the top 6 data job titles*

### Insights
-Senior Data Engineer and Senior Data scientist rples show a considerable number of outliers on the higher end of the salary spectrum, suggesting that exceptional skills or circumstances can lead to high pay in these roles. In contrast, Data Analyst roles demonstrate more consistency on salary, with fewer outliers.

-The median salary increase with the seniority and specialization of the roles. Senior roles not only have higher median salaries, but also larger differences in typical salaries, reflecting greater variance in compensation as resposibilities increase.

## 4. What is the most optimal skill to learn for Data Analysts?

### Visualize Data

```Python

from adjustText import adjust_text
import matplotlib.pyplot as plt

ax = df_DA_skills_high_demand.plot(kind='scatter', x='skill_percent', y='median_salary', figsize=(10, 6))

texts = []
for i, label in enumerate(df_DA_skills_high_demand.index):
    x = df_DA_skills_high_demand['skill_percent'].iloc[i]
    y = df_DA_skills_high_demand['median_salary'].iloc[i]
    texts.append(plt.text(x, y, label, fontsize=9))

adjust_text(texts, arrowprops=dict(arrowstyle='->', color='gray'))

plt.xlabel('Percent of data analyst jobs (%)')
plt.ylabel('Median Salary (USD)')
plt.title('Optimal Skills for Data Analytics Roles')

from matplotlib.ticker import PercentFormatter
ax=plt.gca()
ax.yaxis.set_major_formatter(plt.FuncFormatter(lambda y, pos: f'${int(y/1000)}k'))
ax.xaxis.set_major_formatter((PercentFormatter(decimals=0)))

plt.tight_layout()
plt.show()
```
#### Results
[Best Optimal skills for data analysts](project\5.png)

#### Insights
-The scatter plot shows that most of the programming skills tend to cluster at higher salary levels compared to other categories, indicating that programming expertise might offer greater salary benefits within the data analytics field.
-Analyst tools, including Tableau and Power BI, are prevalent in job postings and offer competitive salaries, showing that visualization and data analysis software are crucial for current data roles. This category not only has good salaries but is also versatile accross different types of data tasks.

# What I learned

Throughout this project, I deepened my understanding of the data analyst job market and enhanced my technical skills in Python, especially in data manipulation and visualization. Here are a few specific things I learned.

- Advanced Python Usage- Utilizing Libraries such as pandas for data manipulation, Seaborn and Matplotlib for Data Visualization and other libraries helped me perform complex data analysis tasks more efficiently


- Data Cleaning- I learned that thorough Data Cleaning and preparations are crucial before any analysis can be conducted

- Strategic Skill Analysis: The project emphasized the importance of one's skills with the market demand. Understanding the relationship between skill demand, salary and job availability.

# Conclusion

This exploration into the data analyst job market has been extremely informative, highlighting the critical skills and trends that shape this evolving field. The insights I got enhance my understanding and provide actionable guidance for anyone looking to advance their career in data analytics. As the market continues to change, ongoing analysis will be essential to stay ahead in data analytics. This project is a good foundation for future explorations and underscores the importance of continuous learning and adaptation in the data field.