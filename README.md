Dynamic Stock Market Index Calculation & Rebalancing Using Python 💹
🚀 Overview
This project demonstrates how to build and calculate a custom stock market index using real-time financial data from Yahoo Finance API. The model supports:

✅ Price-Weighted Index (PWI) - Similar to Dow Jones Industrial Average (DJIA).
✅ Market Cap Weighted Index (MCWI) - Similar to NIFTY 50, SENSEX, and S&P 500.
✅ Free-Float Market Cap Weighted Index (FFMCWI) - Similar to NIFTY 50 (India) and MSCI Indices.
✅ Quarterly Rebalancing of the Index - Automatically adjusts the index weight every 3 months.
✅ Backtesting of Index Performance - Measure how your custom index would have performed historically.
The goal is to understand how major stock indices are calculated and maintained in real-time.

💻 Features
✅ 1. Real-time Market Data
The project fetches real-time historical price data for selected stock tickers using Yahoo Finance API.

📊 Stock Prices (Open, Close, High, Low, Adjusted Close)
✅ Volume
✅ Free-float market cap (dynamically calculated)
✅ 2. Index Calculation Methods
The model supports three major index calculation methods:

Index Type	Calculation Method	Examples
Price-Weighted Index (PWI)	Simple average of stock prices	Dow Jones Industrial Average (DJIA)
Market-Cap Weighted Index (MCWI)	Weighted by market capitalization	NIFTY 50, SENSEX, S&P 500
Free-Float Market Cap Weighted Index (FFMCWI)	Weighted by free-float market capitalization	NIFTY 50, MSCI Indices
✅ 3. Dynamic Rebalancing
The model automatically rebalances the index every quarter (3 months) just like real-world indices do.

🔄 Rebalancing ensures that the index captures the market's top-performing stocks.
🧮 The weight of stocks is recalculated every quarter based on their latest free-float market capitalization.
✅ 4. Backtesting the Index Performance
The model also allows you to backtest your custom index and calculate:

📊 Daily Returns (%)
📈 Cumulative Returns (%)
🔥 Compare performance over time.
⚙️ How the Code Works
💡 1. Input: Stock Tickers & Calculation Method
The user is asked to provide:

✅ Stock Tickers (like RELIANCE.NS, TCS.NS, etc.)
✅ Index Calculation Method:
Price-Weighted Index
Market-Cap Weighted Index
Free-Float Market Cap Weighted Index
💡 2. Real-time Data Fetching
The code automatically fetches real-time historical stock prices using the Yahoo Finance API:

python
Copy
Edit
df = yf.download(self.tickers, period='1y', interval='1d')
💡 3. Calculate Index Value
Depending on the user's input, the model calculates the index as:

python
Copy
Edit
if self.method == 'price-weighted':
    return self.calculate_price_weighted_index()
elif self.method == 'market-cap-weighted':
    return self.calculate_market_cap_weighted_index()
elif self.method == 'free-float-market-cap-weighted':
    return self.calculate_free_float_market_cap_weighted_index()
💡 4. Rebalancing the Index
The model rebalances the index every 3 months like major indices (NIFTY 50, SENSEX, etc.):

python
Copy
Edit
if (date.month % 3 == 0) and (date.day == 1):
    self.rebalance_index()
💡 5. Backtesting
The model calculates:

Daily Returns using pct_change()
Cumulative Returns using:
python
Copy
Edit
cumulative_returns = (1 + daily_returns).cumprod()
📊 Output Files
The output files are saved in the /output directory:

File Name	Content
custom_index.csv	Daily Index Values
index_performance.csv	Backtested Performance
📜 Usage
✅ Step 1: Clone the Repository
bash
Copy
Edit
git clone https://github.com/your-username/your-repo-name.git
cd your-repo-name
✅ Step 2: Install Dependencies
bash
Copy
Edit
pip install yfinance pandas matplotlib numpy
✅ Step 3: Run the Code
Run the script using:

bash
Copy
Edit
python index_calculator.py
The code will:

✅ Fetch real-time stock data.
✅ Calculate the index.
✅ Rebalance it every 3 months.
✅ Backtest the index performance.
✅ Save all outputs in /output/ folder.
