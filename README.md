# Product Data Sales Analysis
In today's competitive market, understanding product performance is essential for business growth. By analyzing and visualizing the product dataset, we can uncover valuable information about sales and profit trends, customer preferences, and category performance.

The dashboard of product data sales analysis can be viewed [here](https://lookerstudio.google.com/reporting/95d8488f-d114-4999-b001-783118d50784)

**Disclaimer**: this product data sales analysis project is still at a **basic level** and may contain errors or inaccuracies. So, further validation are recommended.

# Dataset
**Product Data Sales**

This is a fictional dataset uploaded by Anu Chhetry in Kaggle dataset, showcasing product dataset in 2013 and 2014 from a fictional store. This dataset can be downloaded [here](https://www.kaggle.com/datasets/anuchhetry/product-sales?select=Sales_v1.xlsx).

Variables in this dataset:
- Segment: customer or market category.
- Country: country where the transaction took place.
- Product: type of product sold.
- Discount Band: category of discount level given (None, Low, Medium, and High).
- Units Sold: number of product units sold.
- Manufacturing Price: production cost per unit of the product in USD.
- Sale Price: selling price per unit of the product in USD.
- Gross Sales: total revenue before discounts in USD.
- Discounts: total discount given in USD.
- Sales: total revenue after discount in USD.
- COGS: cost of goods sold i  USD.
- Profit: net profit from sales in USD.
- Date: transaction date (dd/mm/yyyy, e.g., 01/09/2014).
- Month Number: month number of the transaction (1–12).
- Month Name: name of the transaction month (e.g., September).
- Year: year of the transaction (e.g., 2013).

# Setup

For this dataset, I used these following libraries:

*   [`pandas`](https://pandas.pydata.org/?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMML0187ENSkillsNetwork31430127-2021-01-01) for managing the data.
*   [`numpy`](https://numpy.org/?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMML0187ENSkillsNetwork31430127-2021-01-01) for mathematical operations.
*   [`seaborn`](https://seaborn.pydata.org/?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMML0187ENSkillsNetwork31430127-2021-01-01) for visualizing the data.
*   [`matplotlib`](https://matplotlib.org/?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMML0187ENSkillsNetwork31430127-2021-01-01) for additional plotting tools.

## Importing libraries
```python
import pandas as pd
import matplotlib.pyplot as plt
import numpy as np
import seaborn as sns
%matplotlib inline
```
# Data Loading
```python
df = pd.read_excel('Sales_v1.xlsx')
```
```python
df.head()
```

# Data Wrangling
Identify the columns that have Null or NaN information using df.info().
```python
df.info()
```
There are 53 null values in the `Discount Band` column. However, these are not truly null, they represent "None," indicating no discount. Therefore, these null values should be replaced with No Discount.
```python
df['Discount Band'] = df['Discount Band'].fillna('No Discount')
```
Check the dataset information again.
```python
df.info()
```
Now, all 700 data in `Discount Band` are not null.

The header of the `Sales` column has extra leading spaces. To prevent this from causing issues, we need to strip the spaces.
```python
df.columns = df.columns.str.strip()
```
Check the dataset once again.

The extra space in the header of the Sales column has been removed.

Ensure the `Date` column is in datetime format.
```python
df['Date'] = pd.to_datetime(df['Date'])
```
# Data Exploratory
## Sales and Profit Trends
### Monthly sales trend
```python
# Create a Year-Month column from the original Date column
df['Year-Month'] = df['Date'].dt.to_period('M')
# Aggregate sales per month
monthly_trend = df.groupby('Year-Month')['Sales'].sum().reset_index()
# Convert Year-Month period to datetime
monthly_trend['Date'] = monthly_trend['Year-Month'].dt.to_timestamp()

# Plot monthly sales trend
sns.lineplot(data=monthly_trend, x='Date', y='Sales', marker='o', color='green')
# Set title and axis labels
plt.title('Monthly Sales Trend')
plt.xlabel('Date')
plt.ylabel('Sales')
plt.tight_layout()
plt.show()
```
Insight: the monthly sales trend shows strong fluctuations, with the lowest point in September 2013 and the highest peak in October 2014. Overall, the sales pattern indicates potential seasonality effects.
### Monthly profit trend
```python
# Aggregate profit per month
monthly_trend = df.groupby('Year-Month')['Profit'].sum().reset_index()
# Convert Year-Month period to datetime
monthly_trend['Date'] = monthly_trend['Year-Month'].dt.to_timestamp()

# Plot monthly profit trend
sns.lineplot(data=monthly_trend, x='Date', y='Profit', marker='o', color='blue')
# Set title and axis labels
plt.title('Monthly Profit Trend')
plt.xlabel('Date')
plt.ylabel('Sales')
plt.tight_layout()
plt.show()
```
Insight: the monthly profit trend shows strong fluctuations, with the lowest point in November 2014 and the highest peak in December 2014. This indicates unstable profitability that may be influenced by seasonal or external factors.

### Total sales and profit per segment by year
```python
# Aggregate sales per segment by year
segment_sales = df.groupby(['Segment', 'Year'])['Sales'].sum().reset_index()

# Plot
sns.barplot(data=segment_sales, x='Segment', y='Sales', hue='Year')
plt.title('Total Sales per Segment by Year')
plt.xlabel('Segment')
plt.ylabel('Sales')
plt.legend(title='Year')
plt.tight_layout()
plt.show()
```
Insight: the government segment had the highest total sales in both 2013 and 2014. The total sales of every segment in 2013 were much lower than in 2014, which is reasonable since the 2013 data only covers four months.

```python
# Aggregate profit per segment by year
segment_profit = df.groupby(['Segment', 'Year'])['Profit'].sum().reset_index()

# Plot
sns.barplot(data=segment_profit, x='Segment', y='Profit', hue='Year')
plt.title('Total Profit per Segment by Year')
plt.xlabel('Profit')
plt.ylabel('Sales')
plt.legend(title='Year')
plt.tight_layout()
plt.show()
```
Insight: the government segment had the highest total profit in both 2013 and 2014. Meanwhile, the enterprise segment had negative profit in both 2013 and 2014.

## Product Performance Analysis
### Total sales per product by year
```python
# Aggregate sales per product by Year
product_sales = df.groupby(['Product', 'Year'])['Sales'].sum().reset_index()

# Plot
sns.barplot(data=product_sales, x='Product', y='Sales', hue='Year')
plt.title('Total Sales per Product by Year')
plt.xlabel('Product')
plt.ylabel('Sales')
plt.legend(title='Year')
plt.tight_layout()
plt.show()
```
Insight: paseo had the highest total sales in both 2013 and 2014, while carretera had the lowest in 2013 and Montana in 2014.

### Total profit per product by year
```python
# Aggregate profit per product by Year
product_profit = df.groupby(['Product', 'Year'])['Profit'].sum().reset_index()

# Plot
sns.barplot(data=product_profit, x='Product', y='Profit', hue='Year')
plt.title('Total Profit per Product by Year')
plt.xlabel('Product')
plt.ylabel('Profit')
plt.legend(title='Year')
plt.tight_layout()
plt.show()
```
Insight: paseo has the highest total profit in both 2013 and 2014, while carretera has the lowest in 2013 and Montana in 2014.

### Total units sold per product by year
```python
# Aggregate units sold per product by Year
product_units = df.groupby(['Product', 'Year'])['Units Sold'].sum().reset_index()

# Plot
sns.barplot(data=product_units, x='Product', y='Units Sold', hue='Year')
plt.title('Total Units Sold per Product by Year')
plt.xlabel('Product')
plt.ylabel('Units Sold')
plt.legend(title='Year')
plt.tight_layout()
plt.show()
```
Insight: paseo had the highest units sold in both 2013 and 2014, while carretera had the lowest in both 2013 and 2014.

We can also see product performance by other features.

### Product performance by relevant features
```python
# List of numeric features to plot against Product
features = ['Sales', 'Units Sold', 'Manufacturing Price', 'Sale Price', 
            'Gross Sales', 'COGS', 'Profit', 'Discounts']

# Set up subplots
n_features = len(features)
n_cols = 2
n_rows = (n_features + 1) // n_cols

fig, axes = plt.subplots(n_rows, n_cols, figsize=(12, n_rows*4))
axes = axes.flatten()

# Plot each feature as barplot
for i, feature in enumerate(features):
    sns.barplot(data=df, x='Product', y=feature, hue='Year', ax=axes[i], errorbar=None)
    axes[i].set_title(f'{feature} per Product')
    axes[i].set_xlabel('Product')
    axes[i].set_ylabel(feature)
    axes[i].tick_params(axis='x', rotation=0)

# Remove empty subplots if any
for j in range(i+1, len(axes)):
    fig.delaxes(axes[j])

plt.tight_layout()
plt.show()
```

## Geographic Performance Analysis
### Total sales per country by year
```python
# Aggregate sales per country by Year
country_sales = df.groupby(['Country', 'Year'])['Sales'].sum().reset_index()

# Plot
sns.barplot(data=country_sales, x='Country', y='Sales', hue='Year')
plt.title('Total Sales per Country by Year')
plt.xlabel('Country')
plt.ylabel('Sales')
# plt.legend(title='Year')
plt.legend(title='Year', bbox_to_anchor=(1.05, 1), loc='upper left', frameon=False)
plt.tight_layout()
plt.show()
```
Insight: Germany had the highest total profit in 2013 and USA in 2014, while the Mexico had the lowest in both 2013 and 2014.

### Total profit per country by year
```python
# Aggregate profit per country by Year
country_sales = df.groupby(['Country', 'Year'])['Profit'].sum().reset_index()

# Plot
sns.barplot(data=country_sales, x='Country', y='Profit', hue='Year')
plt.title('Total Profit per Country by Year')
plt.xlabel('Country')
plt.ylabel('Profit')
# plt.legend(title='Year')
plt.legend(title='Year', bbox_to_anchor=(1.05, 1), loc='upper left', frameon=False)
plt.tight_layout()
plt.show()
```
Insight: Germany had the highest total profit in 2013 and France in 2014, while USA had the lowest in 2013 and Mexico in 2014.

### Total units sold per country by year
```python
# Aggregate units sold per country by year
country_sales = df.groupby(['Country', 'Year'])['Units Sold'].sum().reset_index()

# Plot
sns.barplot(data=country_sales, x='Country', y='Units Sold', hue='Year')
plt.title('Total Units Sold per Country by Year')
plt.xlabel('Country')
plt.ylabel('Units Sold')
# plt.legend(title='Year')
plt.legend(title='Year', bbox_to_anchor=(1.05, 1), loc='upper left', frameon=False)
plt.tight_layout()
plt.show()
```
Insight: Canada had the highest total units sold in 2013 and France in 2014, while USA had the lowest in 2013 and Germany in 2014.

## Discount Band Analysis
### Total sales per discount band by year
```python
# Aggregate sales per discount band by year
discount_sales = df.groupby(['Discount Band', 'Year'])['Sales'].sum().reset_index()

# Define custom order
custom_order = ['No Discount', 'Low', 'Medium', 'High']

# Plot
plt.figure(figsize=(10,6))
sns.barplot(data=discount_sales, x='Discount Band', y='Sales', order=custom_order, hue='Year')
plt.title('Sales per Discount Band')
plt.xlabel('Discount Band')
plt.ylabel('Sales')
plt.show()
```
Insight: products with a high discount band had the highest total sales in 2013 and those with a medium discount band in 2014, while products with no discount consistently had the lowest total sales in both years.

### Total profit per discount band by year
```python
# Aggregate profit per discount band by year
discount_profit = df.groupby(['Discount Band', 'Year'])['Profit'].sum().reset_index()

# Define custom order
custom_order = ['No Discount', 'Low', 'Medium', 'High']

# Plot
plt.figure(figsize=(10,6))
sns.barplot(data=discount_profit, x='Discount Band', y='Profit', order=custom_order, hue='Year')
plt.title('Profit per Discount Band')
plt.xlabel('Discount Band')
plt.ylabel('Profit')
plt.show()
```
Insight: products with a low discount band had the highest total profit in 2013 and those with a medium discount band in 2014, while products with no discount consistently had the lowest total profit in both years.

### Total units sold per discount band by year
```python
# Aggregate units sold per discount band by year
discount_units = df.groupby(['Discount Band', 'Year'])['Units Sold'].sum().reset_index()

# Define custom order
custom_order = ['No Discount', 'Low', 'Medium', 'High']

# Plot
plt.figure(figsize=(10,6))
sns.barplot(data=discount_units, x='Discount Band', y='Units Sold', order=custom_order, hue='Year')
plt.title('Units Sold per Discount Band')
plt.xlabel('Discount Band')
plt.ylabel('Units Sold')
plt.show()
```
Insight: total units sold in 2013 and 2014 are proportional to the discount band, with the high discount band had the highest units sold and no discount had the lowest.

## Correlation Heatmap
```python
numeric_cols = ['Units Sold','Manufacturing Price','Sale Price','Gross Sales','Discounts','Sales','COGS','Profit']
correlation_matrix = df[numeric_cols].corr()
sns.heatmap(correlation_matrix, annot=True, cmap='coolwarm', center=0)
plt.title('Correlation Heatmap of Numerical Variables')
plt.tight_layout()
plt.show()
```
Insight: the heatmap shows that sales, gross sales, and COGS are highly correlated (almost 1), while profit is strongly correlated with sale price, gross sales, and sales. Discounts have a moderate negative impact on profit, indicating higher discounts reduce profitability.

## Relation between Sales and Profit by Discount Band
```python
# discount band category
order = ['no', 'low', 'med', 'high']

# Plot
size_mapping = {'no': 50, 'low': 100, 'med': 150, 'high': 200}
sns.scatterplot(df, x='Sales', y='Profit', hue='Year', size='Discount Band', sizes=[size_mapping[i] for i in order], hue_order=None)
plt.ylabel('Profit')
plt.title('Sales versus Profit')
plt.show()
```
Insight: the scatter plot shows a positive relationship between sales and profit, which means higher sales generally lead to higher profit. Most data points are from 2014 with larger bubble sizes, indicating higher discount bands, but products with no or low discounts tend to achieve better profitability compared to those with higher discounts.

## Sales and Profit Distribution with Boxplots
```python
# List of features to plot Profit distribution
features = ['Segment', 'Country', 'Product', 'Discount Band']

# Set up subplots
n_features = len(features)
n_cols = 2
n_rows = (n_features + 1) // n_cols

fig, axes = plt.subplots(n_rows, n_cols, figsize=(12, n_rows*5))
axes = axes.flatten()

# Plot boxplots
for i, feature in enumerate(features):
    sns.boxplot(data=df, x=feature, y='Profit', ax=axes[i])
    axes[i].set_title(f'Profit Distribution by {feature}')
    axes[i].set_xlabel(feature)
    axes[i].set_ylabel('Profit')
    axes[i].tick_params(axis='x', rotation=0)

# Remove empty subplots if any
for j in range(i+1, len(axes)):
    fig.delaxes(axes[j])

plt.tight_layout()
plt.show()
```
```python
# List of features to plot sales distribution
features = ['Segment', 'Country', 'Product', 'Discount Band']

# Set up subplots
n_features = len(features)
n_cols = 2
n_rows = (n_features + 1) // n_cols

fig, axes = plt.subplots(n_rows, n_cols, figsize=(12, n_rows*5))
axes = axes.flatten()

# Plot boxplots
for i, feature in enumerate(features):
    sns.boxplot(data=df, x=feature, y='Sales', ax=axes[i])
    axes[i].set_title(f'Profit Distribution by {feature}')
    axes[i].set_xlabel(feature)
    axes[i].set_ylabel('Sales')
    axes[i].tick_params(axis='x', rotation=0)

# Remove empty subplots if any
for j in range(i+1, len(axes)):
    fig.delaxes(axes[j])

plt.tight_layout()
plt.show()
```
