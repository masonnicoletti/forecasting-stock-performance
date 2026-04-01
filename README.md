# DS 4320 Project 1: Analyzing Stock Performance

## Executive Summary

The contents of this repository aim to explore stock performance across S&P 500 companies. This project started by integrating tables from various sources of data about S&P 500 constituents, historical pricing data, company characteristics, ESG metrics, and S&P 500 index performance into a structured relational database. The analysis focuses on evaluating differences in average daily returns across companies based on various features. Using data preprocessing, SQL querying and DuckDB, and Random Forest machine learning, the project identifies key factors associated with stock performance. Visualizations are included in the graphics folder, and a press release was composed to communicate insights to investors. Overall, this repository outlines a framework for analyzing the performance of S&P 500 stocks, and it offers the potential to continuously update and scale the database and conduct further analysis.

---

| Spec | Value |
|------|-------|
| Name | Mason Nicoletti |
| NetID | cxx6sw |
| DOI | [![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.19343949.svg)](https://doi.org/10.5281/zenodo.19343949) |
| Press Release | [Headline](https://github.com/masonnicoletti/forecasting-stock-performance/blob/main/press_release.md) |
| Data | [OneDrive Data Folder](https://myuva-my.sharepoint.com/:f:/g/personal/cxx6sw_virginia_edu/IgDYx1fsWe9ERbTOqEWXDnzSAVI8Miv0-l0skVUzWQmi-Vk?e=LVSwOI) |
| Pipeline | **Pipeline Files** <br> 1. [Data Prep](https://github.com/masonnicoletti/forecasting-stock-performance/blob/main/pipeline/data_prep.ipynb) <br> 2. [Data Analysis](https://github.com/masonnicoletti/forecasting-stock-performance/blob/main/pipeline/data_analysis.ipynb) |
| License | [MIT License](https://github.com/masonnicoletti/forecasting-stock-performance/blob/main/LICENSE) |

---

## Problem Definition

### Problem Statement

**General Problem:** Forecasting stock prices

**Specific Problem:** Analyzing trends and differences in stock performance across S&P 500 companies based on historical pricing, company characteristics, and ESG scores.

### Rationale

The original problem of forecasting stock prices was refined to better align with the structure of the data and the type of analysis being conducted. Stock price prediction requires strong time-series data, which was not acquired for this dataset. Instead, the refined problem focuses on evaluating trends and differences in stock performance across S&P 500 companies using historical data. This reframing allows for a clearer definition of the factors included in the analysis, specifically three categories of data: 1) historical pricing data, summarized into metrics such as average daily return and volatility, 2) company characteristics, and 3) ESG scores, representing environmental, social, and governance factors. By shifting the focus to explanation from prediction, the analysis is able to more effectively identify relationships between these variables and stock performance. This also establishes a more appropriate target variable for meaningful comparison across companies without assuming things about future market conditions.

### Motivation

Despite the vast amount of financial data available, it remains difficult for investors to clearly understand what drives stock performance. This challenge is further complicated by the growing importance of non-financial factors, such as ESG scores and company politics, which are increasingly shaping investment strategies. The motivation for this project is rooted in providing a more structured and accessible way to analyze stock performance by combining financial data, company attributes, and sustainability metrics. By identifying the key factors associated with higher or lower returns across S&P 500 companies, this analysis aims to offer insights that can support more informed investment decisions. Additionally, understanding how ESG factors relate to performance may help investors balance financial goals while also considering sustainability and risk.

### Press Release

[Investor Insights - Stock Performance by Industry](https://github.com/masonnicoletti/forecasting-stock-performance/blob/main/press_release.md)

## Domain Exposition

### Terminology

**Table 1.** Domain Terminology

| Term | Description |
|------|-------------|
| Asset | Economic value resultin from stock ownership |
| EBITDA | Earnings Before Interest, Taxes, Depreciation, and Amortization; indicator of company profitability |
| ESG Score | Performance metric used to assess company sustainability (Evironmental, Social, Governance) |
| GICS Sector | Framework that categorizes companies into 11 main economic sectors |
| Large-cap Stocks | Shares of a company with over $10 Billion market cap |
| Market Cap | Total value of a company's aggregated stock shares |
| Price | The current value of one share of a stock |
| Revenue Growth | Percent increase in company's revenue; key indicator of stock performance |
| S&P 500 | Stock market index tracking performance of the 500 leading US companies |
| Stocks | Securities representing partial ownership of a publicly traded company |
| Stock Exchange | Market where stocks are bought and sold | 
| Stock Market | Trading network that allows investors to buy and sell stocks |
| Symbol | Unique company abbreviation for stock market identification | 
| Volatility | Measure of rapidity of price changes for the shares of a company |
| Volume | Total number of shares traded in a single day during market hours |
| Weight | Percentage of S&P 500 index comprised by an individual company |

### Domain Description

This project lives in the financial markets domain. It centers around the stock market, which is a system where shares of publicly traded companies are bought and sold, reflecting the collective expectations of investors about future performance. The project focuses specifically on the S&P 500, an index that tracks large-cap U.S. companies and is commonly used as an indicator of overall market performance. Proficiency in this domain requires an understanding of financial metrics, market dynamics, and the factors that influence stock prices, including company fundamentals, economic conditions, and investor behavior. Additionally, this project incorporates ESG data, which captures environmental, social, and governance factors that may influence long-term company performance beyond traditional financial indicators. Lastly, this project involves elements of prediction and modeling, where historical data is used to identify patterns and relationships that may inform future market behavior. Knowledge of how financial systems operate, along with an awareness of their limitations and uncertainties, is essential in order to apply these findings effectively in investment-related decision making.

### Background Reading

**Background Reading**: [Domain Exposition Folder](https://myuva-my.sharepoint.com/:f:/g/personal/cxx6sw_virginia_edu/IgDwP3hVgnHnSZBozs8XP3kGAVH3vmF9mlVqJhP_UJrmWJA?e=CAtYpN)

**Table 2.** Summary of Background Readings

| Title | Description | Link |
|-------|-------------|------|
| Stock Market 101 | Outlines the purpose, function, and various aspects about stocks and the stock market | [Stock Market 101](https://myuva-my.sharepoint.com/:u:/g/personal/cxx6sw_virginia_edu/IQDxv4aEnPetTJS2Xi20sDl3AarR_9lwIMgJ1X_gnLhU3cQ?e=bmeB95) |
| S&P 500 | Describes the scale and significance of the S&P 500 as a Gauge of the U.S. Large-Cap Market | [S&P 500](https://myuva-my.sharepoint.com/:u:/g/personal/cxx6sw_virginia_edu/IQAzC_xXktAhR6WB2I5-Y4qlAVXtiT12rkDRfH3N8nbUyTQ?e=c6QT0K) |
| S&P 500 Index Stocks List | Displays data about all 503 constituents of the S&P index, ordered by market cap | [S&P 500 Index Stocks List](https://myuva-my.sharepoint.com/:u:/g/personal/cxx6sw_virginia_edu/IQAoeBbE0o4hR5fkrIVAjINfAXsGysJIqoeVQ4EBaAcFO0c?e=sdSJuY) |
| ESG Scores and Raw Data | Describes ESG scores and approaches for market sustainability | [ESG Scores and Raw Data](https://myuva-my.sharepoint.com/:u:/g/personal/cxx6sw_virginia_edu/IQDNAT_730UsTYusXmbAnlAfAcAfpK96wLu1pveyzGFuiGM?e=eVxjsf) |
| Stock Market Prediction | Defines stock market prediction and explaines methods and key indicators that reveal market performance | [Stock Market Prediction](https://myuva-my.sharepoint.com/:u:/g/personal/cxx6sw_virginia_edu/IQAoDXwn_ttuRbjK8hEHK8lnAfatEYJfDsEGF0RSuQUCQYU?e=HPgw6R) |
| How effective is machine learning in stock market predictions? | Study aimed an analyzing prediction algorithm performance for stock market prediction | [How effective is machine learning in stock market predictions?](https://myuva-my.sharepoint.com/:u:/g/personal/cxx6sw_virginia_edu/IQC0XOivUWIjSooMEYl2TAYrAeMedD9fpIjIQDme8c3I_ZE?e=aulZNf) |


## Data Creation

### Provenance

The raw data used in this analysis was downloaded from several online sources.

1) The S&P 500 Companies table was acquired from a Kaggle page called "*S&P 500 Stocks (daily updated)*". The owner of this page is named Larxel, and the orginial data was collected from FRED and yfinance. Additionally, both the S&P 500 Index table and the S&P 500 Stocks table were downloaded from this same Kaggle page, each as an individual csv file.
   
2) The S&P 500 ESG Data table was acquired from a Kaggle page called "*S&P 500 ESG and Stocks Data 2023-24*". This page is owned by Rikin Zala, and it contains two separate files. The S&P 500 ESG Data csv file was downloaded for this analysis, while the S&P 500 Price Data csv file was not.
   
3) The S&P 500 Constituents table was acquired from a platform called DataHub, specifically from a page called "*S&P 500 Companies with Financial Information*". An available table of all S&P 500 constituents was downloaded as a csv file. The data on this page was originally aquired from the Standard and Poor's website, and it is updated regularly to reflect the current state of the index.

The five tables from these three sources were organized locally to support further data preparation.

### Code

**Table 3.** Code used for Data Creation

| Code | Description | Link |
|------|-------------|------|
| `data_prep.ipynb` | Loads in the raw data, performs transformations to establish relational model, uploads tables to DuckDB, and saves output files | [Link to data prep code](https://github.com/masonnicoletti/forecasting-stock-performance/blob/main/pipeline/data_prep.ipynb)|

### Bias Identification

The S&P 500 stock dataset is susceptible to bias as it is constructed from companies that are selected for inclusion in the S&P 500 index, which represents only large-cap U.S. equities. This selection process introduces bias, as it does not capture small-cap or international companies, and therefore is not representative of the broader global equity market. As a result, any analysis performed using this dataset may not generalize to companies outside of the index. Additionally, survivorship bias may be present, as datasets that rely on current constituents may exclude companies that were previously part of the index but have since been removed due to poor performance or other factors. Furthermore, ESG metrics are derived from third-party rating agencies, which may introduce methodological bias due to differences in scoring criteria, weighting, and data availability across firms.

### Bias Mitigation

Bias within the S&P 500 dataset can be mitigated through the use of historical constituent data and careful integration of multiple data sources. By incorporating a time-varying constituents table, the dataset can account for changes in index membership over time, reducing the impact of survivorship bias and allowing for more accurate longitudinal analysis. Additionally, combining stock price data with index-level data provides a more complete view of market behavior, improving the robustness of analytical conclusions. ESG-related bias can be addressed by acknowledging differences in rating methodologies and, where possible, comparing scores across multiple providers or normalizing values within sectors. Finally, the large scale and high frequency of financial market data helps to reduce random variability, enabling more stable estimates and more reliable modeling outcomes when appropriate statistical methods are applied.

### Rationale

The construction of the S&P 500 relational model required selecting from multiple available sources of company, stock price, and index data collected over different time spans. This presented challenges, as the data with shorter time windows sometimes omitted important market cycles, while available data with longer windows did not always have data nearest to the present and may introduce inconsistencies due to changes in the S&P 500 constituents. In this project, a longer time horizon that includes near-present data was selected in order to balance historical depth with relevance to current market conditions. This decision allowed for more robust time-series analysis and improved the ability to capture trends across different economic periods, while acknowledging that data consistency may vary across the full time span.

Another important design decision involved structuring the relational model, particularly the use of primary keys and temporal alignment across tables. The `Date` variable was chosen as the primary key for the S&P 500 Index table, which also allows it to function as a natural connection point to other time-based tables such as stock prices and constituents. Additional primary keys were created in the S&P 500 ESG Data, S&P 500 Stocks, and S&P 500 Constituents tables to maintain integrity and support efficient joins. These decisions ensured that the dataset adheres to relational modeling principles. However, they also introduce considerations around data alignment, as mismatches in trading days or missing values across tables may affect downstream analysis if not handled carefully.

Finally, the analysis was limited to S&P 500 companies in order to capture a meaningful representation of the broader U.S. stock market. ESG data was incorporated to extend analysis beyond financial performance and include sustainability-related factors, though this introduces additional variability due to scoring methodologies. In data cleaning, features that reflected the current state of a company, such as real-time metrics, were removed from static tables. These decisions were made to create a database that adheres to the rules of the relational model and will be useful for analytics in forecasting stock performance based on a variety of factors.

## Metadata

### Schema

**Figure 1.** ERD of S&P 500 Model

![ERD of S&P 500 Model](./graphics/sp500_erd.drawio.png)

### Data

**Table 4.** Data Tables

| Table | Description | Link |
|-------|-------------|------|
| Companies | Information about S&P 500 companies | [Link to sp500_companies.csv](https://myuva-my.sharepoint.com/:x:/r/personal/cxx6sw_virginia_edu/Documents/DS%204320%20Project%201/Data/csv/sp500_companies.csv?d=w018aa33171a64dafbb973df86d6e91a2&csf=1&web=1&e=gq4rqD) |
| ESG Data | ESG sustainability scores for S&P 500 companies | [Link to sp500_esg.csv](https://myuva-my.sharepoint.com/:x:/r/personal/cxx6sw_virginia_edu/Documents/DS%204320%20Project%201/Data/csv/sp500_esg.csv?d=w262ccc38a5e64f2f83ea3774310c3497&csf=1&web=1&e=Cd2t6G) |
| Stocks | Daily records (2014-2024) for S&P 500 companies | [Link to sp500_stocks.csv](https://myuva-my.sharepoint.com/:x:/r/personal/cxx6sw_virginia_edu/Documents/DS%204320%20Project%201/Data/csv/sp500_stocks.csv?d=w248e8eecd649454cbd0b90311f28ef51&csf=1&web=1&e=q2Whc7) |
| S&P Index | Daily record (1927-2026) of S&P 500 index | [Link to sp500_index.csv](https://myuva-my.sharepoint.com/:x:/r/personal/cxx6sw_virginia_edu/Documents/DS%204320%20Project%201/Data/csv/sp500_index.csv?d=w8bc98b055a494b8db279e87d73fda3e2&csf=1&web=1&e=hsryck) |
| Constituents | Additional information about S&P 500 companies | [Link to sp500_constituents.csv](https://myuva-my.sharepoint.com/:x:/r/personal/cxx6sw_virginia_edu/Documents/DS%204320%20Project%201/Data/csv/sp500_constituents.csv?d=w62325a5e97b748c08e2a6f08e0217165&csf=1&web=1&e=giOnBK) |

### Data Dictionary

**Table 5.** Companies Data Dictionary

| Name | Data Type | Description | Example | Uncertainty |
|----|----|----|----|----|
| Symbol | String | Company ticker symbol as seen on stock exchange | AAPL | |
| Exchange | String | Marketplace where financial securities are traded | NMS | |
| Short Name | String | Short name of company | Apple Inc. | |
| Long Name | String | Full name of company | Apple Inc, | |
| Sector | String | Economic segment of company | Technology | |
| Industry | String | Specific category within sector | Consumer Electrics | |
| EDITDA | Float | Earnings before interest, taxes, depreciation, and amortization | 134660997120 | EDITDA ± 1.96 * √(EDITDA) |
| Revenue Growth | Float | Percent increase in company sales | 0.061 | Revenue Growth ± 1.96 * √(|Revenue Growth|) |
| City | String | City where company operations are headquartered | Cupertino | |
| State | String | State where company operations are headquartered | CA | |
| Country | String | Country where company operations are headquartered | United States | |
| Full Time Employees | Float | Number of full-time employees at company | 164000 | Full Time Employees ± round(1.96 * √(Full Time Employees)) |
| Long Business Summary | String | Information about company | Apple Inc. designs, manufactures, and markets smartphones ... | |
| Weight | Float | Percentage of S&P 500 Index staked in company | 0.06920915243972749 | Weight ± 1.96 * √(Weight) |

**Table 6.** ESG Data Dictionary

| Name | Data Type | Description | Example | Uncertainty |
|----|----|----|----|----|
| ESG ID | Int | Unique identifier for ESG record | 1002 | |
| Symbol | String | Company ticker symbol as seen on stock exchange | AAPL | |
| Full Name | String | Full name of company associated with ESG data | Apple Inc. | |
| GICS Sector | String | Economic sector classification based on Global Industry Classification Standard | Information Technology | |
| GICS Sub-Industry | String | More specific classification within GICS sector | Technology Hardware, Storage & Peripherals | |
| Environment Score | Float | Score measuring company environmental impact and sustainability practices | 0.46 | Environment Score ± 1.96 * √(Environment Score) |
| Social Score | Float | Score measuring company relationships with employees, customers, and communities | 7.39 | Social Score ± 1.96 * √(Social Score) |
| Governance Score | Float | Score measuring company leadership, audits, and shareholder rights | 9.37 | Governance Score ± 1.96 * √(Governance Score) |
| Total ESG | Float | Aggregate ESG score combining environmental, social, and governance factors | 17.22 | Total ESG ± 1.96 * √(Total ESG) |
| Highest Controversy | Int | Indicator of most severe ESG-related controversy involving the company | 3 | Highest Controversy ± 1 |
| Percentile | Float | Percentile ranking of company ESG score relative to peers | 17.82 | Percentile ± RANK(1.96 * √(Percentile)) |
| Rating Year | Int | Year ESG rating was assigned | 2023 | |
| Rating Month | Int | Month ESG rating was assigned | 9 | |
| Market Cap | Float | Total market value of company's outstanding shares | 3296096681984 | Market Cap ± round(1.96 * √(Market Cap)) |
| Beta | Float | Measure of stock volatility relative to overall market | 1.240 | Beta ± 1.96 * √(Beta) |
| Overall Risk | Int | Composite risk score derived from ESG factors | 1 | |Overall Risk ± 2| |

**Table 7.** Stocks Data Dictionary

| Name | Data Type | Description | Example | Uncertainty |
|----|----|----|----|----|
| Ticker ID | Int | Unique identifier for stock price record | 1009709 | |
| Date | Date | Trading date for stock price data | 2018-08-21 | |
| Symbol | String | Company ticker symbol as seen on stock exchange | ABT | |
| Adjusted Close | Float | Closing price adjusted for dividends and stock splits | 58.136356353759766 | Adjusted Close ± (1.96 * √(Adjusted Close)) / 10 |
| Close | Float | Final trading price of stock at market close | 64.75  | Close ± (1.96 * √(Close)) / 10 |
| High | Float | Highest trading price of stock during the day | 65.06999969482422 | High ± (1.96 * √(High)) / 10 |
| Low | Float | Lowest trading price of stock during the day | 64.5 | Low ± (1.96 * √(Low)) / 10 |
| Open | Float | Opening trading price of stock at market open | 64.91000366210938 | Open ± (1.96 * √(Open)) / 10 |
| Volume | Int | Number of shares traded during the day | 4045500 | Volume ± 1.96 * √(Volume) |

**Table 8.** S&P 500 Index Data Dictionary

| Name | Data Type | Description | Example | Uncertainty |
|----|----|----|----|----|
| Date | Date | Trading date for S&P 500 index data | 2018-08-21 | |
| Open | Float | Opening value of the S&P 500 index at market open | 2861.510009765625 | Open ± (1.96 * √(Open)) / 10 |
| High | Float | Highest value of the S&P 500 index during the trading day | 2873.22998046875 | High ± (1.96 * √(High)) / 10 |
| Low | Float | Lowest value of the S&P 500 index during the trading day | 2861.320068359375 | Low ± (1.96 * √(Low)) / 10 |
| Close | Float | Final value of the S&P 500 index at market close | 2862.9599609375 | Close ± (1.96 * √(Close)) / 10 |
| Volume | Int | Total trading volume of all index constituents during the day | 3174010000 | Volume ± 1.96 * √(Volume) |

**Table 9.** Constituents Data Dictionary

| Name | Data Type | Description | Example | Uncertainty |
|----|----|----|----|----|
| Constituent ID | Int | Unique identifier for S&P 500 constituent record | 1038 | |
| Symbol | String | Company ticker symbol as seen on stock exchange | AAPL | |
| Security | String | Name of the company included in the S&P 500 index | Apple Inc. | |
| GICS Sector | String | Economic sector classification based on Global Industry Classification Standard | Information Technology | |
| GICS Sub-Industry | String | More specific classification within GICS sector | Technology Hardware, Storage & Peripherals | |
| Headquarters Location | String | City and state where company headquarters are located | Cupertino, California | |
| Date Added | Date | Date the company was added to the S&P 500 index | 1982-11-30 | |
| CIK | Int | Central Index Key assigned by the SEC to identify the company | 320193 | |
| Founded | Int | Year the company was founded | 1977 | |
