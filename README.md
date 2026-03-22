# polymarket-crypto-5m-dataset

Historical resolution data for all Bitcoin Up/Down 5-minute prediction markets on Polymarket.

## Dataset

**File:** `polymarket_btc_5m_enriched.csv`  
**Markets:** 20,928  
**Date Range:** December 18, 2025 — March 16, 2026  
**Source:** Polymarket Gamma API + Binance BTC/USD prices  

## Columns

| Column | Description |
|--------|-------------|
| `timestamp` | Unix timestamp of market window start |
| `date` | Date (YYYY-MM-DD) |
| `hour_utc` | Hour in UTC (0-23) |
| `minute` | Minute of hour (0, 5, 10...55) |
| `outcome` | Market resolution: `up` or `down` |
| `up_price` | Final UP token price (1.0 = resolved UP, 0.0 = resolved DOWN) |
| `down_price` | Final DOWN token price |
| `btc_open` | BTC/USD price at window open (Binance) |
| `btc_close` | BTC/USD price at window close (Binance) |
| `btc_change_pct` | % price change during window |

## Key Findings

- Overall UP win rate: **50.8%** (slight bull market bias)
- Markets cover BTC price range: **$62,696 - $97,744**
- Best mean reversion edge after large moves (>0.2% in 2 consecutive candles): **54-58% win rate**

## Resolution Criteria

Markets resolve **UP** if BTC price at end of window ≥ price at start.  
Resolution source: [Chainlink BTC/USD Data Stream](https://data.chain.link/streams/btc-usd)

## Usage

```python
import pandas as pd

df = pd.read_csv('polymarket_btc_5m_enriched.csv')

# Win rate by hour
hourly = df.groupby('hour_utc')['outcome'].apply(lambda x: (x=='up').mean())
print(hourly)

# Mean reversion edge
df['prev_change'] = df['btc_change_pct'].shift(1)
df['prev_dir'] = df['prev_change'].apply(lambda x: 'up' if x > 0 else 'down')
reversion = df.groupby('prev_dir')['outcome'].apply(lambda x: (x=='up').mean())
print(reversion)
Related
Built while developing RaptorTrade — automated trading bots for Polymarket.
Author
@felixdoteth on X
License
MIT — free to use, share and build on.
