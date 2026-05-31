# IRIS FinBERT Stock Financial Sentiment Console 📰

![Python](https://img.shields.io/badge/Python-3.11%2B-blue)
![License](https://img.shields.io/badge/License-MIT-purple)
![Framework](https://img.shields.io/badge/Framework-Streamlit-red)
![Model](https://img.shields.io/badge/Model-FinBERT%20%2B%20XGBoost-black)
![Data](https://img.shields.io/badge/Data-Yahoo%20Finance-green)
![NLP](https://img.shields.io/badge/NLP-Hugging%20Face-orange)

**IRIS**, named after the Greek messenger goddess and the colors of the rainbow, is a Streamlit-based financial sentiment dashboard that combines Yahoo Finance market data, Yahoo Finance RSS news, Hugging Face's FinBERT NLP model, technical indicators, and XGBoost return forecasting to help users explore potential stock trends and market sentiment insights.

URL: [https://finbert-stockbot-jxs.streamlit.app/](https://iris-finbert-console-jxs.streamlit.app/)

---

## 📌 Project Overview

**IRIS Stock Financial Sentiment Console** is an interactive financial analytics web application built with Python and Streamlit. The app allows users to enter a stock ticker and keyword, retrieve historical market data, collect related Yahoo Finance news, score article sentiment using FinBERT, calculate technical indicators, and generate return forecasts using an XGBoost regression model.

This project was created as an educational machine learning and financial sentiment analysis dashboard. It combines natural language processing, financial market data, technical analysis, and predictive modelling in one clean web interface.

---

## 🔍 What the App Does

The dashboard walks through a full financial sentiment and forecasting workflow:

1. **User enters a stock ticker and keyword**

   * Example tickers: `AAPL`, `MSFT`, `TSLA`, `SPY`, `QQQ`, `^SPX`, `SHOP.TO`, `TD.TO`
   * Example keywords: `Apple`, `Microsoft`, `Tesla`, `S&P 500`, `Nvidia`

2. **Yahoo Finance market data is fetched**

   * The app retrieves historical daily price data using `yfinance`.
   * Stock data is cached to reduce repeated requests and help avoid rate-limit issues.
   * The app normalizes Yahoo Finance output so close price data works reliably even when `yfinance` returns MultiIndex columns.

3. **Yahoo Finance RSS news is collected**

   * The app pulls recent news headlines and article summaries from Yahoo Finance RSS feeds.
   * Articles are filtered by the user's keyword.
   * News published on non-trading days is aligned to the same or next available trading day.

4. **FinBERT sentiment analysis is applied**

   * Hugging Face's FinBERT model scores each matched article as positive, neutral, or negative.
   * Positive sentiment is represented as a positive score.
   * Negative sentiment is represented as a negative score.
   * Missing sentiment values are filled with `0.0` as a neutral baseline.

5. **Technical indicators are calculated**

   * Daily return percentage
   * 20-day closing price moving average
   * 50-day closing price moving average
   * 100-day closing price moving average
   * 20-day return moving average
   * 50-day return moving average
   * 100-day return moving average

6. **XGBoost return forecasting is performed**

   * The model uses lagged historical returns and lagged sentiment scores.
   * A time-series-aware 80/20 train-test split is used.
   * The user selects the number of business days to forecast.
   * Forecasted returns are generated recursively for the selected forecast window.

7. **Results are displayed**

   * Combined sentiment and returns table
   * Complete historical data table
   * Price moving average chart
   * Daily return moving average chart
   * XGBoost forecast chart
   * Forecast result table
   * MAE and RMSE model performance metrics
   * Feature importance chart
   * Full news sentiment analysis
   * Final written conclusion

---

## ✨ Key Features

* 📊 **Interactive Streamlit Interface**
  Clean dashboard layout with ticker input, keyword filtering, forecast slider, debugging toggle, and one-click analysis through the **Run IRIS Scan** button.

* 🌈 **IRIS Branding**
  Named after Iris, the Greek messenger goddess associated with rainbows, reflecting the app's role in carrying market messages from news data into financial insights.

* 📰 **Yahoo Finance News Sentiment**
  Collects recent Yahoo Finance RSS news articles and filters them based on the user's selected keyword.

* 🤖 **FinBERT Financial NLP**
  Uses Hugging Face's `ProsusAI/finbert` model to classify financial news sentiment as positive, neutral, or negative.

* 📈 **Yahoo Finance Market Data**
  Retrieves historical stock price data using the `yfinance` library.

* ⚡ **Cached Data Loading**
  Uses Streamlit caching for stock data, RSS feeds, and the FinBERT pipeline to improve performance and reduce repeated API/model calls.

* 📉 **Technical Analysis Indicators**
  Calculates and visualizes:

  * Daily returns
  * 20-day moving averages
  * 50-day moving averages
  * 100-day moving averages

* 🧠 **XGBoost Return Forecasting**
  Uses lagged returns and lagged sentiment scores to forecast future daily return percentages.

* 📐 **Model Performance Metrics**
  Evaluates model predictions using:

  * MAE — Mean Absolute Error
  * RMSE — Root Mean Squared Error

* 📊 **Feature Importance Analysis**
  Displays which lagged return and sentiment features had the largest influence on the XGBoost model.

* 🛡️ **Error Handling**
  Includes safeguards for invalid tickers, empty Yahoo Finance downloads, missing close price columns, insufficient data, RSS feed issues, and Streamlit/PyTorch watcher errors.

---

## 🧠 Model Methodology

IRIS combines three major modelling components: sentiment scoring, technical analysis, and machine learning-based return forecasting.

### 1. Sentiment Analysis with FinBERT

The app uses **FinBERT**, a financial language model available through Hugging Face Transformers.

For this project:

* Yahoo Finance RSS articles are retrieved for the selected ticker.
* Article summaries and titles are filtered by the user-entered keyword.
* FinBERT processes each article summary.
* Each article receives:

  * Sentiment label: `positive`, `neutral`, or `negative`
  * Sentiment score: approximately from `-1` to `+1`

Negative sentiment scores are converted into negative values so the sentiment direction can be compared against stock returns.

### 2. Trading Day Alignment

Market data only exists on trading days, while news articles can be published on weekends, holidays, or after market hours.

To handle this:

* Article dates are parsed and converted into normalized timestamps.
* Each article is mapped to the same or next available trading day.
* If no matching trading day is available, the article is skipped.
* Missing sentiment values are filled with `0.0` as a neutral sentiment baseline.

### 3. Technical Feature Engineering

IRIS calculates technical indicators from historical closing prices:

| Indicator | Description |
| --------- | ----------- |
| Daily Return % | Percentage change from the previous close |
| Close MA 20 | Short-term closing price moving average |
| Close MA 50 | Medium-term closing price moving average |
| Close MA 100 | Longer-term closing price moving average |
| Return MA 20 | Short-term return moving average |
| Return MA 50 | Medium-term return moving average |
| Return MA 100 | Longer-term return moving average |

These features help users visually interpret market trend direction and recent volatility.

### 4. XGBoost Forecasting

The forecasting model uses **XGBoost regression** to predict future daily return percentages.

For this project:

* The target variable is daily return percentage.
* The model uses a 30-day lookback window.
* Features include:

  * Lagged daily returns
  * Lagged sentiment scores
  * Current sentiment score

The data is split chronologically:

| Dataset | Purpose |
| ------- | ------- |
| First 80% | Training data |
| Final 20% | Testing data |

The model generates recursive forecasts for the user-selected number of business days.

---

## 📊 Technical Indicators

IRIS calculates moving averages for both price and returns.

| Indicator | Description |
| --------- | ----------- |
| 20-Day Moving Average | Shorter-term trend indicator |
| 50-Day Moving Average | Medium-term trend indicator |
| 100-Day Moving Average | Longer-term trend indicator |

The dashboard also creates a basic 50-day moving average signal in the final conclusion:

| Signal | Meaning |
| ------ | ------- |
| Long | Latest closing price is above the 50-day moving average |
| Short/Watchlist | Latest closing price is below or not clearly above the 50-day moving average |

These indicators are for educational trend interpretation only.

---

## 📰 News Sentiment Analysis

The news sentiment section displays all matched articles from Yahoo Finance RSS.

For each article, the dashboard shows:

* Published date
* Article title
* Sentiment label
* Sentiment score
* Article link
* Article summary

The app also calculates aggregate sentiment metrics:

| Metric | Description |
| ------ | ----------- |
| Total Articles Analyzed | Number of keyword-matched articles scored by FinBERT |
| Average Sentiment Score | Mean sentiment score across matched articles |
| Positive/Negative Ratio | Count of positive-scored articles vs negative-scored articles |

---

## 📁 Project Structure

```bash
IRIS-Stock-Financial-Sentiment-Console/
│
├── JXS-Sentiment-HuggingFace-XGBoost-StockBot-Streamlit.py   # Main Streamlit application
├── requirements.txt                                          # Python package dependencies
├── README.md                                                 # Project documentation
│
└── .streamlit/
    └── config.toml                                           # Streamlit configuration
```

---

## 🛠️ Tech Stack

| Category | Tools |
| -------- | ----- |
| Programming Language | Python |
| Web Framework | Streamlit |
| Data Source | Yahoo Finance via `yfinance` |
| News Source | Yahoo Finance RSS via `feedparser` |
| NLP Model | FinBERT through Hugging Face Transformers |
| Forecasting Model | XGBoost Regressor |
| Data Handling | pandas, numpy |
| Visualization | matplotlib, Streamlit charts |
| Model Evaluation | scikit-learn |
| Deployment | Streamlit Community Cloud |

---

## 📦 Installation

### 1. Clone the Repository

```bash
git clone https://github.com/woi-6ix/jxs_hg_xgboost_stockbot_1.git
cd jxs_hg_xgboost_stockbot_1
```

If your GitHub repository name is different, replace the URL and folder name with your actual repo.

### 2. Create a Virtual Environment

```bash
python -m venv venv
```

Activate the virtual environment:

**Windows**

```bash
venv\Scripts\activate
```

**Mac/Linux**

```bash
source venv/bin/activate
```

### 3. Install Dependencies

```bash
pip install -r requirements.txt
```

### 4. Run the Application

```bash
streamlit run JXS-Sentiment-HuggingFace-XGBoost-StockBot-Streamlit.py
```

---

## 📄 requirements.txt

Use a simple requirements file instead of a full `pip freeze` export:

```txt
streamlit
feedparser
transformers
torch>=2.9,<2.13
pandas
numpy
yfinance
matplotlib
pytz
python-dateutil
scikit-learn
xgboost
```

### Python Version Note

For Streamlit Cloud, dependency compatibility can depend on the Python version selected during deployment.

If Streamlit Cloud uses Python 3.14, avoid pinning an older exact Torch version such as `torch==2.7.1`. A flexible range like the one above helps Streamlit Cloud choose a compatible Torch wheel.

---

## ⚙️ Optional Streamlit Config

Create a `.streamlit/config.toml` file:

```toml
[server]
fileWatcherType = "none"
```

This helps reduce Streamlit file-watcher issues with PyTorch internals on hosted deployments.

---

## 🚀 Deployment on Streamlit Cloud

To deploy the app:

1. Push all project files to GitHub.
2. Go to Streamlit Community Cloud.
3. Create a new app.
4. Select the GitHub repository.
5. Set the main file path to:

```bash
JXS-Sentiment-HuggingFace-XGBoost-StockBot-Streamlit.py
```

6. Make sure `requirements.txt` is in the repo root.
7. Deploy the app.
8. If dependencies fail, reboot the app after updating `requirements.txt`.

Recommended repository files for deployment:

```bash
JXS-Sentiment-HuggingFace-XGBoost-StockBot-Streamlit.py
requirements.txt
.streamlit/config.toml
README.md
```

---

## 🖥️ How to Use the App

1. Open the Streamlit app.
2. Enter a stock ticker.

   * Example: `AAPL`, `MSFT`, `TSLA`, `SPY`, `QQQ`, `^SPX`

3. Enter a company or market keyword.

   * Example: `Apple`, `Tesla`, `S&P 500`, `Nvidia`

4. Select the number of business days to forecast.
5. Optional: enable **Show Debugging Info** if you want to inspect data loading and RSS output.
6. Click **Run IRIS Scan**.
7. Review:

   * Combined sentiment and returns table
   * Historical stock data
   * Price moving averages
   * Return moving averages
   * XGBoost forecast chart
   * Forecast result table
   * Error metrics
   * Feature importance
   * News sentiment results
   * Final conclusion

---

## 📌 Example Tickers

| Market | Example Tickers |
| ------ | --------------- |
| U.S. Stocks | `AAPL`, `MSFT`, `TSLA`, `NVDA`, `AMZN` |
| ETFs | `SPY`, `QQQ`, `DIA` |
| Indexes | `^SPX`, `^GSPC`, `^IXIC` |
| Canadian Stocks | `SHOP.TO`, `TD.TO`, `RY.TO` |

Yahoo Finance ticker symbols can vary by exchange. For Canadian stocks, `.TO` is commonly used.

---

## ⚠️ Troubleshooting

### Missing Package Error

If the app shows an error like:

```bash
ModuleNotFoundError: No module named 'feedparser'
```

Make sure your repo includes `requirements.txt` in the root directory and that it has been committed to the branch Streamlit Cloud is deploying.

### Torch Installation Error

If deployment fails with a message about Torch wheels or Python ABI compatibility, avoid pinning an incompatible exact Torch version.

Use:

```txt
torch>=2.9,<2.13
```

or select a compatible Python version during Streamlit Cloud deployment.

### Yahoo Finance Rate Limit

If the app shows a message like:

```bash
No stock data was returned
```

Yahoo Finance may be temporarily rate-limiting the request. Try:

* Waiting a few minutes
* Using a common ticker like `AAPL` or `SPY`
* Refreshing the app less often
* Keeping caching enabled
* Clearing the Streamlit cache or rebooting the app

### Invalid Ticker

If no closing price data is found, confirm that the ticker exists on Yahoo Finance.

### No Articles Matched the Keyword

If the sentiment section shows no matched articles:

* Try a broader keyword
* Use the company name instead of the ticker
* Use an index keyword like `S&P 500` for `^SPX`
* Check whether Yahoo Finance has recent RSS articles for the selected ticker

### Insufficient Forecasting Data

If the app says there is insufficient data for forecasting, the selected ticker may not have enough usable historical data after lagged features are created.

---

## 📊 Output Screens

The app generates several outputs:

* Combined sentiment and returns analysis
* Complete historical stock data
* Price moving average chart
* Daily return moving average chart
* XGBoost forecast chart
* Forecast result dataframe
* MAE and RMSE metrics
* Feature importance chart
* Full article-level sentiment analysis
* Aggregate sentiment metrics
* Final written analysis conclusion

---

## 📚 Learning Objectives

This project demonstrates:

* Building a machine learning dashboard with Streamlit
* Fetching stock market data using Python
* Pulling RSS news feeds using `feedparser`
* Applying financial NLP through Hugging Face FinBERT
* Aligning non-trading-day news data with trading-day stock data
* Engineering lagged features for time-series forecasting
* Training and evaluating an XGBoost regression model
* Visualizing technical indicators and model results
* Handling Streamlit Cloud dependency and deployment errors
* Communicating financial model outputs through an interactive interface

---

## ⚠️ Financial Disclaimer

This application is for **educational and testing purposes only**.

The sentiment scores, forecasts, technical indicators, and written conclusions generated by this app should not be interpreted as financial advice, trading recommendations, or investment guidance. Stock markets are highly volatile, and machine learning forecasts are uncertain. Always conduct independent research and consult a qualified financial professional before making financial decisions.

---

## 👨‍💻 Author

**Woi-6ix**

Built as part of a machine learning, financial modelling, natural language processing, and Streamlit dashboard development project.

---

## 📜 License

This project is licensed under the MIT License.
