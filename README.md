import yfinance as yf
import pandas as pd
import os

#IndexCalculator
class IndexCalculator:
    def __init__(self, tickers, method):
        self.tickers = tickers
        self.method = method.lower()
        self.data = self.get_data()

    def get_data(self):
        # Fetch historical data for all tickers
        df = yf.download(self.tickers, period='1y', interval='1d')
        return df['Adj Close']

    def calculate_price_weighted_index(self):
        # Simple average of stock prices
        index_value = self.data.mean(axis=1)
        return index_value

    def calculate_market_cap_weighted_index(self):
        # Assume equal market cap for simplicity (in real-time, fetch market caps)
        weights = [1/len(self.tickers)] * len(self.tickers)
        index_value = (self.data * weights).sum(axis=1)
        return index_value

    def calculate_free_float_market_cap_weighted_index(self):
        # Simulate free-float market caps as the latest price * arbitrary float shares
        free_float_shares = [1000, 1500, 2000, 1800, 1700]
        market_caps = self.data.mul(free_float_shares, axis=1)
        index_value = market_caps.sum(axis=1) / sum(free_float_shares)
        return index_value

    def calculate_index(self):
        if self.method == 'price-weighted':
            return self.calculate_price_weighted_index()
        elif self.method == 'market-cap-weighted':
            return self.calculate_market_cap_weighted_index()
        elif self.method == 'free-float-market-cap-weighted':
            return self.calculate_free_float_market_cap_weighted_index()
        else:
            raise ValueError("Invalid method. Choose 'price-weighted', 'market-cap-weighted', or 'free-float-market-cap-weighted'")

    def rebalance_index(self):
        # Perform quarterly rebalancing based on the latest market caps
        rebalancing_dates = pd.date_range(start=self.data.index[0], end=self.data.index[-1], freq='Q')
        rebalanced_index = []

        for i in range(len(rebalancing_dates)-1):
            start_date = rebalancing_dates[i]
            end_date = rebalancing_dates[i+1]

            # Slice data for the quarter
            quarter_data = self.data.loc[start_date:end_date]

            # Recalculate weights based on the last day's market cap of the quarter
            if self.method == 'free-float-market-cap-weighted':
                latest_prices = quarter_data.iloc[-1]
                free_float_shares = [1000, 1500, 2000, 1800, 1700]
                market_caps = latest_prices * free_float_shares
                weights = market_caps / market_caps.sum()
                rebalanced_index.append((quarter_data * weights).sum(axis=1))
            else:
                rebalanced_index.append(self.calculate_index().loc[start_date:end_date])

        # Combine all quarterly data
        final_index = pd.concat(rebalanced_index)
        return final_index

    def save_to_csv(self, filename):
        index_value = self.rebalance_index()
        index_value.to_csv(filename)
        print(f'Index values saved to {filename}')

    def backtest_index(self):
        index_value = self.rebalance_index()
        returns = index_value.pct_change().dropna()
        cumulative_returns = (1 + returns).cumprod()
        print(f'Cumulative Returns: {cumulative_returns[-1]:.2f}')

if __name__ == "__main__":
    # List of stock tickers to include in the index
    tickers = ['RELIANCE.NS', 'TCS.NS', 'HDFCBANK.NS', 'INFY.NS', 'ICICIBANK.NS']

    # Ask user for index calculation method
    print("Choose the index calculation method:")
    print("1. Price-weighted")
    print("2. Market-cap-weighted")
    print("3. Free-float-market-cap-weighted")
    choice = input("Enter your choice (1/2/3): ")

    if choice == '1':
        method = 'price-weighted'
    elif choice == '2':
        method = 'market-cap-weighted'
    elif choice == '3':
        method = 'free-float-market-cap-weighted'
    else:
        raise ValueError("Invalid choice. Please select 1, 2, or 3.")

    # Create directory for output
    output_dir = 'output'
    os.makedirs(output_dir, exist_ok=True)

    # Initialize and calculate index
    calculator = IndexCalculator(tickers, method)
    csv_path = os.path.join(output_dir, 'custom_index.csv')
    calculator.save_to_csv(csv_path)
    calculator.backtest_index()

    print(f'Index calculation and backtesting with quarterly rebalancing completed using {method}. Results saved in {output_dir}')
