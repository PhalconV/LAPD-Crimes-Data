# LAPD-Crimes-Data
LAPD Crimes Data Analysis
This report presents the analysis of crime data provided by the Los Angeles Police Department (LAPD). The goal of this analysis was to identify patterns in criminal activities across different areas, types of crimes, victim demographics, and time periods. By using Python's Pandas and Seaborn libraries, I visualized crime trends and provided actionable insights. Key findings reveal that the area with the name Central had the highest crime rate, and crimes were most frequent on Mondays and afternoon periods across different days. Additionally, the majority of victims were people of age group 26 to 34 and mostly males’ victims, and Identity theft is the most common crime was the most common offense. Hispanic, White, and Black tops the list of victim’s descent.

## Introduction
Los Angeles is known for its vibrant entertainment industry. It also faces significant crime-related challenges due to its large population. The LAPD aims to utilize data-driven approaches to allocate resources more effectively and reduce crime rates in the city. The purpose of this analysis is to identify patterns in criminal behavior based on the crime dataset provided, which contains details such as area name, crime type, victim demographics, descent of victim, crime status, and time of occurrence. The insights gained from this analysis will help inform future decision-making in terms of resource allocation and policy-making.

## Objective
The main objectives of this analysis are:
- To identify crime distribution across different areas of Los Angeles.
- To examine the most common types of crimes.
- To analyze crime distribution across various demographics such as gender, race, and age.
- To investigate temporal patterns in crime, including the time of day, day of the week, and month.
- To provide recommendations based on the identified trends

## Methodology
The crime data was processed and analyzed using Python's Pandas library for data manipulation and Seaborn for data visualization. Key steps involved:
- Data cleaning: Handling missing values and correcting data types (e.g., converting dates).
- Grouping and counting: Using. groupby() and .count() to summarize data for different groups, such as areas and crime types.
- Sorting: Ordering the data by frequency to identify significant patterns.
- Visualization: Using bar plots, histograms, and heatmaps to represent data trends visually.
- Time-based analysis: Extracting time features such as day of the week, month, and time of day for temporal trend analysis.

### Data Source
DataCamp

## Tool
Python

## Data Analysis
### A. Crime Distribution Across Areas
- Analysis: Crimes were grouped by area using the AREA NAME column, and the frequency of crimes in each area was plotted.
- Key Findings: Area with the name Central experienced the highest crime rates, with a total of 14944 crimes. Foothill had the lowest crime rates, with only 5983 crimes.
```python
# Areas and the number of crimes commimted
crimes.groupby("AREA NAME")["DR_NO"].count().sort_values(ascending = False)

grouped_areas = crimes.groupby("AREA NAME")["DR_NO"].count()
# Resetting the index
grouped_areas = grouped_areas.reset_index()
grouped_areas.columns = ["AREA NAME", "Crime Count"]
grouped_areas_sorted = grouped_areas.sort_values("Crime Count", ascending = False)

# Visualization
sns.catplot(data=grouped_areas_sorted, x = "Crime Count", y = "AREA NAME", kind = "bar", palette = "viridis")
# plot
plt.title("Crime Count by Area Name")
plt.xlabel("Number of Crimes")
plt.ylabel("Area Name")
plt.xticks(rotation = 60)
plt.show()
```

### B. Common Types of Crimes
- Analysis: Crimes were grouped by Crm Cd Desc to determine the most frequent crime types.
- Key Findings: The most common type of crime was Theft of Identity, accounting for 12.21% of total crimes. Simple Assault followed closely with 10.60% of total crimes.
```python

# Extract Percentage of top 2 crimes# occurrences of each crime type
crime_type_counts = crimes["Crm Cd Desc"].value_counts()
# percentage for each crime type
crime_type_percentage = (crime_type_counts / crime_type_counts.sum()) * 100
# most common crime type and its percentage
most_common_crime = crime_type_counts.idxmax()
most_common_crime_percentage = crime_type_percentage.max()
# second most common crime type and its percentage
second_most_common_crime = crime_type_counts.index[1]
second_most_common_crime_percentage = crime_type_percentage.iloc[1]
print(f"The most common type of crime was {most_common_crime}, accounting for {most_common_crime_percentage:.2f}% of total crimes.")
print(f"{second_most_common_crime} followed closely with {second_most_common_crime_percentage:.2f}% of total crimes.")

# Visualizing Top 10 Crimes
crime_type = crimes.groupby("Crm Cd Desc")["DR_NO"].count().sort_values(ascending = False)
crime_type
sns.barplot(x = crime_type.index[:10], y = crime_type.values[:10])
plt.title("Top 10 Crimes Committed")
plt.xlabel("Crime Type")
plt.ylabel("Number of Crimes")
plt.xticks(rotation = 90)
plt.show()


```

### C. Crime Distribution by Victim Demographics
- Analysis: We examined how crime was distributed based on victim age, gender, and descent.
- Key Findings: The majority of victims were between 26 and 34, with Male victims being more prevalent. Crimes involving Hispanic/Latin/Mexican made up 39.91% of the total crimes.
```python
# Identify the number of crimes committed against victims of different age groups
bins = [0, 17, 25, 34, 44, 54, 64, 100]
labels = ["0-17", "18-25", "26-34", "35-44", "45-54", "55-64", "65+"]
crimes["Age Ranges"] = pd.cut(crimes["Vict Age"], labels = labels, bins = bins, right = True)
victim_ages = crimes.groupby("Age Ranges")["Vict Age"].count()
ser = pd.Series(victim_ages)
print(ser)

# victim gender distribution
gender_dist = crimes.groupby("Vict Sex")["DR_NO"].count()
gender_dist

# Count the number of male and female victims
gender_distribution = crimes["Vict Sex"].value_counts()
print(gender_distribution)
# Calculate the percentage of each gender
gender_percentage = (gender_distribution / gender_distribution.sum()) * 100
print(gender_percentage)
# Identify the gender with the highest count
most_prevalent_gender = gender_distribution.idxmax()

# table showing victims of crime by descent
# There are 10 records with null values in their descent and 4548 unknowns
descent_crimes = crimes.groupby("Descent_full")["DR_NO"].count()
descent_crimes

descent_crimes_d = descent_crimes.reset_index()
descent_crimes_d.columns = ["Descent_full", "DR_NO"]
descent_crimes_sorted = descent_crimes_d.sort_values(by = "DR_NO", ascending = False)
sns.barplot(data = descent_crimes_sorted, x = "Descent_full", y = "DR_NO")
plt.yscale('log')
plt.xticks(rotation=45, ha='right')
plt.show()
```

### D. Temporal Patterns in Crime
#### 1.	Time of Day:
- Analysis: Crimes were grouped by time of day (morning, afternoon, evening, night) using the TIME OCC column.
- Key Findings: Most crimes occurred during the Afternoon, with a peak at 12 noon.
```python
bins = [0,6,12,18,24]
labels = ["Night", "Morning", "Afternoon", "Evening"]
crimes["crime_period"] = pd.cut(crimes["Hour_OCC"], bins = bins, labels = labels, right = False)
grouped_this = crimes.groupby("crime_period")["Hour_OCC"].count()
period_day = grouped_this.sort_values(ascending = False)
period_day = period_day.reset_index()
period_day.columns = ["crime_period", "crime_count"]
period_day_sorted = period_day.sort_values("crime_period" ,ascending = False)
sns.catplot(data = period_day_sorted, x = "crime_period", y = "crime_count", kind = "bar", hue = "crime_period")
# plot
plt.title("Period of the day crime was committed")
plt.xlabel("Period Crime is Committed")
plt.ylabel("Number of Crimes Committed")
plt.xticks(rotation = 60)
plt.show()

```
#### 2.	Day of the Week:
- Analysis: Crime occurrences were grouped by day of the week.
- Key Findings: Crime rates were highest on Mondays, with 28452 incidents, while Sunday had the lowest crime rates.
```python
# Week day trend
crimes.groupby("Day_word")["DR_NO"].count().sort_values(ascending = False)
```
#### 3.	Month of the Year:
o	Analysis: I analyzed crime trends over different months.
o	Key Findings: The month of June experienced the highest crime rate, potentially due to factors such as Tourism surge, school holidays, holiday and even related factors, alcohol and substance use, and law enforcement resource allocation etc.
```python
# Monthly table
crimes.groupby("Month_word")["DR_NO"].count()

# Month trend visualization
grouped_month = crimes.groupby("Month_word")["DR_NO"].count()
grouped_month = grouped_month.reset_index()
grouped_month.columns = ["Month_word", "Month_count"]
grouped_month_sorted = grouped_month.sort_values("Month_count", ascending = False)
sns.catplot(data = grouped_month_sorted, x = "Month_word", y = "Month_count", kind = "bar")
# plot
plt.title("Crime Count by Month")
plt.xlabel("Month of Crime")
plt.ylabel("Number of Crimes")
plt.xticks(rotation = 60)
plt.show()
```
### Heatmaps
```python
# Creating heatmap to visualize areas, crime period, and rate
area_time_pivot = crimes.pivot_table(index ="AREA NAME", columns = "crime_period", values = "DR_NO", aggfunc = "count", fill_value = 0)
plt.figure(figsize = (12, 6))
sns.heatmap(area_time_pivot, cmap = "Blues", annot = True, fmt="d")
plt.title("Crimes by Area and Time of Day")
plt.xlabel("Time of Day")
plt.ylabel("Area")
plt.show()


# Creating heatmap for weekday and hours of the day
hour_weekday_pivot = crimes.pivot_table(index = "Weekday", columns = "Hour_OCC", values = "DR_NO", aggfunc = "count", fill_value = 0)

plt.figure(figsize = (12, 6))
sns.heatmap(hour_weekday_pivot, cmap = "coolwarm", annot = False)
plt.title("Crimes by Day of the Week and Hour")
plt.xlabel("Hour of the Day")
plt.ylabel("Day of the Week")
plt.show()


# Creating heatmap for weekday and period of crime
time_weekday_pivot = crimes.pivot_table(index = "Weekday", columns = "crime_period", values = "DR_NO", aggfunc = "count", fill_value = 0)

plt.figure(figsize = (8, 5))
sns.heatmap(time_weekday_pivot, cmap = "YlGnBu", annot = True, fmt = "d")
plt.title("Crimes by Time Period and Weekday")
plt.xlabel("Time of Day")
plt.ylabel("Day of the Week")
plt.show()
```

## Key Insights:
- Area name Central experiences significantly higher crime rates, suggesting a need for increased police presence and preventive measures in this area.
- Identity Theft is the most common crime, indicating the necessity for targeted crime prevention programs focused on this type of offense.
- Crimes are more frequent during 12 noon, suggesting that additional patrols during this time could help reduce crime rates.
- Male gender and age group 26 to 34 are most affected, highlighting the importance of victim support services for this demographic.

## Recommendations
- Allocate more police resources to high-crime areas such as Central.
- Implement targeted crime prevention strategies to reduce Theft in general, especially Identity Theft.
- Increase patrols during high-crime periods, particularly in the Afternoon.
- Focus victim support services on Hispanics/Latin/Mexicans, who are most affected by crimes.

This analysis of LAPD crime data provides valuable insights into the distribution of crime across different areas, victim demographics, and time periods. By understanding these patterns, the LAPD can allocate resources more efficiently and implement more targeted crime prevention strategies. Continued analysis of updated crime data will be essential to track the effectiveness of these measures and adjust strategies as needed.
