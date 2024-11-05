import requests
import pandas as pd
import numpy as np
from requests.auth import HTTPBasicAuth

# Define weights for each scoring factor
WEIGHTS = {
    'win_percentage': 0.5,  # Adjust based on importance
    'profit_loss': 0.3,
    'actual_expected': 0.2
}

# Define the API base URL and credentials
base_url = "https://api.theracingapi.com"  # Base URL for The Racing API
username = "vdyLumb5e2U9D9JtcttKUTCv"  # Your API username
password = "1WEhCpMf8TBO9zP19qyISpyD"  # Your API password

# Function to fetch race data for the best dogs/horses, regardless of time and meeting
def fetch_best_race_data(region_codes=None):
    try:
        url = f"{base_url}/v1/courses"
        params = {}
        if region_codes:
            params['region_codes'] = region_codes

        response = requests.get(url, auth=HTTPBasicAuth(username, password), params=params)
        response.raise_for_status()
        return response.json()
    except requests.exceptions.RequestException as e:
        print(f"Error fetching data: {e}")
        return None

# Fetch race data
data = fetch_best_race_data(region_codes=['gb', 'ire'])

# Flatten and process the data
if data:
    courses_df = pd.json_normalize(data, 'courses')
    print("Flattened course data loaded successfully!")

    # Add example metrics for scoring (assuming metrics are fetched separately or in future integrations)
    courses_df['win_percentage'] = np.random.rand(len(courses_df))  # Placeholder random data
    courses_df['profit_loss'] = np.random.rand(len(courses_df))
    courses_df['actual_expected'] = np.random.rand(len(courses_df))
else:
    print("Failed to load race data.")

# Calculate scores and determine winners, forecast, and tricast
def calculate_predictions(df):
    # Normalize each metric to a [0,1] range
    df['win_score'] = df['win_percentage'] / df['win_percentage'].max()
    df['profit_loss_score'] = df['profit_loss'] / df['profit_loss'].max()
    df['actual_expected_score'] = df['actual_expected'] / df['actual_expected'].max()

    # Calculate the final confidence score using weights
    df['confidence_score'] = (
        df['win_score'] * WEIGHTS['win_percentage'] +
        df['profit_loss_score'] * WEIGHTS['profit_loss'] +
        df['actual_expected_score'] * WEIGHTS['actual_expected']
    )

    # Sort the DataFrame by confidence_score in descending order
    sorted_df = df.sort_values(by='confidence_score', ascending=False)

    # Select winners
    winner = sorted_df.iloc[0]  # Top-ranked course/horse
    forecast = sorted_df.iloc[:2]  # Top 2 for forecast
    tricast = sorted_df.iloc[:3]  # Top 3 for tricast

    # Display results
    print("\nPredictions:")
    print(f"Winners: {winner['course']} with confidence score {winner['confidence_score']:.2f}")
    print("Forecast:", forecast[['course', 'confidence_score']])
    print("Tricast:", tricast[['course', 'confidence_score']])

# Run predictions
if data:
    calculate_predictions(courses_df)
else:
    print("Unable to calculate predictions.")
