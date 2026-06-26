# Python for Data Analysis - Interview Questions

## Table of Contents
1. [Pandas Fundamentals](#pandas-fundamentals)
2. [Data Manipulation](#data-manipulation)
3. [Data Cleaning](#data-cleaning)
4. [Aggregation & Grouping](#aggregation--grouping)
5. [Merging & Joining](#merging--joining)
6. [Time Series with Pandas](#time-series-with-pandas)
7. [NumPy Essentials](#numpy-essentials)
8. [Data Visualization](#data-visualization)
9. [SQL in Python](#sql-in-python)
10. [Common Interview Scenarios](#common-interview-scenarios)

---

## Pandas Fundamentals

### Q1: What is Pandas and what are its main data structures?
**Answer:**

**Pandas** is a Python library for data manipulation and analysis.

**Main Data Structures:**

**1. Series:**
- One-dimensional labeled array
- Like a column in a spreadsheet
- Index + Values

```python
import pandas as pd

s = pd.Series([1, 2, 3, 4, 5], index=['a', 'b', 'c', 'd', 'e'])
```

**2. DataFrame:**
- Two-dimensional labeled data structure
- Like a spreadsheet or SQL table
- Index (rows) + Columns

```python
df = pd.DataFrame({
    'Name': ['Alice', 'Bob', 'Charlie'],
    'Age': [25, 30, 35],
    'City': ['NYC', 'LA', 'Chicago']
})
```

**3. Index:**
- Immutable sequence used for indexing
- Can be numeric, string, datetime, multi-level

---

### Q2: How do you read and write data in Pandas?
**Answer:**

```python
import pandas as pd

# Reading data
df_csv = pd.read_csv('data.csv')
df_excel = pd.read_excel('data.xlsx', sheet_name='Sheet1')
df_json = pd.read_json('data.json')
df_sql = pd.read_sql('SELECT * FROM table', connection)
df_parquet = pd.read_parquet('data.parquet')

# Common read parameters
df = pd.read_csv('data.csv', 
                 sep=',',           # Delimiter
                 header=0,          # Row to use as column names
                 index_col=0,       # Column to use as index
                 usecols=['A', 'B'], # Columns to load
                 nrows=1000,        # Number of rows to read
                 skiprows=5,        # Rows to skip
                 na_values=['N/A', 'NULL'],  # Values to treat as NA
                 parse_dates=['date_col'],    # Parse as datetime
                 dtype={'col1': 'str', 'col2': 'int32'})  # Data types

# Writing data
df.to_csv('output.csv', index=False)
df.to_excel('output.xlsx', sheet_name='Sheet1', index=False)
df.to_json('output.json', orient='records')
df.to_sql('table_name', connection, if_exists='replace', index=False)
df.to_parquet('output.parquet', index=False)
```

---

### Q3: Explain Indexing and Selection in Pandas
**Answer:**

```python
import pandas as pd
import numpy as np

df = pd.DataFrame(np.random.randn(5, 4), 
                  columns=['A', 'B', 'C', 'D'],
                  index=['a', 'b', 'c', 'd', 'e'])

# Selection by column name
df['A']           # Series
df[['A', 'B']]    # DataFrame (multiple columns)

# Selection by label (.loc)
df.loc['a']              # Row 'a'
df.loc['a', 'B']         # Single value
df.loc['a':'c']          # Slice (inclusive)
df.loc[:, 'A':'C']       # Column slice
df.loc['a':'c', 'A':'C'] # Row and column slice

# Selection by position (.iloc)
df.iloc[0]               # First row
df.iloc[0, 1]            # Single value (row 0, col 1)
df.iloc[0:3]             # Rows 0, 1, 2 (exclusive end)
df.iloc[:, 0:3]          # Columns 0, 1, 2

# Boolean indexing
df[df['A'] > 0]                          # Rows where A > 0
df[(df['A'] > 0) & (df['B'] < 0)]        # Multiple conditions (AND)
df[(df['A'] > 0) | (df['B'] < 0)]        # Multiple conditions (OR)
df[df['A'].isin([1, 2, 3])]              # IN operator
df[df['A'].between(1, 5)]                # Between
df[df['Name'].str.contains('Alice')]       # String contains

# Query method
df.query('A > 0 and B < 0')
df.query('A > @threshold')  # Using variable

# .at and .iat (fast scalar access)
df.at['a', 'B']    # By label
df.iat[0, 1]       # By position
```

---

### Q4: What are the differences between .loc, .iloc, and .ix?
**Answer:**

| Feature | .loc | .iloc | .ix (Deprecated) |
|---------|------|-------|-----------------|
| Indexing | Label-based | Position-based | Mixed (label or position) |
| Slice End | Inclusive | Exclusive | Depends |
| Example | df.loc['a':'c'] | df.iloc[0:3] | df.ix[0:3] |
| Status | Active | Active | Deprecated since 0.20 |

**Key Points:**
- Always use `.loc` for labels and `.iloc` for positions
- `.ix` was confusing and removed
- `.loc` with integer labels can be tricky (use `.iloc` instead)

---

## Data Manipulation

### Q5: How do you handle missing data in Pandas?
**Answer:**

```python
import pandas as pd
import numpy as np

df = pd.DataFrame({
    'A': [1, 2, np.nan, 4],
    'B': [5, np.nan, np.nan, 8],
    'C': ['x', 'y', 'z', np.nan]
})

# Detect missing values
df.isnull()           # Boolean DataFrame
df.isnull().sum()     # Count per column
df.isnull().sum().sum()  # Total count
df.notnull()          # Opposite of isnull

# Drop missing values
df.dropna()                    # Drop rows with any NA
df.dropna(axis=1)              # Drop columns with any NA
df.dropna(how='all')           # Drop rows where ALL values are NA
df.dropna(thresh=2)            # Keep rows with at least 2 non-NA values
df.dropna(subset=['A', 'B'])   # Only consider specific columns

# Fill missing values
df.fillna(0)                   # Fill with 0
df.fillna({'A': 0, 'B': 99})  # Column-specific fill
df.fillna(method='ffill')      # Forward fill
df.fillna(method='bfill')      # Backward fill
df['A'].fillna(df['A'].mean()) # Fill with mean
df['A'].fillna(df['A'].median()) # Fill with median
df['A'].fillna(df['A'].mode()[0])  # Fill with mode

# Interpolation
df.interpolate()               # Linear interpolation
df.interpolate(method='polynomial', order=2)

# Replace specific values
df.replace(np.nan, 'Missing')
df.replace([np.nan, 'NULL', 'N/A'], 'Missing')
```

---

### Q6: How do you filter, sort, and rank data?
**Answer:**

```python
import pandas as pd

df = pd.DataFrame({
    'Name': ['Alice', 'Bob', 'Charlie', 'David'],
    'Age': [25, 30, 35, 28],
    'Salary': [50000, 60000, 75000, 55000],
    'Dept': ['IT', 'HR', 'IT', 'Finance']
})

# Filtering
df[df['Age'] > 28]                          # Simple condition
df[(df['Age'] > 25) & (df['Salary'] > 55000)]  # Multiple conditions
df[df['Dept'].isin(['IT', 'HR'])]           # IN operator
df[df['Name'].str.startswith('A')]          # String starts with
df[df['Name'].str.contains('li')]            # String contains
df.query('Age > 25 and Salary > 50000')      # Query method

# Sorting
df.sort_values('Age')                        # Ascending
df.sort_values('Age', ascending=False)       # Descending
df.sort_values(['Dept', 'Salary'], ascending=[True, False])  # Multiple columns

# Ranking
df['Salary_Rank'] = df['Salary'].rank()                    # Default: average
df['Salary_Rank'] = df['Salary'].rank(method='min')      # Min rank for ties
df['Salary_Rank'] = df['Salary'].rank(method='dense')    # No gaps in ranking
df['Salary_Rank'] = df['Salary'].rank(ascending=False)     # Descending rank

# Top N
df.nlargest(3, 'Salary')                     # Top 3 by salary
df.nsmallest(3, 'Age')                      # Bottom 3 by age

# Unique values
df['Dept'].unique()                          # Array of unique values
df['Dept'].nunique()                         # Count of unique values
df['Dept'].value_counts()                    # Frequency counts
df['Dept'].value_counts(normalize=True)     # Proportions
```

---

### Q7: How do you apply functions to data?
**Answer:**

```python
import pandas as pd
import numpy as np

df = pd.DataFrame({
    'A': [1, 2, 3, 4],
    'B': [10, 20, 30, 40]
})

# apply() - Apply function along axis
# Axis 0 (default): Apply to each column
# Axis 1: Apply to each row

df.apply(np.sum)                    # Sum of each column
df.apply(np.sum, axis=1)          # Sum of each row
df.apply(lambda x: x.max() - x.min())  # Range of each column
df.apply(lambda x: x**2)          # Square each element

# applymap() - Apply to each element (DataFrame only)
df.applymap(lambda x: x**2)

# map() - Apply to Series (element-wise)
df['A'].map(lambda x: x**2)
df['A'].map({1: 'One', 2: 'Two'})  # Dictionary mapping

# Vectorized operations (fastest)
df['C'] = df['A'] + df['B']        # Element-wise addition
df['D'] = df['A'] * 2               # Scalar multiplication
df['E'] = np.sqrt(df['A'])          # NumPy functions

# String operations
df['Name'] = df['Name'].str.upper()
df['Name'] = df['Name'].str.replace('A', 'X')
df['Name'] = df['Name'].str.strip()
df['First_Name'] = df['Full_Name'].str.split(' ').str[0]

# Custom function with multiple columns
def calculate_bonus(row):
    if row['Dept'] == 'Sales':
        return row['Salary'] * 0.15
    return row['Salary'] * 0.10

df['Bonus'] = df.apply(calculate_bonus, axis=1)

# Using np.where (vectorized if-else)
df['Category'] = np.where(df['Salary'] > 60000, 'High', 'Low')

# Using np.select (multiple conditions)
conditions = [
    df['Salary'] < 40000,
    df['Salary'] < 60000,
    df['Salary'] >= 60000
]
choices = ['Low', 'Medium', 'High']
df['Salary_Band'] = np.select(conditions, choices, default='Unknown')
```

---

## Data Cleaning

### Q8: How do you handle duplicates, outliers, and data type conversions?
**Answer:**

```python
import pandas as pd
import numpy as np

# Duplicates
df.duplicated()                    # Boolean Series
df.duplicated(subset=['Name', 'Age'])  # Check specific columns
df.drop_duplicates()               # Remove duplicate rows
df.drop_duplicates(subset=['Name'], keep='first')  # Keep first occurrence
df.drop_duplicates(keep=False)     # Remove all duplicates

# Data type conversions
df['Age'] = df['Age'].astype(int)
df['Salary'] = df['Salary'].astype(float)
df['Date'] = pd.to_datetime(df['Date'])
df['Category'] = df['Category'].astype('category')

# Convert to numeric (coerce errors to NaN)
df['Amount'] = pd.to_numeric(df['Amount'], errors='coerce')

# Outlier detection (IQR method)
Q1 = df['Salary'].quantile(0.25)
Q3 = df['Salary'].quantile(0.75)
IQR = Q3 - Q1
lower_bound = Q1 - 1.5 * IQR
upper_bound = Q3 + 1.5 * IQR

outliers = df[(df['Salary'] < lower_bound) | (df['Salary'] > upper_bound)]

# Remove outliers
df_clean = df[(df['Salary'] >= lower_bound) & (df['Salary'] <= upper_bound)]

# Z-score method
from scipy import stats
z_scores = np.abs(stats.zscore(df['Salary']))
df_clean = df[z_scores < 3]

# String cleaning
df['Name'] = df['Name'].str.strip()                    # Remove whitespace
df['Name'] = df['Name'].str.lower()                     # Lowercase
df['Name'] = df['Name'].str.title()                     # Title case
df['Phone'] = df['Phone'].str.replace(r'[^0-9]', '', regex=True)  # Keep only digits

# Standardize categorical values
df['Dept'] = df['Dept'].str.strip().str.title()
mapping = {'It': 'IT', 'Hr': 'HR', 'Finance': 'Finance'}
df['Dept'] = df['Dept'].replace(mapping)
```

---

## Aggregation & Grouping

### Q9: How do you group and aggregate data?
**Answer:**

```python
import pandas as pd
import numpy as np

df = pd.DataFrame({
    'Dept': ['IT', 'HR', 'IT', 'Finance', 'HR', 'IT'],
    'Name': ['Alice', 'Bob', 'Charlie', 'David', 'Eve', 'Frank'],
    'Salary': [50000, 60000, 75000, 55000, 62000, 80000],
    'Age': [25, 30, 35, 28, 32, 40]
})

# Basic groupby
df.groupby('Dept')['Salary'].mean()           # Mean salary by dept
df.groupby('Dept')['Salary'].sum()            # Total salary by dept
df.groupby('Dept')['Salary'].count()            # Count by dept
df.groupby('Dept')['Salary'].agg(['mean', 'sum', 'count', 'min', 'max', 'std'])

# Multiple aggregations
df.groupby('Dept').agg({
    'Salary': ['mean', 'sum', 'count'],
    'Age': ['mean', 'min', 'max']
})

# Named aggregations
df.groupby('Dept').agg(
    avg_salary=('Salary', 'mean'),
    total_salary=('Salary', 'sum'),
    emp_count=('Name', 'count'),
    avg_age=('Age', 'mean')
)

# Multiple groupby columns
df.groupby(['Dept', 'Age'])['Salary'].mean()

# Transform (keep original shape)
df['Dept_Avg_Salary'] = df.groupby('Dept')['Salary'].transform('mean')
df['Salary_vs_Dept_Avg'] = df['Salary'] - df['Dept_Avg_Salary']

# Filter groups
df.groupby('Dept').filter(lambda x: x['Salary'].sum() > 100000)  # Depts with total > 100K
df.groupby('Dept').filter(lambda x: len(x) > 1)                # Depts with > 1 employee

# Apply custom function
def top_2_salaries(group):
    return group.nlargest(2, 'Salary')

df.groupby('Dept').apply(top_2_salaries)

# Pivot table
pd.pivot_table(df, values='Salary', index='Dept', columns='Age', aggfunc='mean')
pd.pivot_table(df, values='Salary', index='Dept', aggfunc=['mean', 'sum', 'count'])
pd.pivot_table(df, values=['Salary', 'Age'], index='Dept', aggfunc={'Salary': 'mean', 'Age': 'max'})

# Cross-tabulation
pd.crosstab(df['Dept'], df['Age'])
pd.crosstab(df['Dept'], df['Age'], values=df['Salary'], aggfunc='mean')

# Groupby with as_index=False (returns DataFrame)
df.groupby('Dept', as_index=False)['Salary'].mean()
```

---

## Merging & Joining

### Q10: How do you merge DataFrames?
**Answer:**

```python
import pandas as pd

df1 = pd.DataFrame({
    'ID': [1, 2, 3, 4],
    'Name': ['Alice', 'Bob', 'Charlie', 'David'],
    'Dept': ['IT', 'HR', 'IT', 'Finance']
})

df2 = pd.DataFrame({
    'ID': [1, 2, 3, 5],
    'Salary': [50000, 60000, 75000, 90000],
    'Bonus': [5000, 6000, 7500, 9000]
})

# Merge (SQL JOIN equivalent)
pd.merge(df1, df2, on='ID')                    # Inner join (default)
pd.merge(df1, df2, on='ID', how='left')        # Left join
pd.merge(df1, df2, on='ID', how='right')       # Right join
pd.merge(df1, df2, on='ID', how='outer')       # Full outer join

# Merge on different column names
pd.merge(df1, df2, left_on='EmpID', right_on='ID')

# Merge on multiple columns
pd.merge(df1, df2, on=['ID', 'Dept'])

# Merge with suffixes for overlapping columns
df3 = pd.DataFrame({'ID': [1, 2], 'Name': ['Alice', 'Bob'], 'Value': [10, 20]})
df4 = pd.DataFrame({'ID': [1, 2], 'Name': ['Alice', 'Bob'], 'Value': [100, 200]})
pd.merge(df3, df4, on='ID', suffixes=('_left', '_right'))

# Join (uses index)
df1.set_index('ID').join(df2.set_index('ID'))

# Concatenate (UNION ALL)
pd.concat([df1, df2])                           # Stack vertically
pd.concat([df1, df2], axis=1)                   # Stack horizontally
pd.concat([df1, df2], ignore_index=True)        # Reset index
pd.concat([df1, df2], keys=['x', 'y'])        # Add hierarchical index

# Cross join (Cartesian product)
df1.merge(df2, how='cross')
```

---

## Time Series with Pandas

### Q11: How do you work with time series data?
**Answer:**

```python
import pandas as pd
import numpy as np

# Create date range
dates = pd.date_range(start='2024-01-01', end='2024-12-31', freq='D')
# freq options: 'D' (daily), 'W' (weekly), 'M' (monthly), 'Q' (quarterly), 'H' (hourly)

# Create time series
df = pd.DataFrame({
    'Date': pd.date_range('2024-01-01', periods=365, freq='D'),
    'Sales': np.random.randint(100, 1000, 365)
})
df.set_index('Date', inplace=True)

# Resampling
df.resample('M').sum()           # Monthly sum
df.resample('W').mean()          # Weekly average
df.resample('Q').agg(['sum', 'mean', 'std'])  # Quarterly aggregations

# Rolling windows
df['MA_7'] = df['Sales'].rolling(window=7).mean()      # 7-day moving average
df['MA_30'] = df['Sales'].rolling(window=30).mean()    # 30-day moving average
df['Rolling_Sum'] = df['Sales'].rolling(window=7).sum()
df['Rolling_Max'] = df['Sales'].rolling(window=7).max()

# Expanding (cumulative)
df['Cumulative_Sum'] = df['Sales'].expanding().sum()
df['Cumulative_Mean'] = df['Sales'].expanding().mean()

# Shifting (lag/lead)
df['Sales_Lag1'] = df['Sales'].shift(1)        # Previous day
df['Sales_Lag7'] = df['Sales'].shift(7)        # Previous week
df['Sales_Lead1'] = df['Sales'].shift(-1)     # Next day

# Percentage change
df['Daily_Change'] = df['Sales'].pct_change() * 100
df['Weekly_Change'] = df['Sales'].pct_change(periods=7) * 100

# Date extraction
df['Year'] = df.index.year
df['Month'] = df.index.month
df['Day'] = df.index.day
df['Quarter'] = df.index.quarter
df['DayOfWeek'] = df.index.dayofweek  # 0=Monday
df['WeekOfYear'] = df.index.isocalendar().week

# Time-based filtering
df['2024-01']                    # January 2024
df['2024-01':'2024-03']          # Q1 2024
df[df.index.dayofweek < 5]       # Weekdays only
df[df.index.month.isin([6, 7, 8])]  # Summer months

# Time zone handling
df.tz_localize('UTC')            # Add timezone
df.tz_convert('US/Eastern')      # Convert timezone

# Interpolation
df['Sales'] = df['Sales'].interpolate(method='linear')

# Date offsets
df.index + pd.DateOffset(days=5)     # Add 5 days
df.index + pd.DateOffset(months=1)   # Add 1 month
pd.to_datetime('2024-01-15') + pd.offsets.BDay(5)  # 5 business days
```

---

## NumPy Essentials

### Q12: What are NumPy arrays and how do they differ from Python lists?
**Answer:**

```python
import numpy as np

# Creating arrays
arr1 = np.array([1, 2, 3, 4, 5])
arr2 = np.array([[1, 2, 3], [4, 5, 6]])

# Special arrays
np.zeros((3, 4))          # 3x4 array of zeros
np.ones((3, 4))           # 3x4 array of ones
np.eye(3)                 # 3x3 identity matrix
np.arange(0, 10, 2)       # [0, 2, 4, 6, 8]
np.linspace(0, 1, 5)      # 5 evenly spaced values between 0 and 1
np.random.rand(3, 3)      # Random values between 0 and 1
np.random.randn(3, 3)     # Random values from standard normal
np.random.randint(1, 10, (3, 3))  # Random integers

# Array properties
arr = np.array([[1, 2, 3], [4, 5, 6]])
arr.shape                 # (2, 3)
arr.ndim                  # 2
arr.size                  # 6
arr.dtype                 # int64
arr.itemsize              # 8 bytes per element

# Indexing and slicing
arr[0, 1]                 # Element at row 0, col 1
arr[0, :]                 # First row
arr[:, 1]                 # Second column
arr[0:2, 1:3]             # Subarray

# Boolean indexing
arr[arr > 3]              # Elements greater than 3
arr[(arr > 2) & (arr < 5)]  # Elements between 2 and 5

# Reshaping
arr.reshape(3, 2)         # Reshape to 3x2
arr.flatten()             # Flatten to 1D
arr.T                     # Transpose

# Mathematical operations
arr + 10                  # Add scalar
arr * 2                   # Multiply by scalar
arr1 + arr2               # Element-wise addition
arr1 * arr2               # Element-wise multiplication
np.dot(arr1, arr2)        # Matrix multiplication
np.sqrt(arr)              # Square root
np.exp(arr)               # Exponential
np.log(arr)               # Natural log
np.sin(arr)               # Sine

# Aggregation
np.sum(arr)               # Sum of all elements
np.sum(arr, axis=0)       # Sum along columns
np.sum(arr, axis=1)       # Sum along rows
np.mean(arr)              # Mean
np.median(arr)            # Median
np.std(arr)               # Standard deviation
np.min(arr), np.max(arr)  # Min and max
np.argmin(arr), np.argmax(arr)  # Index of min and max

# Broadcasting
arr = np.array([[1, 2, 3], [4, 5, 6]])
row = np.array([10, 20, 30])
arr + row                 # Adds row to each row of arr

# Stacking
np.vstack((arr1, arr2))   # Vertical stack
np.hstack((arr1, arr2))   # Horizontal stack
np.concatenate((arr1, arr2), axis=0)

# Saving and loading
np.save('array.npy', arr)
loaded = np.load('array.npy')
```

**NumPy vs Python Lists:**

| Feature | NumPy Array | Python List |
|---------|-------------|-------------|
| Data Type | Homogeneous (same type) | Heterogeneous |
| Performance | Fast (vectorized C operations) | Slow (Python loops) |
| Memory | Compact, contiguous | Scattered pointers |
| Operations | Element-wise by default | Requires loops |
| Size | Fixed at creation | Dynamic |
| Functionality | Mathematical, linear algebra | General purpose |

---

## Data Visualization

### Q13: How do you create visualizations in Python?
**Answer:**

```python
import matplotlib.pyplot as plt
import seaborn as sns
import pandas as pd
import numpy as np

# Matplotlib basics
x = np.linspace(0, 10, 100)
y = np.sin(x)

plt.figure(figsize=(10, 6))
plt.plot(x, y, label='sin(x)', color='blue', linewidth=2)
plt.plot(x, np.cos(x), label='cos(x)', color='red', linestyle='--')
plt.title('Trigonometric Functions')
plt.xlabel('x')
plt.ylabel('y')
plt.legend()
plt.grid(True, alpha=0.3)
plt.show()

# Subplots
fig, axes = plt.subplots(2, 2, figsize=(12, 10))
axes[0, 0].plot(x, np.sin(x))
axes[0, 0].set_title('Sine')
axes[0, 1].plot(x, np.cos(x))
axes[0, 1].set_title('Cosine')
axes[1, 0].plot(x, np.tan(x))
axes[1, 0].set_title('Tangent')
axes[1, 1].plot(x, np.exp(x))
axes[1, 1].set_title('Exponential')
plt.tight_layout()
plt.show()

# Bar chart
categories = ['A', 'B', 'C', 'D']
values = [10, 20, 15, 25]
plt.bar(categories, values, color=['red', 'green', 'blue', 'orange'])
plt.title('Bar Chart')
plt.show()

# Horizontal bar chart
plt.barh(categories, values)
plt.title('Horizontal Bar Chart')
plt.show()

# Histogram
plt.hist(np.random.randn(1000), bins=30, edgecolor='black', alpha=0.7)
plt.title('Histogram')
plt.show()

# Scatter plot
x = np.random.randn(100)
y = np.random.randn(100)
plt.scatter(x, y, alpha=0.5, c=np.random.rand(100), cmap='viridis')
plt.title('Scatter Plot')
plt.colorbar()
plt.show()

# Pie chart
plt.pie(values, labels=categories, autopct='%1.1f%%', startangle=90)
plt.title('Pie Chart')
plt.show()

# Seaborn (statistical visualization)
df = pd.DataFrame({
    'x': np.random.randn(100),
    'y': np.random.randn(100),
    'category': np.random.choice(['A', 'B', 'C'], 100)
})

# Distribution plots
sns.histplot(df['x'], kde=True)
sns.boxplot(data=df, x='category', y='x')
sns.violinplot(data=df, x='category', y='x')

# Relationship plots
sns.scatterplot(data=df, x='x', y='y', hue='category')
sns.lineplot(data=df, x='x', y='y')
sns.regplot(data=df, x='x', y='y')  # With regression line

# Categorical plots
sns.barplot(data=df, x='category', y='x')
sns.countplot(data=df, x='category')

# Heatmap
corr = df.corr()
sns.heatmap(corr, annot=True, cmap='coolwarm', center=0)

# Pair plot
sns.pairplot(df, hue='category')

# Styling
sns.set_style('whitegrid')
sns.set_palette('husl')
```

---

## SQL in Python

### Q14: How do you work with SQL databases in Python?
**Answer:**

```python
import pandas as pd
import sqlite3
from sqlalchemy import create_engine

# SQLite (file-based)
conn = sqlite3.connect('database.db')

# PostgreSQL
# conn = create_engine('postgresql://user:password@localhost:5432/dbname')

# MySQL
# conn = create_engine('mysql+pymysql://user:password@localhost:3306/dbname')

# Read SQL into DataFrame
df = pd.read_sql('SELECT * FROM employees', conn)
df = pd.read_sql('SELECT * FROM employees WHERE salary > 50000', conn)

# Read with parameters (prevents SQL injection)
df = pd.read_sql('SELECT * FROM employees WHERE dept = ?', conn, params=('IT',))

# Write DataFrame to SQL
df.to_sql('new_table', conn, if_exists='replace', index=False)
# if_exists options: 'fail', 'replace', 'append'

# Using SQLAlchemy for more control
from sqlalchemy import create_engine, text

engine = create_engine('sqlite:///database.db')

with engine.connect() as connection:
    result = connection.execute(text('SELECT * FROM employees'))
    for row in result:
        print(row)

# Complex query with pandas
df = pd.read_sql('''
    SELECT 
        d.department_name,
        COUNT(e.employee_id) as emp_count,
        AVG(e.salary) as avg_salary
    FROM employees e
    JOIN departments d ON e.dept_id = d.dept_id
    WHERE e.hire_date >= '2023-01-01'
    GROUP BY d.department_name
    HAVING COUNT(e.employee_id) > 5
    ORDER BY avg_salary DESC
''', conn)

# Close connection
conn.close()
```

---

## Common Interview Scenarios

### Q15: How would you analyze a dataset from start to finish?
**Answer:**

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns

# Step 1: Load and inspect data
df = pd.read_csv('data.csv')
print(df.head())
print(df.info())
print(df.describe())
print(df.shape)

# Step 2: Data cleaning
# Handle missing values
df.isnull().sum()
df = df.dropna(subset=['critical_column'])  # Drop rows with missing critical data
df['column'] = df['column'].fillna(df['column'].median())  # Impute

# Remove duplicates
df = df.drop_duplicates()

# Fix data types
df['date'] = pd.to_datetime(df['date'])
df['category'] = df['category'].astype('category')

# Handle outliers
Q1 = df['numeric_col'].quantile(0.25)
Q3 = df['numeric_col'].quantile(0.75)
IQR = Q3 - Q1
df = df[(df['numeric_col'] >= Q1 - 1.5*IQR) & (df['numeric_col'] <= Q3 + 1.5*IQR)]

# Step 3: Exploratory Data Analysis (EDA)
# Univariate analysis
df['numeric_col'].hist()
df['category'].value_counts().plot(kind='bar')

# Bivariate analysis
sns.scatterplot(data=df, x='col1', y='col2')
sns.boxplot(data=df, x='category', y='numeric_col')

# Correlation analysis
corr = df.corr(numeric_only=True)
sns.heatmap(corr, annot=True)

# Step 4: Feature engineering
df['year'] = df['date'].dt.year
df['month'] = df['date'].dt.month
df['day_of_week'] = df['date'].dt.dayofweek

# Create derived features
df['profit_margin'] = df['profit'] / df['revenue']
df['is_weekend'] = df['day_of_week'].isin([5, 6])

# Step 5: Aggregation and summarization
summary = df.groupby('category').agg({
    'revenue': ['sum', 'mean', 'count'],
    'profit': ['sum', 'mean'],
    'profit_margin': 'mean'
}).round(2)

# Step 6: Visualization and reporting
plt.figure(figsize=(12, 8))
sns.barplot(data=df, x='category', y='revenue')
plt.title('Revenue by Category')
plt.xticks(rotation=45)
plt.tight_layout()
plt.savefig('revenue_by_category.png')

# Step 7: Export results
summary.to_csv('summary_report.csv')
df.to_csv('cleaned_data.csv', index=False)
```

---

### Q16: How do you handle large datasets in Pandas?
**Answer:**

```python
import pandas as pd

# 1. Read in chunks
chunk_size = 100000
chunks = []
for chunk in pd.read_csv('large_file.csv', chunksize=chunk_size):
    # Process each chunk
    processed = chunk[chunk['column'] > 0]  # Filter
    chunks.append(processed)

df = pd.concat(chunks)

# 2. Use categorical data types
df['category'] = df['category'].astype('category')  # Reduces memory

# 3. Specify data types on read
df = pd.read_csv('large_file.csv', 
                 dtype={'id': 'int32', 'amount': 'float32'},
                 usecols=['id', 'amount', 'date'])  # Only needed columns

# 4. Use efficient data types
df['int_col'] = df['int_col'].astype('int32')       # Instead of int64
df['float_col'] = df['float_col'].astype('float32')  # Instead of float64

# 5. Query optimization with eval and query
df.query('column > 100 and other < 50')  # Faster for large DataFrames

# 6. Use Dask for out-of-core computation
import dask.dataframe as dd
ddf = dd.read_csv('large_file.csv')
result = ddf.groupby('category').amount.sum().compute()

# 7. Use PyArrow backend (Pandas 2.0+)
df = pd.read_csv('file.csv', dtype_backend='pyarrow')

# 8. Delete intermediate variables
import gc
del large_df
gc.collect()
```

---

### Q17: How do you pivot and reshape data?
**Answer:**

```python
import pandas as pd

# Sample data
df = pd.DataFrame({
    'Date': ['2024-01', '2024-01', '2024-02', '2024-02'],
    'Product': ['A', 'B', 'A', 'B'],
    'Sales': [100, 200, 150, 250]
})

# Pivot (wide format)
pivot = df.pivot(index='Date', columns='Product', values='Sales')
# Output:
# Product    A    B
# Date
# 2024-01  100  200
# 2024-02  150  250

# Pivot table (with aggregation)
df_pivot = pd.pivot_table(df, values='Sales', index='Date', 
                          columns='Product', aggfunc='sum')

# Melt (long format)
df_long = pivot.reset_index().melt(id_vars='Date', var_name='Product', value_name='Sales')

# Stack / Unstack
stacked = pivot.stack()      # Column index becomes row index
unstacked = stacked.unstack()  # Row index becomes column index

# Wide to long
df_wide = pd.DataFrame({
    'ID': [1, 2],
    'Sales_Jan': [100, 200],
    'Sales_Feb': [150, 250],
    'Sales_Mar': [200, 300]
})
df_long = pd.wide_to_long(df_wide, stubnames='Sales', i='ID', j='Month', sep='_', suffix='\w+')

# Cross-tabulation
pd.crosstab(df['Date'], df['Product'], values=df['Sales'], aggfunc='sum')
```

---

### Q18: How do you work with categorical data?
**Answer:**

```python
import pandas as pd

df = pd.DataFrame({
    'Category': ['Low', 'Medium', 'High', 'Medium', 'Low'],
    'Size': ['Small', 'Large', 'Medium', 'Small', 'Large']
})

# Convert to categorical
df['Category'] = pd.Categorical(df['Category'], 
                                  categories=['Low', 'Medium', 'High'],
                                  ordered=True)

# Benefits of categorical:
# - Memory efficient
# - Ordered comparisons work
df['Category'] > 'Low'  # Returns True for Medium and High

# Encoding for machine learning
# One-hot encoding
pd.get_dummies(df['Category'], prefix='Cat')

# Label encoding
df['Category_Code'] = df['Category'].cat.codes

# Value counts with percentages
df['Category'].value_counts(normalize=True)

# Groupby with ordered categories preserves order
df.groupby('Category').size()
```

---

### Q19: How do you handle dates and times efficiently?
**Answer:**

```python
import pandas as pd

# Parsing dates
df['date'] = pd.to_datetime(df['date_string'])
df['date'] = pd.to_datetime(df['date_string'], format='%Y-%m-%d')

# Extract components
df['year'] = df['date'].dt.year
df['month'] = df['date'].dt.month
df['day'] = df['date'].dt.day
df['quarter'] = df['date'].dt.quarter
df['dayofweek'] = df['date'].dt.dayofweek  # 0=Monday
df['is_weekend'] = df['date'].dt.dayofweek.isin([5, 6])
df['weekofyear'] = df['date'].dt.isocalendar().week

# Date arithmetic
df['date_plus_7'] = df['date'] + pd.Timedelta(days=7)
df['date_plus_month'] = df['date'] + pd.DateOffset(months=1)
df['days_since'] = (pd.Timestamp.now() - df['date']).dt.days

# Period operations
df['month_period'] = df['date'].dt.to_period('M')
df['quarter_period'] = df['date'].dt.to_period('Q')

# Resampling time series
df.set_index('date', inplace=True)
df.resample('M').sum()       # Monthly sum
df.resample('W').mean()      # Weekly average
df.resample('Q').agg(['sum', 'mean', 'count'])

# Time zones
df['date_utc'] = df['date'].dt.tz_localize('UTC')
df['date_est'] = df['date_utc'].dt.tz_convert('US/Eastern')

# Business days
from pandas.tseries.offsets import BDay
df['next_business_day'] = df['date'] + BDay(1)

# Date ranges
pd.date_range('2024-01-01', '2024-12-31', freq='MS')  # Month start
pd.date_range('2024-01-01', periods=12, freq='M')      # 12 month ends
pd.bdate_range('2024-01-01', '2024-01-31')            # Business days only
```

---

### Q20: How do you create a reproducible data analysis pipeline?
**Answer:**

```python
import pandas as pd
import numpy as np
from pathlib import Path

class DataPipeline:
    def __init__(self, raw_path, processed_path):
        self.raw_path = Path(raw_path)
        self.processed_path = Path(processed_path)
        self.data = None

    def load_data(self):
        self.data = pd.read_csv(self.raw_path)
        print(f"Loaded {len(self.data)} rows")
        return self

    def clean_data(self):
        # Remove duplicates
        self.data = self.data.drop_duplicates()
        # Handle missing values
        self.data = self.data.dropna(subset=['critical_column'])
        # Fix data types
        self.data['date'] = pd.to_datetime(self.data['date'])
        self.data['amount'] = pd.to_numeric(self.data['amount'], errors='coerce')
        # Remove outliers
        Q1 = self.data['amount'].quantile(0.25)
        Q3 = self.data['amount'].quantile(0.75)
        IQR = Q3 - Q1
        self.data = self.data[
            (self.data['amount'] >= Q1 - 1.5*IQR) & 
            (self.data['amount'] <= Q3 + 1.5*IQR)
        ]
        print(f"After cleaning: {len(self.data)} rows")
        return self

    def engineer_features(self):
        self.data['year'] = self.data['date'].dt.year
        self.data['month'] = self.data['date'].dt.month
        self.data['day_of_week'] = self.data['date'].dt.dayofweek
        self.data['is_weekend'] = self.data['day_of_week'].isin([5, 6])
        self.data['amount_log'] = np.log1p(self.data['amount'])
        return self

    def save_processed(self):
        self.processed_path.parent.mkdir(parents=True, exist_ok=True)
        self.data.to_csv(self.processed_path, index=False)
        print(f"Saved to {self.processed_path}")
        return self

    def run(self):
        return (self
                .load_data()
                .clean_data()
                .engineer_features()
                .save_processed())

# Usage
pipeline = DataPipeline('data/raw.csv', 'data/processed.csv')
pipeline.run()
```
