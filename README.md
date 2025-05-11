# Wk-8--global-tracker
Global tracker

# Import necessary libraries
import pandas as pd
import requests
import matplotlib.pyplot as plt
import seaborn as sns

# Step 1: Fetch COVID-19 Data from an API
def fetch_covid_data():
    url = "https://api.covid19api.com/summary"
    try:
        response = requests.get(url)
        data = response.json()
        print("Data fetched successfully!")
        return data
    except requests.exceptions.RequestException as e:
        print(f"Error fetching data: {e}")
        return None

# Step 2: Transform and clean the data
def transform_data(data):
    if data is None:
        return None
    countries_data = data['Countries']
    # Create a DataFrame from the countries' data
    df = pd.DataFrame(countries_data)
    
    # Handle missing data (replace NaN with 0 or appropriate value)
    df.fillna(0, inplace=True)
    
    # Convert the Date column to datetime format
    df['Date'] = pd.to_datetime(df['Date'])
    
    return df

# Step 3: Perform basic analysis (statistics, grouping, etc.)
def basic_analysis(df):
    print("Basic statistics of numerical columns:")
    print(df.describe())
    
    # Example: Group by 'Country' and compute the total cases for each country
    grouped_data = df.groupby('Country')['TotalConfirmed'].sum()
    print("\nTotal confirmed cases by country:")
    print(grouped_data)

# Step 4: Data Visualization (Line chart, Bar chart, etc.)
def visualize_data(df):
    # Line chart showing the trend of total confirmed cases globally
    plt.figure(figsize=(10, 6))
    sns.lineplot(x=df['Date'], y=df['TotalConfirmed'], label='Global Total Confirmed')
    plt.title('Global Total Confirmed COVID-19 Cases Over Time')
    plt.xlabel('Date')
    plt.ylabel('Total Confirmed Cases')
    plt.legend()
    plt.grid(True)
    plt.show()

    # Bar chart showing the top 10 countries with the most cases
    top_10 = df.nlargest(10, 'TotalConfirmed')
    plt.figure(figsize=(10, 6))
    sns.barplot(x='TotalConfirmed', y='Country', data=top_10, palette='viridis')
    plt.title('Top 10 Countries with Most Confirmed COVID-19 Cases')
    plt.xlabel('Total Confirmed Cases')
    plt.ylabel('Country')
    plt.show()

    # Histogram of total confirmed cases distribution
    plt.figure(figsize=(10, 6))
    sns.histplot(df['TotalConfirmed'], kde=True, bins=20)
    plt.title('Distribution of Total Confirmed COVID-19 Cases')
    plt.xlabel('Total Confirmed Cases')
    plt.ylabel('Frequency')
    plt.show()

    # Scatter plot to show the relationship between total confirmed and total deaths
    plt.figure(figsize=(10, 6))
    sns.scatterplot(x='TotalConfirmed', y='TotalDeaths', data=df, hue='Country', palette='Set1')
    plt.title('Total Confirmed vs Total Deaths for Each Country')
    plt.xlabel('Total Confirmed Cases')
    plt.ylabel('Total Deaths')
    plt.show()

# Main function to execute all steps
def main():
    # Fetch the data from the API
    covid_data = fetch_covid_data()

    # Transform the data into a DataFrame
    covid_df = transform_data(covid_data)

    if covid_df is not None:
        # Perform basic analysis
        basic_analysis(covid_df)

        # Visualize the data with multiple plots
        visualize_data(covid_df)

    else:
        print("No data to analyze.")

# Run the main function
if __name__ == "__main__":
    main()
