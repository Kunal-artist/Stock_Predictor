# 🇮🇳 Indian Stock Price Predictor

This project is a web application built with **Streamlit** that predicts the next day's stock prices (Open, High, Low, Close) for various Indian stocks listed on the National Stock Exchange (NSE). It uses a combination of technical analysis, machine learning, and an optional **Reinforcement Learning (Q-Learning)** module to enhance prediction accuracy. The application provides an interactive and user-friendly interface for visualizing data, analyzing model performance, and getting future price predictions.

***

## ✨ Key Features

-   **Interactive UI**: A clean and modern interface built with Streamlit, making it easy for users to select stocks and configure parameters.
-   **Comprehensive Stock List**: Includes a pre-loaded list of major Indian stocks across various sectors, along with a search functionality. Users can also input any custom NSE ticker.
-   **Next-Day Price Prediction**: Predicts the Open, High, Low, and Close (OHLC) prices for the next trading day.
-   **Reinforcement Learning Enhancement**: An optional Q-Learning model can be enabled to dynamically optimize prediction parameters based on past performance, potentially improving accuracy.
-   **Robust Backtesting**: The model's performance is validated over a user-defined historical period (e.g., the last 15 days).
-   **Performance Metrics**: Key performance indicators like **Mean Absolute Percentage Error (MAPE)** and **Root Mean Squared Error (RMSE)** are calculated to evaluate the model's accuracy.
-   **Interactive Visualizations**: Uses **Plotly** to generate interactive candlestick charts, moving averages, and backtesting result graphs.
-   **Data Export**: Allows users to download the prediction results as a CSV file.



***

## 🧠 How It Works

The application follows a logical pipeline from data acquisition to prediction and visualization.

### 1. Data Fetching
-   Historical stock data is fetched from **Yahoo Finance** using the `yfinance` library.
-   The user can specify the number of years of historical data to use for analysis.
-   The code includes a robust retry mechanism (`tenacity`) to handle potential network errors when fetching data.

### 2. Feature Engineering
-   To prepare the data for prediction, several key features are engineered from the raw OHLC data:
    -   **Simple Moving Averages (SMA)**: 5-day and 20-day SMAs are calculated to identify short-term and medium-term trends.
    -   **Trend**: The percentage change in the closing price is used to measure momentum.

### 3. Prediction Model
The core prediction logic is based on a **Moving Average Crossover Strategy** combined with volatility analysis:
-   **Trend Direction**: The model first checks if the 5-day SMA is above the 20-day SMA.
    -   If **SMA_5 > SMA_20** (a bullish signal), it predicts an upward trend.
    -   If **SMA_5 < SMA_20** (a bearish signal), it predicts a downward or sideways trend.
-   **Base Prediction**: A base prediction for the next day's close is calculated based on the last closing price and the recent trend.
-   **Volatility**: The model calculates the daily volatility to estimate the potential price range.
-   **OHLC Calculation**:
    -   `Predicted Close`: The base prediction.
    -   `Predicted High/Low`: Calculated by adding/subtracting the daily volatility from the predicted close.
    -   `Predicted Open`: Estimated based on the average of recent open prices, adjusted for momentum.
-   **Constraints**: Logical constraints are applied to ensure that the predicted High is the highest value and the Low is the lowest, and that prices do not deviate unrealistically from the last close.

### 4. Reinforcement Learning (Q-Learning)
-   This is an optional, advanced feature to improve the model.
-   **Goal**: The Q-Learning agent learns the optimal `trend_factor` to use in the prediction model. This factor moderates the trend's influence, especially in bearish scenarios.
-   **State**: The state is defined by the current market condition (e.g., `is_bullish_crossover`, `last_price_return`).
-   **Action**: The agent can choose an action from a set of possible `trend_factor` values (e.g., 0.3, 0.5, 0.7, 1.0).
-   **Reward**: The agent receives a positive reward if its chosen action leads to a prediction with low error (MAPE <= 1%) and a negative reward otherwise.
-   **Q-Table**: Over time, the agent populates a Q-Table, which stores the expected reward for taking a specific action in a given state. For the final prediction, the model uses the action with the highest Q-value for the current state.

### 5. Backtesting
-   To validate the model's accuracy, it is backtested on historical data.
-   The script iterates through the last `N` days (e.g., 15 days), making a prediction for each day using only the data available *before* that day.
-   The predicted OHLC values are then compared against the actual historical prices to calculate the MAPE and RMSE for that period.

***

## 🚀 How to Run the Project

To run this application on your local machine, follow these steps:

### 1. Prerequisites
-   Ensure you have Python 3.7 or higher installed.

### 2. Installation
-   Clone the repository or save the `stock_predictor.py` file to your computer.
-   Install the required Python libraries using pip:
    ```bash
    pip install streamlit pandas numpy yfinance plotly tenacity
    ```

### 3. Execution
-   Open your terminal or command prompt.
-   Navigate to the directory where you saved the `stock_predictor.py` file.
-   Run the following command:
    ```bash
    streamlit run stock_predictor.py
    ```
-   The application will automatically open in a new tab in your default web browser.

***

## 📂 File Structure

The entire project is contained within a single Python script:

-   `stock_predictor.py`: This file contains all the code for the Streamlit UI, data fetching, feature engineering, prediction logic, backtesting, and reinforcement learning.

***

## ⚠️ Disclaimer

This application is intended for **educational and informational purposes only**. The predictions generated are based on historical data and mathematical models, which do not guarantee future performance. Stock market investments are subject to market risks. This is **not financial advice**. Always conduct your own research and consult with a qualified financial advisor before making any investment decisions.
