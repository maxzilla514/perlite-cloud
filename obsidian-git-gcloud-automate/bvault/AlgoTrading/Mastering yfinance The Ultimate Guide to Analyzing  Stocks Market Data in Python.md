---
title: "Mastering yfinance: The Ultimate Guide to Analyzing  Stocks Market Data in Python"
source: "https://www.marketcalls.in/python/mastering-yfinance-the-ultimate-guide-to-analyzing-stocks-market-data-in-python.html"
author:
  - "[[Rajandran R]]"
published: 2025-03-31
created: 2025-06-17
description: "yfinance is a free Python library that makes it easy to fetch historical market data, financials, fundamentals, news, and even options chain data from Yahoo Finance."
tags:
  - "clippings"
---
12 min read

## Introduction

`yfinance` is a free Python library that makes it easy to fetch historical market data, financials, fundamentals, news, and even **options chain data** from Yahoo Finance.

![](https://i0.wp.com/www.marketcalls.in/wp-content/uploads/2025/03/image-77.png?resize=1024%2C512&ssl=1)

yfinance is **beginner-friendly**, great for **learning and testing** and it is perfect for:

- Beginners learning data science or finance
- Analysts prototyping strategies
- Developers building trading dashboards
- Educators creating financial tools

By the end of this guide, you’ll be able to confidently analyze stocks from **both the US and Indian markets**, including their price history, financials, and even news headlines.

## Installing yfinance

```bash
pip install yfinance --upgrade
```

## Symbol Format

| Region | Example Symbol | Format |
| --- | --- | --- |
| US | Apple | `AAPL` |
| India | Tata Motors | `TATAMOTORS.NS` |
| Index | NIFTY 50 | `^NSEI` |
| Crypto | Bitcoin | `BTC-USD` |

## Stock Symbols Format

To get data for Indian stocks, use:

- **NSE stocks** → add `.NS` suffix (e.g., `TCS.NS`, `AXISBANK.NS`)
- **BSE stocks** → add `.BO` suffix (e.g., `RELIANCE.BO` for Reliance on BSE)

```python
import yfinance as yf

df = yf.download("AAPL", period="6mo", interval="1d", auto_adjust=True)
df
```

![](https://i0.wp.com/www.marketcalls.in/wp-content/uploads/2025/03/image-69.png?resize=550%2C428&ssl=1)

```python
df_india = yf.download("RELIANCE.NS", period="3mo", interval="1d", auto_adjust=True)
df_india
```

![](https://i0.wp.com/www.marketcalls.in/wp-content/uploads/2025/03/image-70.png?resize=634%2C408&ssl=1)

## Supported Timeframes & Duration Limits

| Interval | Description | Max Period Allowed | Supported for |
| --- | --- | --- | --- |
| `1m` | 1 minute | ~7 days | US & Indian Stocks |
| `2m` | 2 minutes | ~7 days | US & Indian Stocks |
| `5m` | 5 minutes | 60 days | US & Indian Stocks |
| `15m` | 15 minutes | 60 days | US & Indian Stocks |
| `30m` | 30 minutes | 60 days | US & Indian Stocks |
| `60m` | 1 hour | 60 days | US & Indian Stocks |
| `1d` | 1 day | full history | All tickers |
| `1wk` | 1 week | full history | All tickers |
| `1mo` | 1 month | full history | All tickers |
| `3mo` | 1 quarter | full history | All tickers |

## Last 100 Days using timedelta

```python
from datetime import datetime, timedelta
import yfinance as yf

start = datetime.today() - timedelta(days=100)
end = datetime.today()

df = yf.download("ICICIBANK.NS", start=start, end=end, interval='1d', auto_adjust=True)
```

![](https://i0.wp.com/www.marketcalls.in/wp-content/uploads/2025/03/image-71.png?resize=646%2C399&ssl=1)

## period Values Supported by yfinance:

| Period | Meaning | Approx. Duration |
| --- | --- | --- |
| `1d` | 1 day | Today’s data |
| `5d` | 5 days | Past 5 trading days |
| `1mo` | 1 month | Last ~21 trading days |
| `3mo` | 3 months | Last ~63 trading days |
| `6mo` | 6 months | Last ~126 trading days |
| `1y` | 1 year | Last ~252 trading days |
| `2y` | 2 years | Last ~504 trading days |
| `5y` | 5 years | ~1,260 trading days |
| `10y` | 10 years | ~2,520 trading days |
| `ytd` | Year to Date | From Jan 1st to today |
| `max` | Maximum available history | Varies by ticker |

## Intraday Data

```python
df = yf.download("TCS.NS", period="5d", interval="5m", auto_adjust=True)
```

![](https://i0.wp.com/www.marketcalls.in/wp-content/uploads/2025/03/image-72.png?resize=660%2C401&ssl=1)

## Index vs Multi-Index (Must-Know for Multi-Stock Users)

### What is an Index?

In pandas, an **index** is the label (usually a date) for each row.

Example with single stock:

```python
Index(['Open', 'High', 'Low', 'Close', 'Adj Close', 'Volume'], dtype='object')
```

### What is a Multi-Index?

When you download **multiple stocks**, `yfinance` returns a **MultiIndex**:  
One level for the **metric** (`Open`, `Close`, etc.), and one for the **ticker** (`AAPL`, `MSFT`, etc.).

```python
df = yf.download(["AAPL","TSLA"], period="1mo")
print(df.columns)
```

**Output**

```bash
MultiIndex([( 'Close', 'AAPL'),
            ( 'Close', 'TSLA'),
            (  'High', 'AAPL'),
            (  'High', 'TSLA'),
            (   'Low', 'AAPL'),
            (   'Low', 'TSLA'),
            (  'Open', 'AAPL'),
            (  'Open', 'TSLA'),
            ('Volume', 'AAPL'),
            ('Volume', 'TSLA')],
           names=['Price', 'Ticker'])
```

### Why Use MultiIndex?

- Efficient way to represent multi-dimensional data
- Easy to compare multiple stocks side-by-side
- You can slice easily using `.xs()` (cross-section)

🔄 Extract a Single Stock:

```python
aapl = df.xs('AAPL', axis=1, level=1)
aapl
```

![](https://i0.wp.com/www.marketcalls.in/wp-content/uploads/2025/03/image-73.png?resize=550%2C672&ssl=1)

```python
search = yf.Search("Tesla", news_count=5, include_research=True)

print("Search:")

for quote in search.quotes:
    print(quote['symbol'], quote['shortname'])

print("\nNews:")

for news in search.news:
    print(news['title'])
```

**Output**

```bash
Search:
TSLA Tesla, Inc.
TSLA.MX TESLA INC
TL0.F Tesla Inc.                    
TL0.DE Tesla Inc.                    
TESLAI-USD Tesla AI USD

News:
Tesla Stock Dives Ahead Of Key Delivery Data. It’s About More Than Musk.
Tesla Rivals BYD, XPeng Have Momentum With China EV Sales Due
Tesla Insiders Are Dumping the Stock. Is It a Red Flag or a Red Herring?
Tesla deliveries likely fell as competition, Musk backlash surge
Why Tesla Quarterly Deliveries Could Be The Lowest In More Than Two Years
```

## Financials & Fundamentals

```python
msft = yf.Ticker("MSFT")
print(msft.financials)
print(msft.cashflow)
print(msft.earnings)
```

Output

```python
2024-06-30  \
Tax Effect Of Unusual Items                            -99918000.0   
Tax Rate For Calcs                                           0.182   
Normalized EBITDA                                   133558000000.0   
Total Unusual Items                                   -549000000.0   
Total Unusual Items Excluding Goodwill                -549000000.0   
Net Income From Continuing Operation Net Minori...   88136000000.0   
Reconciled Depreciation                              22287000000.0   
Reconciled Cost Of Revenue                           74114000000.0   
EBITDA                                              133009000000.0   
EBIT                                                110722000000.0   
Net Interest Income                                    222000000.0   
Interest Expense                                      2935000000.0   
Interest Income                                       3157000000.0   
Normalized Income                                    88585082000.0   
Net Income From Continuing And Discontinued Ope...   88136000000.0   
Total Expenses                                      135689000000.0   
Total Operating Income As Reported                  109433000000.0   
Diluted Average Shares                                7469000000.0   
Basic Average Shares                                  7431000000.0   
Diluted EPS                                                   11.8   
Basic EPS                                                    11.86   
Diluted NI Availto Com Stockholders                  88136000000.0   
Net Income Common Stockholders                       88136000000.0   
Net Income                                           88136000000.0   
Net Income Including Noncontrolling Interests        88136000000.0   
Net Income Continuous Operations                     88136000000.0   
Tax Provision                                        19651000000.0   
Pretax Income                                       107787000000.0   
Other Income Expense                                 -1868000000.0   
Other Non Operating Income Expenses                  -1319000000.0   
Special Income Charges                                -206000000.0   
Write Off                                              206000000.0   
Gain On Sale Of Security                              -343000000.0   
Net Non Operating Interest Income Expense              222000000.0   
Interest Expense Non Operating                        2935000000.0   
Interest Income Non Operating                         3157000000.0   
Operating Income                                    109433000000.0   
Operating Expense                                    61575000000.0   
Research And Development                             29510000000.0   
Selling General And Administration                   32065000000.0   
Selling And Marketing Expense                        24456000000.0   
General And Administrative Expense                    7609000000.0   
Other Gand A                                          7609000000.0   
Gross Profit                                        171008000000.0   
Cost Of Revenue                                      74114000000.0   
Total Revenue                                       245122000000.0   
Operating Revenue                                   245122000000.0   

                                                        2023-06-30  \
Tax Effect Of Unusual Items                             -2850000.0   
Tax Rate For Calcs                                            0.19   
Normalized EBITDA                                   105155000000.0   
Total Unusual Items                                    -15000000.0   
Total Unusual Items Excluding Goodwill                 -15000000.0   
Net Income From Continuing Operation Net Minori...   72361000000.0   
Reconciled Depreciation                              13861000000.0   
Reconciled Cost Of Revenue                           65863000000.0   
EBITDA                                              105140000000.0   
EBIT                                                 91279000000.0   
Net Interest Income                                   1026000000.0   
Interest Expense                                      1968000000.0   
Interest Income                                       2994000000.0   
Normalized Income                                    72373150000.0   
Net Income From Continuing And Discontinued Ope...   72361000000.0   
Total Expenses                                      123392000000.0   
Total Operating Income As Reported                   88523000000.0   
Diluted Average Shares                                7472000000.0   
Basic Average Shares                                  7446000000.0   
Diluted EPS                                                   9.68   
Basic EPS                                                     9.72   
Diluted NI Availto Com Stockholders                  72361000000.0   
Net Income Common Stockholders                       72361000000.0   
Net Income                                           72361000000.0   
Net Income Including Noncontrolling Interests        72361000000.0   
Net Income Continuous Operations                     72361000000.0   
Tax Provision                                        16950000000.0   
Pretax Income                                        89311000000.0   
Other Income Expense                                  -238000000.0   
Other Non Operating Income Expenses                   -223000000.0   
Special Income Charges                                 -30000000.0   
Write Off                                               30000000.0   
Gain On Sale Of Security                                15000000.0   
Net Non Operating Interest Income Expense             1026000000.0   
Interest Expense Non Operating                        1968000000.0   
Interest Income Non Operating                         2994000000.0   
Operating Income                                     88523000000.0   
Operating Expense                                    57529000000.0   
Research And Development                             27195000000.0   
Selling General And Administration                   30334000000.0   
Selling And Marketing Expense                        22759000000.0   
General And Administrative Expense                    7575000000.0   
Other Gand A                                          7575000000.0   
Gross Profit                                        146052000000.0   
Cost Of Revenue                                      65863000000.0   
Total Revenue                                       211915000000.0   
Operating Revenue                                   211915000000.0   

                                                        2022-06-30  \
Tax Effect Of Unusual Items                             43754000.0   
Tax Rate For Calcs                                           0.131   
Normalized EBITDA                                    99905000000.0   
Total Unusual Items                                    334000000.0   
Total Unusual Items Excluding Goodwill                 334000000.0   
Net Income From Continuing Operation Net Minori...   72738000000.0   
Reconciled Depreciation                              14460000000.0   
Reconciled Cost Of Revenue                           62650000000.0   
EBITDA                                              100239000000.0   
EBIT                                                 85779000000.0   
Net Interest Income                                     31000000.0   
Interest Expense                                      2063000000.0   
Interest Income                                       2094000000.0   
Normalized Income                                    72447754000.0   
Net Income From Continuing And Discontinued Ope...   72738000000.0   
Total Expenses                                      114887000000.0   
Total Operating Income As Reported                   83383000000.0   
Diluted Average Shares                                7540000000.0   
Basic Average Shares                                  7496000000.0   
Diluted EPS                                                   9.65   
Basic EPS                                                      9.7   
Diluted NI Availto Com Stockholders                  72738000000.0   
Net Income Common Stockholders                       72738000000.0   
Net Income                                           72738000000.0   
Net Income Including Noncontrolling Interests        72738000000.0   
Net Income Continuous Operations                     72738000000.0   
Tax Provision                                        10978000000.0   
Pretax Income                                        83716000000.0   
Other Income Expense                                   302000000.0   
Other Non Operating Income Expenses                    -32000000.0   
Special Income Charges                                -101000000.0   
Write Off                                              101000000.0   
Gain On Sale Of Security                               435000000.0   
Net Non Operating Interest Income Expense               31000000.0   
Interest Expense Non Operating                        2063000000.0   
Interest Income Non Operating                         2094000000.0   
Operating Income                                     83383000000.0   
Operating Expense                                    52237000000.0   
Research And Development                             24512000000.0   
Selling General And Administration                   27725000000.0   
Selling And Marketing Expense                        21825000000.0   
General And Administrative Expense                    5900000000.0   
Other Gand A                                          5900000000.0   
Gross Profit                                        135620000000.0   
Cost Of Revenue                                      62650000000.0   
Total Revenue                                       198270000000.0   
Operating Revenue                                   198270000000.0   

                                                            2021-06-30  
Tax Effect Of Unusual Items                           180160797.164637  
Tax Rate For Calcs                                            0.138266  
Normalized EBITDA                                        83831000000.0  
Total Unusual Items                                       1303000000.0  
Total Unusual Items Excluding Goodwill                    1303000000.0  
Net Income From Continuing Operation Net Minori...       61271000000.0  
Reconciled Depreciation                                  11686000000.0  
Reconciled Cost Of Revenue                               52232000000.0  
EBITDA                                                   85134000000.0  
EBIT                                                     73448000000.0  
Net Interest Income                                       -215000000.0  
Interest Expense                                          2346000000.0  
Interest Income                                           2131000000.0  
Normalized Income                                   60148160797.164635  
Net Income From Continuing And Discontinued Ope...       61271000000.0  
Total Expenses                                           98172000000.0  
Total Operating Income As Reported                       69916000000.0  
Diluted Average Shares                                    7608000000.0  
Basic Average Shares                                      7547000000.0  
Diluted EPS                                                       8.05  
Basic EPS                                                         8.12  
Diluted NI Availto Com Stockholders                      61271000000.0  
Net Income Common Stockholders                           61271000000.0  
Net Income                                               61271000000.0  
Net Income Including Noncontrolling Interests            61271000000.0  
Net Income Continuous Operations                         61271000000.0  
Tax Provision                                             9831000000.0  
Pretax Income                                            71102000000.0  
Other Income Expense                                      1401000000.0  
Other Non Operating Income Expenses                         98000000.0  
Special Income Charges                                     -13000000.0  
Write Off                                                   13000000.0  
Gain On Sale Of Security                                  1316000000.0  
Net Non Operating Interest Income Expense                 -215000000.0  
Interest Expense Non Operating                            2346000000.0  
Interest Income Non Operating                             2131000000.0  
Operating Income                                         69916000000.0  
Operating Expense                                        45940000000.0  
Research And Development                                 20716000000.0  
Selling General And Administration                       25224000000.0  
Selling And Marketing Expense                            20117000000.0  
General And Administrative Expense                        5107000000.0  
Other Gand A                                              5107000000.0  
Gross Profit                                            115856000000.0  
Cost Of Revenue                                          52232000000.0  
Total Revenue                                           168088000000.0  
Operating Revenue                                       168088000000.0  
                                                    2024-06-30     2023-06-30  \
Free Cash Flow                                   74071000000.0  59475000000.0   
Repurchase Of Capital Stock                     -17254000000.0 -22245000000.0   
Repayment Of Debt                               -29070000000.0  -2750000000.0   
Issuance Of Debt                                 29645000000.0            0.0   
Issuance Of Capital Stock                         2002000000.0   1866000000.0   
Capital Expenditure                             -44477000000.0 -28107000000.0   
End Cash Position                                18315000000.0  34704000000.0   
Beginning Cash Position                          34704000000.0  13931000000.0   
Effect Of Exchange Rate Changes                   -210000000.0   -194000000.0   
Changes In Cash                                 -16179000000.0  20967000000.0   
Financing Cash Flow                             -37757000000.0 -43935000000.0   
Cash Flow From Continuing Financing Activities  -37757000000.0 -43935000000.0   
Net Other Financing Charges                      -1309000000.0  -1006000000.0   
Cash Dividends Paid                             -21771000000.0 -19800000000.0   
Common Stock Dividend Paid                      -21771000000.0 -19800000000.0   
Net Common Stock Issuance                       -15252000000.0 -20379000000.0   
Common Stock Payments                           -17254000000.0 -22245000000.0   
Common Stock Issuance                             2002000000.0   1866000000.0   
Net Issuance Payments Of Debt                      575000000.0  -2750000000.0   
Net Short Term Debt Issuance                      5250000000.0            0.0   
Short Term Debt Payments                                   NaN            NaN   
Short Term Debt Issuance                          5250000000.0            0.0   
Net Long Term Debt Issuance                      -4675000000.0  -2750000000.0   
Long Term Debt Payments                         -29070000000.0  -2750000000.0   
Long Term Debt Issuance                          24395000000.0            0.0   
Investing Cash Flow                             -96970000000.0 -22680000000.0   
Cash Flow From Continuing Investing Activities  -96970000000.0 -22680000000.0   
Net Other Investing Changes                      -1298000000.0  -3116000000.0   
Net Investment Purchase And Sale                 17937000000.0  10213000000.0   
Sale Of Investment                               35669000000.0  47864000000.0   
Purchase Of Investment                          -17732000000.0 -37651000000.0   
Net Business Purchase And Sale                  -69132000000.0  -1670000000.0   
Purchase Of Business                            -69132000000.0  -1670000000.0   
Net PPE Purchase And Sale                       -44477000000.0 -28107000000.0   
Purchase Of PPE                                 -44477000000.0 -28107000000.0   
Operating Cash Flow                             118548000000.0  87582000000.0   
Cash Flow From Continuing Operating Activities  118548000000.0  87582000000.0   
Change In Working Capital                         1824000000.0  -2388000000.0   
Change In Other Working Capital                   5348000000.0   5535000000.0   
Change In Other Current Liabilities               5616000000.0   2825000000.0   
Change In Other Current Assets                   -8465000000.0  -4824000000.0   
Change In Payables And Accrued Expense            5232000000.0  -3079000000.0   
Change In Payable                                 5232000000.0  -3079000000.0   
Change In Account Payable                         3545000000.0  -2721000000.0   
Change In Tax Payable                             1687000000.0   -358000000.0   
Change In Income Tax Payable                      1687000000.0   -358000000.0   
Change In Inventory                               1284000000.0   1242000000.0   
Change In Receivables                            -7191000000.0  -4087000000.0   
Changes In Account Receivables                   -7191000000.0  -4087000000.0   
Stock Based Compensation                         10734000000.0   9611000000.0   
Unrealized Gain Loss On Investment Securities     -146000000.0   -303000000.0   
Asset Impairment Charge                            206000000.0     30000000.0   
Deferred Tax                                     -4738000000.0  -6059000000.0   
Deferred Income Tax                              -4738000000.0  -6059000000.0   
Depreciation Amortization Depletion              22287000000.0  13861000000.0   
Depreciation And Amortization                    22287000000.0  13861000000.0   
Depreciation                                     22287000000.0  13861000000.0   
Operating Gains Losses                             245000000.0    469000000.0   
Gain Loss On Investment Securities                 245000000.0    469000000.0   
Net Income From Continuing Operations            88136000000.0  72361000000.0   

                                                   2022-06-30     2021-06-30  \
Free Cash Flow                                  65149000000.0  56118000000.0   
Repurchase Of Capital Stock                    -32696000000.0 -27385000000.0   
Repayment Of Debt                               -9023000000.0  -3750000000.0   
Issuance Of Debt                                          0.0            NaN   
Issuance Of Capital Stock                        1841000000.0   1693000000.0   
Capital Expenditure                            -23886000000.0 -20622000000.0   
End Cash Position                               13931000000.0  14224000000.0   
Beginning Cash Position                         14224000000.0  13576000000.0   
Effect Of Exchange Rate Changes                  -141000000.0    -29000000.0   
Changes In Cash                                  -152000000.0    677000000.0   
Financing Cash Flow                            -58876000000.0 -48486000000.0   
Cash Flow From Continuing Financing Activities -58876000000.0 -48486000000.0   
Net Other Financing Charges                      -863000000.0  -2523000000.0   
Cash Dividends Paid                            -18135000000.0 -16521000000.0   
Common Stock Dividend Paid                     -18135000000.0 -16521000000.0   
Net Common Stock Issuance                      -30855000000.0 -25692000000.0   
Common Stock Payments                          -32696000000.0 -27385000000.0   
Common Stock Issuance                            1841000000.0   1693000000.0   
Net Issuance Payments Of Debt                   -9023000000.0  -3750000000.0   
Net Short Term Debt Issuance                              0.0            NaN   
Short Term Debt Payments                                  NaN            NaN   
Short Term Debt Issuance                                  0.0            NaN   
Net Long Term Debt Issuance                     -9023000000.0  -3750000000.0   
Long Term Debt Payments                         -9023000000.0  -3750000000.0   
Long Term Debt Issuance                                   0.0            NaN   
Investing Cash Flow                            -30311000000.0 -27577000000.0   
Cash Flow From Continuing Investing Activities -30311000000.0 -27577000000.0   
Net Other Investing Changes                     -2825000000.0   -922000000.0   
Net Investment Purchase And Sale                18438000000.0   2876000000.0   
Sale Of Investment                              44894000000.0  65800000000.0   
Purchase Of Investment                         -26456000000.0 -62924000000.0   
Net Business Purchase And Sale                 -22038000000.0  -8909000000.0   
Purchase Of Business                           -22038000000.0  -8909000000.0   
Net PPE Purchase And Sale                      -23886000000.0 -20622000000.0   
Purchase Of PPE                                -23886000000.0 -20622000000.0   
Operating Cash Flow                             89035000000.0  76740000000.0   
Cash Flow From Continuing Operating Activities  89035000000.0  76740000000.0   
Change In Working Capital                         446000000.0   -936000000.0   
Change In Other Working Capital                  5109000000.0   4633000000.0   
Change In Other Current Liabilities              3169000000.0   5551000000.0   
Change In Other Current Assets                  -3514000000.0  -4391000000.0   
Change In Payables And Accrued Expense           3639000000.0    489000000.0   
Change In Payable                                3639000000.0    489000000.0   
Change In Account Payable                        2943000000.0   2798000000.0   
Change In Tax Payable                             696000000.0  -2309000000.0   
Change In Income Tax Payable                      696000000.0  -2309000000.0   
Change In Inventory                             -1123000000.0   -737000000.0   
Change In Receivables                           -6834000000.0  -6481000000.0   
Changes In Account Receivables                  -6834000000.0  -6481000000.0   
Stock Based Compensation                         7502000000.0   6118000000.0   
Unrealized Gain Loss On Investment Securities    -509000000.0  -1057000000.0   
Asset Impairment Charge                           101000000.0     13000000.0   
Deferred Tax                                    -5702000000.0   -150000000.0   
Deferred Income Tax                             -5702000000.0   -150000000.0   
Depreciation Amortization Depletion             14460000000.0  11686000000.0   
Depreciation And Amortization                   14460000000.0  11686000000.0   
Depreciation                                    14460000000.0  11686000000.0   
Operating Gains Losses                             -1000000.0   -205000000.0   
Gain Loss On Investment Securities                 -1000000.0   -205000000.0   
Net Income From Continuing Operations           72738000000.0  61271000000.0   

                                               2020-06-30  
Free Cash Flow                                        NaN  
Repurchase Of Capital Stock                           NaN  
Repayment Of Debt                                     NaN  
Issuance Of Debt                                      0.0  
Issuance Of Capital Stock                             NaN  
Capital Expenditure                                   NaN  
End Cash Position                                     NaN  
Beginning Cash Position                               NaN  
Effect Of Exchange Rate Changes                       NaN  
Changes In Cash                                       NaN  
Financing Cash Flow                                   NaN  
Cash Flow From Continuing Financing Activities        NaN  
Net Other Financing Charges                           NaN  
Cash Dividends Paid                                   NaN  
Common Stock Dividend Paid                            NaN  
Net Common Stock Issuance                             NaN  
Common Stock Payments                                 NaN  
Common Stock Issuance                                 NaN  
Net Issuance Payments Of Debt                         NaN  
Net Short Term Debt Issuance                          0.0  
Short Term Debt Payments                              0.0  
Short Term Debt Issuance                              NaN  
Net Long Term Debt Issuance                           NaN  
Long Term Debt Payments                               NaN  
Long Term Debt Issuance                               0.0  
Investing Cash Flow                                   NaN  
Cash Flow From Continuing Investing Activities        NaN  
Net Other Investing Changes                           NaN  
Net Investment Purchase And Sale                      NaN  
Sale Of Investment                                    NaN  
Purchase Of Investment                                NaN  
Net Business Purchase And Sale                        NaN  
Purchase Of Business                                  NaN  
Net PPE Purchase And Sale                             NaN  
Purchase Of PPE                                       NaN  
Operating Cash Flow                                   NaN  
Cash Flow From Continuing Operating Activities        NaN  
Change In Working Capital                             NaN  
Change In Other Working Capital                       NaN  
Change In Other Current Liabilities                   NaN  
Change In Other Current Assets                        NaN  
Change In Payables And Accrued Expense                NaN  
Change In Payable                                     NaN  
Change In Account Payable                             NaN  
Change In Tax Payable                                 NaN  
Change In Income Tax Payable                          NaN  
Change In Inventory                                   NaN  
Change In Receivables                                 NaN  
Changes In Account Receivables                        NaN  
Stock Based Compensation                              NaN  
Unrealized Gain Loss On Investment Securities         NaN  
Asset Impairment Charge                               NaN  
Deferred Tax                                          NaN  
Deferred Income Tax                                   NaN  
Depreciation Amortization Depletion                   NaN  
Depreciation And Amortization                         NaN  
Depreciation                                          NaN  
Operating Gains Losses                                NaN  
Gain Loss On Investment Securities                    NaN  
Net Income From Continuing Operations                 NaN
```

## Option Chain Data

```python
spy    = yf.Ticker("SPY")
expiry = spy.options[0]
chain  = spy.option_chain(expiry)

chain.calls.head()
```

![](https://i0.wp.com/www.marketcalls.in/wp-content/uploads/2025/03/image-74.png?resize=1024%2C135&ssl=1)

```python
spy    = yf.Ticker("SPY")
expiry = spy.options[0]
chain  = spy.option_chain(expiry)

chain.puts.head()
```

![](https://i0.wp.com/www.marketcalls.in/wp-content/uploads/2025/03/image-75.png?resize=1024%2C136&ssl=1)

## Sector & Industry Analysis

```python
from yfinance import Sector

tech = Sector('technology')
print(tech.top_companies)
```

**Output**

```bash
name      rating  \
symbol                                                             
AAPL                                      Apple Inc.         Buy   
MSFT                           Microsoft Corporation  Strong Buy   
NVDA                              NVIDIA Corporation  Strong Buy   
AVGO                                   Broadcom Inc.  Strong Buy   
ORCL                              Oracle Corporation         Buy   
CRM                                 Salesforce, Inc.         Buy   
CSCO                             Cisco Systems, Inc.         Buy   
IBM      International Business Machines Corporation         Buy   
ACN                                    Accenture plc         Buy   
PLTR                      Palantir Technologies Inc.        Hold   
INTU                                     Intuit Inc.         Buy   
ADBE                                      Adobe Inc.         Buy   
QCOM                           QUALCOMM Incorporated         Buy   
AMD                     Advanced Micro Devices, Inc.         Buy   
TXN                   Texas Instruments Incorporated         Buy   
NOW                                 ServiceNow, Inc.         Buy   
UBER                         Uber Technologies, Inc.         Buy   
ADP                  Automatic Data Processing, Inc.        Hold   
FI                                      Fiserv, Inc.         Buy   
AMAT                         Applied Materials, Inc.         Buy   
ADI                             Analog Devices, Inc.         Buy   
INTC                               Intel Corporation        Hold   
ANET                             Arista Networks Inc         Buy   
MU                           Micron Technology, Inc.         Buy   
LRCX                        Lam Research Corporation         Buy   
KLAC                                 KLA Corporation         Buy   
APP                             AppLovin Corporation         Buy   
CRWD                      CrowdStrike Holdings, Inc.         Buy   
APH                             Amphenol Corporation         Buy   
MSI                         Motorola Solutions, Inc.         Buy   
FTNT                                  Fortinet, Inc.        Hold   
CDNS                    Cadence Design Systems, Inc.         Buy   
MSTR                           Strategy Incorporated  Strong Buy   
SNPS                                  Synopsys, Inc.  Strong Buy   
ROP                         Roper Technologies, Inc.         Buy   
DELL                          Dell Technologies Inc.         Buy   
WDAY                                   Workday, Inc.         Buy   
ADSK                                  Autodesk, Inc.         Buy   
PANW                        Palo Alto Networks, Inc.         Buy   
PAYX                                   Paychex, Inc.        Hold   
TEAM                           Atlassian Corporation         Buy   
MRVL                        Marvell Technology, Inc.  Strong Buy   
SNOW                                  Snowflake Inc.         Buy   
NXPI                         NXP Semiconductors N.V.         Buy   
FICO                          Fair Isaac Corporation         Buy   
TEL                              TE Connectivity plc         Buy   
GRMN                                     Garmin Ltd.        Hold   
FIS     Fidelity National Information Services, Inc.         Buy   
GLW                             Corning Incorporated         Buy   
NET                                 Cloudflare, Inc.         Buy   

        market weight  
symbol                 
AAPL         0.200167  
MSFT         0.167977  
NVDA         0.157289  
AVGO         0.046511  
ORCL         0.023305  
CRM          0.015404  
CSCO         0.014813  
IBM          0.013888  
ACN          0.011552  
PLTR         0.011301  
INTU         0.010240  
ADBE         0.010145  
QCOM         0.010124  
AMD          0.010004  
TXN          0.009834  
NOW          0.009669  
UBER         0.009216  
ADP          0.007481  
FI           0.007469  
AMAT         0.007191  
ADI          0.006026  
INTC         0.005857  
ANET         0.005847  
MU           0.005792  
LRCX         0.005595  
KLAC         0.005437  
APP          0.005177  
CRWD         0.005153  
APH          0.004784  
MSI          0.004420  
FTNT         0.004402  
CDNS         0.004204  
MSTR         0.004072  
SNPS         0.004021  
ROP          0.003831  
DELL         0.003814  
WDAY         0.003760  
ADSK         0.003387  
PANW         0.003356  
PAYX         0.003329  
TEAM         0.003291  
MRVL         0.003186  
SNOW         0.002907  
NXPI         0.002889  
FICO         0.002685  
TEL          0.002576  
GRMN         0.002508  
FIS          0.002416  
GLW          0.002320  
NET          0.002293
```

```python
tcs = yf.Ticker("TCS.NS")

print(tcs.info['sector'])
print(tcs.earnings)
print(tcs.financials)
print(tcs.actions)  # Dividends and stock splits
```

Output

```bash
Technology
None
                                                         2024-03-31  \
Tax Effect Of Unusual Items                           -1924680000.0   
Tax Rate For Calcs                                            0.258   
Normalized EBITDA                                    685060000000.0   
Total Unusual Items                                   -7460000000.0   
Total Unusual Items Excluding Goodwill                -7460000000.0   
Net Income From Continuing Operation Net Minori...   459080000000.0   
Reconciled Depreciation                               49850000000.0   
Reconciled Cost Of Revenue                          1328710000000.0   
EBITDA                                               677600000000.0   
EBIT                                                 627750000000.0   
Net Interest Income                                   30030000000.0   
Interest Expense                                       7780000000.0   
Interest Income                                       37810000000.0   
Normalized Income                                    464615320000.0   
Net Income From Continuing And Discontinued Ope...   459080000000.0   
Total Expenses                                      1814680000000.0   
Diluted Average Shares                                 3646851755.0   
Basic Average Shares                                   3646851755.0   
Diluted EPS                                                  125.88   
Basic EPS                                                    125.88   
Diluted NI Availto Com Stockholders                  459080000000.0   
Net Income Common Stockholders                       459080000000.0   
Otherunder Preferred Stock Dividend                             0.0   
Net Income                                           459080000000.0   
Minority Interests                                    -1910000000.0   
Net Income Including Noncontrolling Interests        460990000000.0   
Net Income Continuous Operations                     460990000000.0   
Tax Provision                                        158980000000.0   
Pretax Income                                        619970000000.0   
Other Non Operating Income Expenses                     510000000.0   
Special Income Charges                               -10580000000.0   
Other Special Charges                                  9440000000.0   
Write Off                                              1140000000.0   
Net Non Operating Interest Income Expense             30030000000.0   
Interest Expense Non Operating                         7780000000.0   
Interest Income Non Operating                         37810000000.0   
Operating Income                                     594250000000.0   
Operating Expense                                    485970000000.0   
Other Operating Expenses                             145690000000.0   
Depreciation And Amortization In Income Statement     49850000000.0   
Depreciation Income Statement                         49850000000.0   
Selling General And Administration                   180810000000.0   
General And Administrative Expense                   180810000000.0   
Gross Profit                                        1080220000000.0   
Cost Of Revenue                                     1328710000000.0   
Total Revenue                                       2408930000000.0   
Operating Revenue                                   2408930000000.0   

                                                         2023-03-31  \
Tax Effect Of Unusual Items                             277560000.0   
Tax Rate For Calcs                                            0.257   
Normalized EBITDA                                    626000000000.0   
Total Unusual Items                                    1080000000.0   
Total Unusual Items Excluding Goodwill                 1080000000.0   
Net Income From Continuing Operation Net Minori...   421470000000.0   
Reconciled Depreciation                               50220000000.0   
Reconciled Cost Of Revenue                          1197590000000.0   
EBITDA                                               627080000000.0   
EBIT                                                 576860000000.0   
Net Interest Income                                   24690000000.0   
Interest Expense                                       7790000000.0   
Interest Income                                       32480000000.0   
Normalized Income                                    420667560000.0   
Net Income From Continuing And Discontinued Ope...   421470000000.0   
Total Expenses                                      1710810000000.0   
Diluted Average Shares                                 3659051373.0   
Basic Average Shares                                   3659051373.0   
Diluted EPS                                                  115.19   
Basic EPS                                                    115.19   
Diluted NI Availto Com Stockholders                  421470000000.0   
Net Income Common Stockholders                       421470000000.0   
Otherunder Preferred Stock Dividend                             0.0   
Net Income                                           421470000000.0   
Minority Interests                                    -1560000000.0   
Net Income Including Noncontrolling Interests        423030000000.0   
Net Income Continuous Operations                     423030000000.0   
Tax Provision                                        146040000000.0   
Pretax Income                                        569070000000.0   
Other Non Operating Income Expenses                     970000000.0   
Special Income Charges                                -1160000000.0   
Other Special Charges                                  -240000000.0   
Write Off                                              1400000000.0   
Net Non Operating Interest Income Expense             24690000000.0   
Interest Expense Non Operating                         7790000000.0   
Interest Income Non Operating                         32480000000.0   
Operating Income                                     543770000000.0   
Operating Expense                                    513220000000.0   
Other Operating Expenses                             130730000000.0   
Depreciation And Amortization In Income Statement     50220000000.0   
Depreciation Income Statement                         50220000000.0   
Selling General And Administration                   235830000000.0   
General And Administrative Expense                   235830000000.0   
Gross Profit                                        1056990000000.0   
Cost Of Revenue                                     1197590000000.0   
Total Revenue                                       2254580000000.0   
Operating Revenue                                   2254580000000.0   

                                                         2022-03-31  \
Tax Effect Of Unusual Items                             238080000.0   
Tax Rate For Calcs                                            0.256   
Normalized EBITDA                                    569820000000.0   
Total Unusual Items                                     930000000.0   
Total Unusual Items Excluding Goodwill                  930000000.0   
Net Income From Continuing Operation Net Minori...   383270000000.0   
Reconciled Depreciation                               46040000000.0   
Reconciled Cost Of Revenue                          1002670000000.0   
EBITDA                                               570750000000.0   
EBIT                                                 524710000000.0   
Net Interest Income                                   18790000000.0   
Interest Expense                                       7840000000.0   
Interest Income                                       26630000000.0   
Normalized Income                                    382578080000.0   
Net Income From Continuing And Discontinued Ope...   383270000000.0   
Total Expenses                                      1431660000000.0   
Diluted Average Shares                                 3698832195.0   
Basic Average Shares                                   3698832195.0   
Diluted EPS                                                  103.62   
Basic EPS                                                    103.62   
Diluted NI Availto Com Stockholders                  383270000000.0   
Net Income Common Stockholders                       383270000000.0   
Otherunder Preferred Stock Dividend                             0.0   
Net Income                                           383270000000.0   
Minority Interests                                    -1220000000.0   
Net Income Including Noncontrolling Interests        384490000000.0   
Net Income Continuous Operations                     384490000000.0   
Tax Provision                                        132380000000.0   
Pretax Income                                        516870000000.0   
Other Non Operating Income Expenses                     780000000.0   
Special Income Charges                                -1050000000.0   
Other Special Charges                                  -300000000.0   
Write Off                                              1350000000.0   
Net Non Operating Interest Income Expense             18790000000.0   
Interest Expense Non Operating                         7840000000.0   
Interest Income Non Operating                         26630000000.0   
Operating Income                                     485880000000.0   
Operating Expense                                    428990000000.0   
Other Operating Expenses                             103860000000.0   
Depreciation And Amortization In Income Statement     46040000000.0   
Depreciation Income Statement                         46040000000.0   
Selling General And Administration                   194590000000.0   
General And Administrative Expense                   194590000000.0   
Gross Profit                                         914870000000.0   
Cost Of Revenue                                     1002670000000.0   
Total Revenue                                       1917540000000.0   
Operating Revenue                                   1917540000000.0   

                                                         2021-03-31  
Tax Effect Of Unusual Items                           -2821120000.0  
Tax Rate For Calcs                                            0.256  
Normalized EBITDA                                    495640000000.0  
Total Unusual Items                                  -11020000000.0  
Total Unusual Items Excluding Goodwill               -11020000000.0  
Net Income From Continuing Operation Net Minori...   324300000000.0  
Reconciled Depreciation                               40650000000.0  
Reconciled Cost Of Revenue                           868750000000.0  
EBITDA                                               484620000000.0  
EBIT                                                 443970000000.0  
Net Interest Income                                   18670000000.0  
Interest Expense                                       6370000000.0  
Interest Income                                       25040000000.0  
Normalized Income                                    332498880000.0  
Net Income From Continuing And Discontinued Ope...   324300000000.0  
Total Expenses                                      1214950000000.0  
Diluted Average Shares                                 3740110733.0  
Basic Average Shares                                   3740110733.0  
Diluted EPS                                                   86.71  
Basic EPS                                                     86.71  
Diluted NI Availto Com Stockholders                  324300000000.0  
Net Income Common Stockholders                       324300000000.0  
Otherunder Preferred Stock Dividend                             0.0  
Net Income                                           324300000000.0  
Minority Interests                                    -1320000000.0  
Net Income Including Noncontrolling Interests        325620000000.0  
Net Income Continuous Operations                     325620000000.0  
Tax Provision                                        111980000000.0  
Pretax Income                                        437600000000.0  
Other Non Operating Income Expenses                     570000000.0  
Special Income Charges                               -13060000000.0  
Other Special Charges                                 11050000000.0  
Write Off                                              2010000000.0  
Net Non Operating Interest Income Expense             18670000000.0  
Interest Expense Non Operating                         6370000000.0  
Interest Income Non Operating                         25040000000.0  
Operating Income                                     426820000000.0  
Operating Expense                                    346200000000.0  
Other Operating Expenses                              90440000000.0  
Depreciation And Amortization In Income Statement     40650000000.0  
Depreciation Income Statement                         40650000000.0  
Selling General And Administration                   151100000000.0  
General And Administrative Expense                   151100000000.0  
Gross Profit                                         773020000000.0  
Cost Of Revenue                                      868750000000.0  
Total Revenue                                       1641770000000.0  
Operating Revenue                                   1641770000000.0
```

## Plotting with Matplotlib

```python
import matplotlib.pyplot as plt

df = yf.download("HDFCBANK.NS", period="1y", interval="1d", auto_adjust=True)
df['Close'].plot(title='HDFC Bank - 1 Year Chart')
plt.show()
```

![](https://i0.wp.com/www.marketcalls.in/wp-content/uploads/2025/03/image-76.png?resize=560%2C438&ssl=1)

## Is yfinance Production-Ready?

| Use Case | Suitable? | Notes |
| --- | --- | --- |
| Learning | ✅ Yes | Perfect for beginners |
| Prototyping | ✅ Yes | Strategy design, backtesting |
| Real-time Trading | ❌ No | Not built for live trading |
| Commercial Apps | ❌ No | TOS restricts this |

## Final Thoughts

This tutorial gives you a **strong foundation** to work with both **US and Indian stock data** using `yfinance`. From downloading historical OHLCV data to exploring options and financials, you’re now equipped to start building your own analytics, backtests, or visualizations.

Creator of OpenAlgo - OpenSource Algo Trading framework for Indian Traders. Building GenAI Applications. Telecom Engineer turned Full-time Derivative Trader. Mostly Trading Nifty, Banknifty, High Liquid Stock Derivatives. Trading the Markets Since 2006 onwards. Using Market Profile and Orderflow for more than a decade. Designed and published 100+ open source trading systems on various trading tools. Strongly believe that market understanding and robust trading frameworks are the key to the trading success. Building Algo Platforms, Writing about Markets, Trading System Design, Market Sentiment, Trading Softwares & Trading Nuances since 2007 onwards. Author of Marketcalls.in