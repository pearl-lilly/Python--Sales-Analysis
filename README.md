# Python--Sales-Analysis
# Overview

Welcome to my analysis of sales data, focusing on uncovering trends, performance metrics, and customer behavior. This project was created to provide actionable insights into sales performance, helping optimize strategies for products, regions, and overall revenue growth.
The dataset contains detailed information on orders, sales amounts, ratings, food types, states, and cities. Using Python, I explore key questions to understand patterns in sales and customer engagement.

# Key Performance Indicators (KPIs)

These are the main metrics tracked in this project:

1. Total Sales: The sum of all sales revenue in the dataset.
2. Average Rating: The mean of customer ratings to measure satisfaction.
3. Average Order Value: The mean sales amount per order.
4. Rating Count: Total number of ratings submitted by customers.
5. Total Orders: Total number of orders in the dataset.

# Business Questions

Below are the questions this analysis seeks to answer:

1. What is the monthly sales trend?
2. How does daily sales trend?
3. What are the total sales by food type?
4. What are the total sales by state?
5. What does a quarterly performance summary reveal?
6. Which are the top 5 cities by sales?
7. How does the weekly sales trend look?

# Tools I Used
To perform this analysis, I leveraged the following tools:

- **Python:** For all data manipulation and analysis.
- **Pandas:** To clean, aggregate, and analyze the dataset.
- **Matplotlib & Seaborn:** For data visualization.
- **Jupyter Notebooks:** To combine code, visualizations, and notes.
- **Visual Studio Code:** For writing and testing scripts.
- **Git & GitHub:** For version control and sharing the project.

  # Data Preparation and Cleanup

This section outlines the steps taken to prepare the data for analysis, ensuring accuracy and usability.

## Import & Clean Up Data

I start by importing necessary libraries and loading the dataset, followed by initial data cleaning tasks to ensure data quality.

```python
# Importing Libraries
import pandas as pd
import numpy as np
import seaborn as sns
import matplotlib.pyplot as plt
import plotly.express as px
!pip install folium
 

# Loading Data
df = pd.read_excel("C:/Users/HP/Downloads/swiggy_data.xlsx")
#  Data Cleaning & Preparation

Before performing any analysis, the dataset was thoroughly cleaned and prepared to ensure accurate and reliable results. Below is the complete data cleaning workflow used in this project.
##  1. Initial Data Inspection

  python
# Check first few rows
df.head()
# Check dataset shape
df.shape
# Check column names
df.columns
# Check data types
df.info()
# Check summary statistics
df.describe()
# Check missing values
df.isnull().sum()
# Identify missing values
df.isnull().sum()

# Drop rows with missing critical values
df = df.dropna(subset=["order_date", "price_(inr)"])
# Fill missing ratings with median (if applicable)
df["rating"] = df["rating"].fillna(df["rating"].median())
# Fill missing rating_count with 0
df["rating_count"] = df["rating_count"].fillna(0)

# Check duplicates
df.duplicated().sum()
# Remove duplicates
df = df.drop_duplicates()

# Convert order_date to datetime
df["order_date"] = pd.to_datetime(df["order_date"])
# Ensure numeric columns are properly formatted
df["price_(inr)"] = pd.to_numeric(df["price_(inr)"], errors="coerce")
df["rating"] = pd.to_numeric(df["rating"], errors="coerce")
df["rating_count"] = pd.to_numeric(df["rating_count"], errors="coerce")

```

# 📊 Key Performance Indicators (KPIs)

To evaluate overall business performance, the following KPIs were calculated:

---
```python
1️⃣ Total Sales

**Definition:** The total revenue generated from all orders.

**Formula:**
total_sales = df["Price (INR)"]. sum()
print ("Total_Sales:", round(total_sales,2))

Result: ₹ 53012505.77

Business Importance:
Measures overall business revenue and growth performance.

Average Order Value (AOV)

Definition: The average revenue generated per order.

Formula:

average_order_value = df["Price (INR)"]. mean()
print ("Average Order Value:", round(average_order_value,2))

Result: ₹ 268.51

Business Importance:
Helps understand customer spending behavior and pricing effectiveness.

Average Rating

Definition: The mean customer satisfaction score.

Formula: average_rating = df["Rating"]. mean()
print ("average_rating", round(average_rating,2))

=Result: 4.34

Business Importance:
Indicates customer satisfaction and service/product quality.

Rating Count

Definition: Total number of ratings submitted.

Formula: rating_count = df["Rating Count"]. sum()
print ("Rating Count:", round(rating_count,2))

Result:5591574

Business Importance:
Shows customer engagement and product popularity.

Total Orders

Definition: Total number of transactions recorded.

Formula:Total_Orders = df["Order Date"]. count()
print ("Total Order:", round(Total_Orders,2))

Result: 197430 orders

Business Importance:
Reflects demand level and business activity.
```

# The Analysis 

## 1. What is the Monthly Sales Trend?

To understand overall revenue performance over time, I analyzed monthly sales trends. I grouped the data by month using the Order Date column and calculated total revenue for each month.

This analysis helps identify seasonality, peak revenue periods, and overall growth patterns.


---

### Visualize Data

```python
df["YearMonth"] = df["Order Date"].dt.to_period("M").dt.to_timestamp()

monthly_revenue = (
    df.groupby("YearMonth")["Price (INR)"]
    .sum()
    .reset_index()
)

monthly_revenue = monthly_revenue.sort_values("YearMonth")

max_value = monthly_revenue["Price (INR)"].max()
max_month = monthly_revenue.loc[
    monthly_revenue["Price (INR)"] == max_value, "YearMonth"
].values[0]

plt.figure(figsize=(12, 5))

plt.plot(
    monthly_revenue["YearMonth"],
    monthly_revenue["Price (INR)"],
    marker='o',
    linewidth=2
)

plt.scatter(
    max_month,
    max_value,
    s=120,
    zorder=5
)

plt.text(
    max_month,
    max_value,
    f" Highest: {int(max_value):,}",
    fontsize=10,
    verticalalignment="bottom"
)

plt.gca().xaxis.set_major_formatter(mdates.DateFormatter('%b %Y'))
plt.gca().xaxis.set_major_locator(mdates.MonthLocator(interval=2))

plt.xlabel("Month")
plt.ylabel("Revenue (INR)")
plt.title("Monthly Revenue Trend")

plt.grid(True, linestyle="--", alpha=0.4)
plt.tight_layout()
plt.show()

```

### Result
<img width="554" height="253" alt="image" src="https://github.com/user-attachments/assets/8fc7b8d4-89c9-468b-853c-a9573756ef3b" />

*Line chart visualizing total revenue per month, with the highest revenue month highlighted*

### Insights:
- January recorded the highest revenue, indicating a very strong start to the year.
- A sharp decline occurred in February, which may be due to post-holiday reduced spending or fewer transactions.
- From March to May, revenue showed a steady recovery, suggesting improved sales performance and customer activity.
- June experienced another drop in revenue before the business regained momentum.
- Revenue increased consistently in July and August, showing a positive upward trend toward the end of the period.
Overall, despite mid-year fluctuations, the general trend indicates recovery and growth after February.

## What is the Daily Sales Trend

To analyze daily sales performance, I extracted the day name from the Order Date column and grouped the data by each day of the week. I then calculated the total revenue for each day to identify which days generate the highest and lowest sales.

This analysis helps understand customer purchasing behavior across the week and can guide marketing strategies or promotional campaigns. 


---

### Visualize Data

```python
df["DayName"] = pd.to_datetime(df["Order Date"]).dt.day_name()

daily_revenue = (
    df.groupby("DayName")["Price (INR)"]
    .sum()
    .reindex(["Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday", "Sunday"])
)

plt.figure(figsize=(10, 5))

bars = plt.bar(
    daily_revenue.index,
    daily_revenue.values,
    color="#4C72B0"
)

for bar in bars:
    height = bar.get_height()
    plt.text(
        bar.get_x() + bar.get_width() / 2,
        height,
        f"{height:,.0f}",
        ha="center",
        va="bottom",
        fontsize=9
    )

plt.title("Daily Revenue Trend", fontsize=14, fontweight="bold")
plt.xlabel("Day")
plt.ylabel("Revenue (INR)")
plt.xticks(rotation=30)
plt.grid(axis="y", linestyle="--", alpha=0.4)
plt.tight_layout()
plt.show()

```
### Results
<img width="561" height="281" alt="image" src="https://github.com/user-attachments/assets/f702464f-e33d-45f2-bfa0-3edad8be27b2" />

### Insight 
Revenue is fairly consistent across the week, showing stable daily sales performance.
Tuesday recorded the lowest revenue, indicating reduced customer activity early in the week.
Sales begin to increase from Wednesday and continue rising toward the weekend.
Saturday generated the highest revenue, showing that customers purchase more during weekends.
Although revenue drops slightly on Sunday, it remains higher than most weekdays.
This pattern suggests that customer purchasing behavior is stronger toward the end of the week, especially on Saturdays.

### What is the Total Sales by Food Type?

To analyze revenue contribution by food category, I classified each dish as Veg or Non-Veg using keyword matching on the Dish Name column.

If a dish name contained keywords such as chicken, egg, fish, mutton, biryani, kebab, etc., it was categorized as Non-Veg. All other dishes were classified as Veg.

I then grouped the data by food category and calculated the total revenue for each group. A donut chart was used to clearly show the revenue distribution.


### Visualize Data

```python
non_veg_keywords = [
    "chicken", "egg", "fish", "mutton",
    "prawn", "biryani", "kabab", "kebab",
    "non-veg", "non veg"
]

df["Food Category"] = np.where(
    df["Dish Name"].str.lower().str.contains("|".join(non_veg_keywords), na=False),
    "Non-Veg",
    "Veg"
)

food_revenue = df.groupby("Food Category")["Price (INR)"].sum().reset_index()

fig = px.pie(
    food_revenue,
    values="Price (INR)",
    names="Food Category",
    hole=0.5,
    title="Revenue Contribution: Veg vs Non-Veg"
)

fig.update_traces(textinfo="percent+label", pull=[0.05, 0])

fig.update_layout(
    height=500,
    margin=dict(t=68, b=48, l=48, r=40)
)

fig.show()

```

### Results
<img width="617" height="284" alt="image" src="https://github.com/user-attachments/assets/92e73f0e-bf4a-43b8-8011-e5001256fdbe" />



### Insights:
- Vegetarian items generate 62.9% of total revenue, making them the primary revenue driver of the business. This indicates a strong market preference for veg offerings and highlights them as a strategic growth segment.

- With nearly two-thirds of total sales coming from Veg items, the business could prioritize:
Expanding high-performing vegetarian dishes
Introducing premium or combo veg meals
Running targeted promotions around vegetarian bestsellers

- Non-Veg items contribute 37.1% of revenue, representing a solid secondary revenue stream. While smaller, this segment still holds substantial value and may offer opportunities for upselling or bundling strategies.

- The revenue gap between Veg and Non-Veg suggests that menu optimization, pricing strategy, and marketing campaigns should lean toward strengthening the dominant Veg category while maintaining variety in Non-Veg options.
  

### What is the Total Sales by State

To understand regional performance, I grouped the dataset by State and calculated the total revenue generated in each state.

The results were sorted in descending order to clearly identify top-performing states. A horizontal bar chart was used for better readability and easier comparison across regions.

This analysis helps identify high-performing markets and potential areas for expansion or targeted marketing.

### Visualize Data

```python
state_sales = df.groupby("State", as_index=False)["Price (INR)"].sum()
state_sales = state_sales.sort_values("Price (INR)", ascending=False)

fig = px.bar(
    state_sales,
    x="Price (INR)",
    y="State",
    orientation="h",
    title="Revenue by State (INR)",
    text="Price (INR)",
    color_discrete_sequence=["#4C72B0"]
)

fig.update_layout(
    height=600,
    yaxis=dict(autorange="reversed"),
    title=dict(font_size=20, font_family="Arial", x=0.5),
    xaxis_title="Revenue (INR)",
    yaxis_title="State",
    font=dict(family="Arial", size=12, color="#333333"),
    plot_bgcolor="white",
    paper_bgcolor="white",
    margin=dict(l=100, r=50, t=80, b=50)
)

fig.update_traces(
    texttemplate="₹%{text:,.0f}",
    textposition="outside",
    marker_line_color='rgba(0,0,0,0)',
    marker_line_width=0
)

fig.show()

```
### Results
<img width="615" height="321" alt="image" src="https://github.com/user-attachments/assets/0d06816b-2d66-4c0a-b206-7235edcb116d" />


### Insights:
- Karnataka generates significantly higher revenue than every other state, making it the primary revenue driver.
- Telangana and Delhi are the next strongest contributors, but still far behind Karnataka.
- States like Tamil Nadu, Punjab, and Madhya Pradesh show moderate performance.
- Several states such as Nagaland, Tripura, and Jharkhand contribute very little to overall revenue.
This indicates that revenue is highly concentrated in a few key states, while many regions remain underperforming and may present opportunities for market expansion

### How Did the Business Perform Quarterly (Sales vs Rating)

To evaluate overall business performance across quarters, I analyzed two key metrics:

Total Sales (INR)

Average Customer Rating

I grouped the data by quarter and calculated both total revenue and average rating.
To clearly compare performance trends, I used a dual-axis line chart — one axis for sales and the other for ratings.

This helps assess whether higher sales align with better customer satisfaction.

### Visualize Data

```python
quarterly_summary = quarterly_summary.sort_values("Quarter")

fig, ax1 = plt.subplots(figsize=(12, 5))

ax1.plot(
    quarterly_summary["Quarter"],
    quarterly_summary["Total_Sales"],
    marker='o',
    linewidth=2
)

ax1.set_xlabel("Quarter")
ax1.set_ylabel("Total Sales (INR)")
ax1.grid(True, linestyle="--", alpha=0.4)

ax2 = ax1.twinx()

ax2.plot(
    quarterly_summary["Quarter"],
    quarterly_summary["Avg_Rating"],
    marker='o',
    linewidth=2
)

ax2.set_ylabel("Average Rating")

plt.title("Quarterly Performance: Sales vs Rating")
plt.xticks(rotation=45)
plt.tight_layout()
plt.show()
```

### Results
<img width="620" height="263" alt="image" src="https://github.com/user-attachments/assets/d378eee7-6ce2-4435-aa95-76d68d78e5e4" />

### Insights:
- Q1 and Q2 show strong and consistent sales performance with similar revenue and order volume.
- In Q3, there is a significant decline in both total sales and number of orders.
- The average customer rating remains constant across all quarters, indicating that the drop in sales is not due to customer dissatisfaction.
- The reduction in revenue in Q3 is directly linked to the decrease in order volume rather than product quality or customer experience.
This suggests that external factors such as seasonality or reduced customer demand may have impacted sales performance in Q3.

### Which Cities Generate the Highest Sales?

To identify the strongest performing markets, I grouped the dataset by City and calculated total revenue for each city.

I then selected the top 5 cities with the highest sales and visualized them using a horizontal bar chart for easy comparison.

This analysis highlights the most valuable geographic markets for the business.

### Visualize Data

```python
top_5_cities = (
    df.groupby("City")["Price (INR)"]
      .sum()
      .nlargest(5)
      .sort_values()
      .reset_index()
)

fig = px.bar(
    top_5_cities,
    x="Price (INR)",
    y="City",
    orientation="h",
    title="Top 5 Cities by Sales (INR)",
    color_discrete_sequence=["#4C72B0"],
    text="Price (INR)"
)

fig.update_traces(
    texttemplate="₹%{text:,.0f}",
    textposition="outside"
)

fig.update_layout(
    yaxis=dict(autorange="reversed"),
    plot_bgcolor="white",
    paper_bgcolor="white",
    height=400,
    title=dict(font_size=18, x=0.5),
    font=dict(family="Arial", size=12, color="#333333"),
    margin=dict(l=100, r=50, t=80, b=50)
)

fig.show()
```

### Results
<img width="622" height="205" alt="image" src="https://github.com/user-attachments/assets/a18e74aa-e6df-4e95-a5f3-ab268cd54af9" />

### Insights:
- Bengaluru generates significantly higher revenue than every other city, making it the primary revenue driver.
- Lucknow and Hyderabad are the next strongest contributors, but still far behind Bengaluru.
- Cities like Hyderabad, Mumbai moderate performance.
- New Delhi contribute very little to overall revenue.
This indicates that revenue is highly concentrated in a few key cities, while many regions remain underperforming and may present opportunities for market expansion.

### What I Learned

Gained hands-on experience in cleaning, preparing, and validating real-world sales data using Python and Pandas.

Learned how to calculate and interpret KPIs like Total Sales, Average Order Value, Average Rating, Rating Count, and Total Orders to measure business performance.

Developed skills in data visualization using Matplotlib, Seaborn, and Plotly, allowing trends and insights to be communicated clearly.

Practiced segmenting and categorizing data, such as classifying dishes into Veg vs Non-Veg, and analyzing performance across states, cities, and time periods.

Understood how to link sales performance with customer engagement by comparing revenue trends with average ratings.

### General Insights

Revenue Trends: Monthly revenue shows strong peaks at certain times (e.g., January) with seasonal dips (e.g., February and mid-year). Daily trends indicate higher sales on weekends, particularly Saturdays.

Product Performance: Veg dishes are the main revenue driver (≈63%), highlighting a customer preference that can guide menu expansion and marketing strategies.

Regional Performance: Karnataka dominates revenue generation, followed by Telangana and Delhi, while other states contribute less. Similarly, Bengaluru is the top city for sales.

Quarterly Performance: Sales fluctuate across quarters, but customer satisfaction remains consistent, suggesting revenue dips are due to demand rather than service issues.

### Challenges Faced

Data Quality: Handling missing values and duplicates required careful cleaning to avoid skewed results.

Categorization: Classifying dishes into Veg and Non-Veg based on keywords was challenging due to inconsistent naming conventions.

Time-Series Analysis: Grouping and visualizing sales over time required transforming date columns and dealing with irregularities in data frequency.

Multi-Dimensional Insights: Comparing sales across states, cities, food categories, and time periods simultaneously required clear structuring of KPIs and visualizations to avoid confusion.

### Conclusion

This project provided a comprehensive understanding of sales analysis in Python. By combining KPIs, visualizations, and segmentation, I was able to uncover actionable insights into customer behavior, product performance, and regional sales trends. The analysis highlights opportunities for menu optimization, regional expansion, and targeted promotions, while reinforcing the importance of data cleaning and structured visualization in deriving meaningful business insights.



