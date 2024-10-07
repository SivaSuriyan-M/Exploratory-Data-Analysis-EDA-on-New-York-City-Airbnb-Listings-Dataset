# NYC Airbnb Listings: Exploratory Data Analysis (EDA)

This repository contains a detailed Exploratory Data Analysis (EDA) on a dataset of Airbnb listings in New York City. The dataset contains various features, such as listing ID, host details, geographical information, price, and availability. This analysis aims to uncover patterns, trends, and anomalies, providing meaningful insights about the Airbnb market in NYC.

## Dataset Overview

The dataset consists of 48,895 rows and 16 columns:

- **id**: Unique identifier for each listing
- **name**: Name of the Airbnb listing
- **host_id**: Unique identifier for the host
- **host_name**: Name of the host
- **neighbourhood_group**: The borough/group in NYC
- **neighbourhood**: Neighborhood within the borough
- **latitude**: Latitude of the listing
- **longitude**: Longitude of the listing
- **room_type**: Type of room available
- **price**: Price per night
- **minimum_nights**: Minimum number of nights required to book
- **number_of_reviews**: Total number of reviews
- **last_review**: Date of the last review
- **reviews_per_month**: Average number of reviews per month
- **calculated_host_listings_count**: Total number of listings by the host
- **availability_365**: Number of days the listing is available in a year

## EDA Steps

### 1. Data Cleaning and Handling Missing Values

We identified columns with missing values (`name`, `host_name`, `last_review`, `reviews_per_month`) and handled them by either dropping unnecessary data or imputing values where appropriate.

```python
# Checking for missing values
missing_values = df.isnull().sum()

# Dropping rows with missing values in essential columns
df_cleaned = df.dropna(subset=['name', 'host_name'])

# Imputing missing values in 'reviews_per_month'
df['reviews_per_month'].fillna(df['reviews_per_month'].mean(), inplace=True)
```

### 2. Data Type Conversion

Converted `last_review` to datetime format to facilitate time-based analysis.

```python
df['last_review'] = pd.to_datetime(df['last_review'], errors='coerce')
```

### 3. Descriptive Statistics

Computed summary statistics for numerical columns and examined the distribution of categorical variables.

```python
# Summary statistics for numerical columns
df.describe()

# Distribution of categorical variables
df['neighbourhood_group'].value_counts()
df['room_type'].value_counts()
```

### 4. Data Visualization

We visualized key aspects of the dataset, such as price distributions, room type frequencies, and geographical spread.

```python
# Price distribution
sns.histplot(df['price'], bins=50, kde=True)
plt.title('Price Distribution')
plt.show()

# Count plot of room types
sns.countplot(x='room_type', data=df)
plt.title('Room Types in NYC Listings')
plt.show()
```

### 5. Geographical Analysis

Mapped the geographical distribution of the listings based on `latitude` and `longitude`.

```python
# Scatter plot of listings based on location
sns.scatterplot(x='longitude', y='latitude', data=df, hue='neighbourhood_group', palette='Set1')
plt.title('Geographical Distribution of Listings')
plt.show()
```

### 6. Outlier Detection

Identified outliers in numerical columns like `price` and `minimum_nights` using the Interquartile Range (IQR) method.

```python
# Outlier detection using IQR
def detect_outliers(df, column):
    Q1 = df[column].quantile(0.25)
    Q3 = df[column].quantile(0.75)
    IQR = Q3 - Q1
    lower_bound = Q1 - 1.5 * IQR
    upper_bound = Q3 + 1.5 * IQR
    return df[(df[column] < lower_bound) | (df[column] > upper_bound)]
    
# Detecting outliers in price
outliers_price = detect_outliers(df, 'price')
print(outliers_price)
```

### 7. Time Series Analysis

Analyzed trends in customer reviews over time by aggregating the data by month.

```python
# Aggregating reviews by month
df['year_month'] = df['last_review'].dt.to_period('M')
monthly_reviews = df.groupby('year_month')['reviews_per_month'].sum().reset_index()

# Plotting the trend of reviews per month
sns.lineplot(x='year_month', y='reviews_per_month', data=monthly_reviews)
plt.xticks(rotation=45)
plt.title('Trend of Reviews Per Month')
plt.show()
```

## Conclusion

This EDA provided significant insights into the Airbnb market in New York City, identifying key patterns in pricing, geographical distribution, and review dynamics. We also detected outliers in certain variables and visualized the trends in reviews over time.

---



