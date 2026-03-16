# Day 1 — Data Analyst Foundations
### Block 1 | Libraries: `pandas` · `seaborn` · `plotly` · `openpyxl`

---

## What You Will Learn Today

By the end of Day 1 you will be able to:
- Load any dataset from a CSV or Excel file
- Understand and explore data using Pandas
- Clean messy data (missing values, wrong types, duplicates)
- Analyze data using groupby, aggregations and filters
- Visualize data using Seaborn and Plotly
- Know when to use which chart type

---

## 1. What is Data Analysis?

Data analysis is the process of inspecting, cleaning, transforming and summarizing data to discover useful information and support decision-making.

Think of it like this:
> You have 10,000 rows of sales data from a company. Your job is to answer questions like — which product sells the most? Which city has the lowest revenue? Which month had the highest returns? You cannot answer these by looking at raw rows. You need to clean, group, filter and visualize.

That is exactly what a Data Analyst does every day.

---

## 2. Pandas — The Core Tool

Pandas is a Python library for working with structured data (tables, spreadsheets, CSVs).

The two main data structures in Pandas:

### Series
A single column of data. Like one column in Excel.
```python
import pandas as pd

marks = pd.Series([85, 90, 78, 92, 88])
print(marks)
# 0    85
# 1    90
# 2    78
# 3    92
# 4    88
```

### DataFrame
A full table with rows and columns. Like an entire Excel sheet.
```python
data = {
    "name":   ["Alice", "Bob", "Charlie", "Diana"],
    "city":   ["Mumbai", "Delhi", "Mumbai", "Pune"],
    "sales":  [15000, 22000, 18000, 30000],
    "age":    [25, 30, 28, 35]
}

df = pd.DataFrame(data)
print(df)
```
Output:
```
      name    city  sales  age
0    Alice  Mumbai  15000   25
1      Bob   Delhi  22000   30
2  Charlie  Mumbai  18000   28
3    Diana    Pune  30000   35
```

---

## 3. Loading Data

### From CSV
```python
df = pd.read_csv("data.csv")
```

### From Excel
```python
df = pd.read_excel("data.xlsx", sheet_name="Sheet1")
```

### From URL (Kaggle datasets, GitHub raw links)
```python
url = "https://raw.githubusercontent.com/datasciencedojo/datasets/master/titanic.csv"
df = pd.read_csv(url)
```

---

## 4. Exploring Your Data — Always Do This First

Before doing anything with data, you must understand what you are dealing with.

```python
df.shape          # (rows, columns) — how big is the dataset?
df.head()         # first 5 rows — what does data look like?
df.tail()         # last 5 rows
df.info()         # column names, data types, non-null counts
df.describe()     # statistics: mean, min, max, std for numbers
df.columns        # list of column names
df.dtypes         # data type of each column
df.isnull().sum() # count of missing values per column
df.duplicated().sum() # count of duplicate rows
df['city'].value_counts() # frequency of each unique value in a column
df['sales'].unique()      # all unique values in a column
```

### Why does this matter?
If you skip exploration you will build analysis on wrong assumptions. For example — a column named `age` might have string values like "25 years" instead of integers. You will not know until you check `df.dtypes`.

---

## 5. Selecting Data

### Select a single column
```python
df['sales']         # returns a Series
df[['name','sales']] # returns a DataFrame with 2 columns
```

### Select rows by condition (filtering)
```python
df[df['city'] == 'Mumbai']           # rows where city is Mumbai
df[df['sales'] > 20000]              # rows where sales > 20000
df[(df['city'] == 'Mumbai') & (df['sales'] > 15000)]  # AND condition
df[(df['city'] == 'Delhi') | (df['age'] < 30)]        # OR condition
```

### Select by position — iloc
```python
df.iloc[0]        # first row
df.iloc[0:3]      # first 3 rows
df.iloc[:, 0:2]   # all rows, first 2 columns
```

### Select by label — loc
```python
df.loc[0, 'name']          # row 0, column 'name'
df.loc[0:2, ['name','city']] # rows 0-2, selected columns
```

### The difference between iloc and loc
- `iloc` uses integer positions (like list indexing — 0,1,2)
- `loc` uses labels (column names, index labels)

---

## 6. Cleaning Data

Real-world data is always messy. Cleaning is 60% of a data analyst's job.

### Handling Missing Values
```python
df.isnull().sum()              # find missing values

df.dropna()                    # remove ALL rows with any missing value
df.dropna(subset=['sales'])    # remove rows where 'sales' is missing only

df['age'].fillna(df['age'].mean())        # fill missing with mean
df['city'].fillna('Unknown')              # fill missing with a fixed value
df.fillna(method='ffill')                 # fill with previous row's value
```

### Removing Duplicates
```python
df.duplicated().sum()    # how many duplicates?
df.drop_duplicates()     # remove duplicate rows
df.drop_duplicates(subset=['name'])  # remove where 'name' is duplicate
```

### Fixing Data Types
```python
df['age'] = df['age'].astype(int)         # convert to integer
df['sales'] = df['sales'].astype(float)   # convert to float
df['date'] = pd.to_datetime(df['date'])   # convert string to datetime
```

### Renaming Columns
```python
df.rename(columns={'name': 'employee_name', 'city': 'location'}, inplace=True)
```

### Dropping Columns
```python
df.drop(columns=['unnecessary_column'], inplace=True)
```

### Stripping Whitespace from Strings
```python
df['city'] = df['city'].str.strip()       # remove leading/trailing spaces
df['city'] = df['city'].str.lower()       # convert to lowercase
df['city'] = df['city'].str.upper()       # convert to uppercase
```

---

## 7. Transforming Data

### Adding a New Column
```python
df['sales_tax'] = df['sales'] * 0.18          # calculate new column
df['full_label'] = df['name'] + " - " + df['city']  # combine columns
```

### Apply — Run a Function on Every Row
```python
def categorize(sales):
    if sales > 25000:
        return 'High'
    elif sales > 15000:
        return 'Medium'
    else:
        return 'Low'

df['category'] = df['sales'].apply(categorize)

# Same thing with lambda (one-liner)
df['category'] = df['sales'].apply(lambda x: 'High' if x > 25000 else 'Medium' if x > 15000 else 'Low')
```

### Sorting
```python
df.sort_values('sales', ascending=False)           # sort by sales, highest first
df.sort_values(['city','sales'], ascending=[True, False])  # sort by multiple columns
```

---

## 8. Groupby — The Most Powerful Pandas Tool

Groupby splits your data into groups, applies a function, and combines results. Think of it as pivot tables in Excel.

```python
# Total sales per city
df.groupby('city')['sales'].sum()

# Average sales per city
df.groupby('city')['sales'].mean()

# Multiple aggregations at once
df.groupby('city')['sales'].agg(['sum','mean','count','max','min'])

# Group by multiple columns
df.groupby(['city','category'])['sales'].sum()

# Reset index to get a clean DataFrame back
result = df.groupby('city')['sales'].sum().reset_index()
```

### The logic behind groupby:
```
Original data:
  Alice   Mumbai  15000
  Bob     Delhi   22000
  Charlie Mumbai  18000
  Diana   Pune    30000

groupby('city')['sales'].sum()
→ Groups:
   Mumbai → [15000, 18000] → sum = 33000
   Delhi  → [22000]        → sum = 22000
   Pune   → [30000]        → sum = 30000
```

---

## 9. Merging and Joining DataFrames

Like SQL JOIN — combine two tables on a common column.

```python
orders = pd.DataFrame({
    'order_id': [1, 2, 3],
    'customer_id': [101, 102, 101],
    'amount': [500, 300, 700]
})

customers = pd.DataFrame({
    'customer_id': [101, 102, 103],
    'name': ['Alice', 'Bob', 'Charlie']
})

# Inner join — only matching rows
pd.merge(orders, customers, on='customer_id', how='inner')

# Left join — all orders, matching customer info
pd.merge(orders, customers, on='customer_id', how='left')
```

---

## 10. Working with Excel — openpyxl

```python
import pandas as pd

# Read Excel
df = pd.read_excel("sales.xlsx", sheet_name="Q1")

# Write to Excel
df.to_excel("output.xlsx", index=False)

# Write multiple sheets
with pd.ExcelWriter("report.xlsx") as writer:
    df_q1.to_excel(writer, sheet_name="Q1", index=False)
    df_q2.to_excel(writer, sheet_name="Q2", index=False)
```

---

## 11. Visualization — When to Use Which Chart

| Chart Type | Use When | Library |
|------------|----------|---------|
| Bar Chart | Comparing categories | Seaborn / Plotly |
| Line Chart | Showing trends over time | Seaborn / Plotly |
| Scatter Plot | Relationship between 2 numbers | Seaborn / Plotly |
| Histogram | Distribution of a single number | Seaborn |
| Box Plot | Spread + outliers of a number | Seaborn |
| Heatmap | Correlation between all columns | Seaborn |
| Pie Chart | Part of a whole (use sparingly) | Plotly |

---

## 12. Seaborn — Static Beautiful Charts

Seaborn is built on top of Matplotlib. Best for quick, clean, publication-quality static charts.

```python
import seaborn as sns
import matplotlib.pyplot as plt

# Bar chart
sns.barplot(data=df, x='city', y='sales', palette='Blues_d')
plt.title("Sales by City")
plt.show()

# Line chart
sns.lineplot(data=df, x='month', y='revenue')
plt.show()

# Scatter plot
sns.scatterplot(data=df, x='age', y='sales', hue='city')
plt.show()

# Histogram
sns.histplot(data=df, x='sales', bins=20, kde=True)
plt.show()

# Box plot
sns.boxplot(data=df, x='city', y='sales', palette='Set2')
plt.show()

# Heatmap (correlation matrix)
sns.heatmap(df.corr(), annot=True, cmap='coolwarm', fmt='.2f')
plt.title("Correlation Heatmap")
plt.show()
```

### What is a correlation heatmap?
It shows how strongly two columns are related.
- Value close to 1 → strong positive relationship (both go up together)
- Value close to -1 → strong negative relationship (one goes up, other goes down)
- Value close to 0 → no relationship

---

## 13. Plotly — Interactive Charts

Plotly charts are interactive — you can hover, zoom, filter. Best for dashboards and sharing with non-technical people.

```python
import plotly.express as px

# Bar chart
fig = px.bar(df, x='city', y='sales', title='Sales by City', color='city')
fig.show()

# Line chart
fig = px.line(df, x='month', y='revenue', title='Monthly Revenue')
fig.show()

# Scatter plot
fig = px.scatter(df, x='age', y='sales', color='city', size='sales',
                 hover_data=['name'], title='Age vs Sales')
fig.show()

# Pie chart
fig = px.pie(df, values='sales', names='city', title='Sales Distribution')
fig.show()

# Histogram
fig = px.histogram(df, x='sales', nbins=20, title='Sales Distribution')
fig.show()

# Box plot
fig = px.box(df, x='city', y='sales', title='Sales Spread by City')
fig.show()
```

### Seaborn vs Plotly — which to use when?
- Use **Seaborn** when you want a quick static chart for analysis
- Use **Plotly** when you want interactive charts for sharing or dashboards

---

## 14. Full Analysis Workflow (The Standard Process)

This is the exact workflow every data analyst follows:

```
1. Load data         → pd.read_csv() or pd.read_excel()
2. Explore           → shape, info, describe, isnull, value_counts
3. Clean             → handle nulls, fix types, remove duplicates
4. Transform         → add columns, apply functions, sort
5. Analyze           → groupby, aggregations, filters
6. Visualize         → seaborn for quick view, plotly for dashboard
7. Export            → df.to_excel() or df.to_csv()
```

Never skip step 2 and 3. Always explore before you analyze.

---

## 15. Key Syntax Cheatsheet

```python
# Loading
pd.read_csv("file.csv")
pd.read_excel("file.xlsx")

# Exploring
df.shape · df.head() · df.info() · df.describe()
df.isnull().sum() · df.duplicated().sum()
df['col'].value_counts()

# Selecting
df['col'] · df[['col1','col2']]
df[df['col'] > value]
df.loc[row, col] · df.iloc[row, col]

# Cleaning
df.dropna() · df.fillna(value)
df.drop_duplicates()
df['col'].astype(type)
df.rename(columns={})

# Transforming
df['new'] = df['col'] * 2
df['new'] = df['col'].apply(func)
df.sort_values('col', ascending=False)

# Groupby
df.groupby('col')['val'].sum()
df.groupby('col')['val'].agg(['sum','mean','count'])

# Merge
pd.merge(df1, df2, on='col', how='inner/left/right/outer')

# Export
df.to_csv("out.csv", index=False)
df.to_excel("out.xlsx", index=False)
```

---

## Resources
- Pandas Docs: https://pandas.pydata.org/docs/
- Plotly Express: https://plotly.com/python/plotly-express/
- Seaborn Gallery: https://seaborn.pydata.org/examples/index.html
- Free Dataset: https://www.kaggle.com/datasets/datasciencedojo/titanic