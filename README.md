# Railway Data Analysis Report
# =======================

# Import necessary libraries
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

# Load the dataset
from google.colab import files

uploaded = files.upload()  # Upload 'Railway_info.csv'
df = pd.read_csv("Railway_info.csv")

# =======================
# 1. DATA OVERVIEW
# =======================
print("Dataset Overview:\n")
print(df.info())  # Show column names, types, and missing values
print("\nFirst 5 Rows:\n", df.head())

# Check for missing values
missing_values = df.isnull().sum()
print("\nMissing Values:\n", missing_values)

# Check for unique values in important columns
print("\nUnique Source Stations:", df["Source_Station_Name"].nunique())
print("Unique Destination Stations:", df["Destination_Station_Name"].nunique())

# =======================
# 2. DATA CLEANING
# =======================
# Remove extra spaces and standardize column names
df.columns = df.columns.str.strip()

# Handle missing values (drop or fill)
df = df.dropna()  # Drop missing values (modify if needed)

# Standardize 'days' column (fix extra 'D' issue)
df['days'] = df['days'].str.replace(r'D$', '', regex=True).str.upper()

print("\nData Cleaning Done! \n")

# =======================
# 3. KEY STATISTICS
# =======================
print("\nTotal number of trains:", df.shape[0])
print("Most common source station:", df["Source_Station_Name"].mode()[0])
print("Most common destination station:", df["Destination_Station_Name"].mode()[0])

# Count trains per source station
train_counts = df.groupby('Source_Station_Name').size().reset_index(name='Train_Count')

# Select Top 20 Stations with Highest Train Counts
top_stations = train_counts.nlargest(20, 'Train_Count')

# =======================
# 4. VISUALIZATIONS
# =======================

# Train Distribution Per Source Station (Top 20 - Horizontal Bar Chart)
plt.figure(figsize=(12, 8))
sns.barplot(y=top_stations['Source_Station_Name'], x=top_stations['Train_Count'], palette="viridis")

plt.title("Top 20 Source Stations by Number of Trains", fontsize=14)
plt.xlabel("Train Count", fontsize=12)
plt.ylabel("Source Station", fontsize=12)
plt.grid(axis='x', linestyle='--', alpha=0.7)
plt.show()

# Distribution of Train Journeys Over the Week (Fix Overlapping Issues)
day_order = ["MONDAY", "TUESDAY", "WEDNESDAY", "THURSDAY", "FRIDAY", "SATURDAY", "SUNDAY"]
plt.figure(figsize=(8, 5))
sns.countplot(data=df, x="days", order=day_order, palette="coolwarm")

plt.title("Train Journeys by Day of the Week", fontsize=14)
plt.xlabel("Day", fontsize=12)
plt.ylabel("Number of Trains", fontsize=12)
plt.xticks(rotation=45)  # Ensure proper spacing
plt.grid(axis='y', linestyle='--', alpha=0.7)
plt.show()

# =======================
# 5. INSIGHTS & FINDINGS
# =======================

print("\nInsights & Findings:\n")
print(f"1. The dataset contains {df.shape[0]} train records connecting various stations.")
print(f"2. The most common source station is: {df['Source_Station_Name'].mode()[0]}")
print(f"3. The most common destination station is: {df['Destination_Station_Name'].mode()[0]}")
print(f"4. The busiest day for train operations is: {df['days'].mode()[0]}")
print("5. The number of trains is not evenly distributed across all days.")

# =======================
# 6. REPORT SUMMARY
# =======================
summary = """
# Railway Data Analysis Report

## 1. Data Overview
- Total Trains: {}
- Unique Source Stations: {}
- Unique Destination Stations: {}

## 2. Key Findings
- The most common **Source Station**: {}
- The most common **Destination Station**: {}
- The busiest day of the week: {}

## 3. Visualizations
- The **horizontal bar chart** shows the top 20 source stations with the highest train count.
- The **day-wise distribution chart** highlights train frequency variations.

## 4. Insights & Recommendations
- Train traffic is highest from **{}**.
- The dataset shows uneven distribution, suggesting peak-hour optimizations.
- More analysis can be done on **weekend vs. weekday operations**.

""".format(df.shape[0], df["Source_Station_Name"].nunique(), df["Destination_Station_Name"].nunique(),
           df["Source_Station_Name"].mode()[0], df["Destination_Station_Name"].mode()[0], df["days"].mode()[0],
           df["Source_Station_Name"].mode()[0])

print(summary)
