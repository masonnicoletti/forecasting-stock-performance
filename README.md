# DS 4320 Project 1: Forecasting Stock Performance

## Executive Summary

---

| Spec | Value |
|------|-------|
| Name | Mason Nicoletti |
| NetID | cxx6sw |
| DOI | [DOI Link]() |
| Press Release | [Headline]() |
| Data | [OneDrive Data Folder](https://myuva-my.sharepoint.com/:f:/r/personal/cxx6sw_virginia_edu/Documents/DS%204320%20Project%201/Data?csf=1&web=1&e=xrsKd2) |
| Pipeline | [Pipeline Files]() |
| License | [License]() |

---

## Problem Definition


## Domain Exposition


## Data Creation


## Metadata

### Schema

**Figure 1.** ERD of S&P 500 Model

![ERD of S&P 500 Model](./graphics/sp500_erd.drawio.png)

### Data

**Table 2.** Data tables

| Table | Description | Link |
|-------|-------------|------|
| Companies | Information about S&P 500 companies | [Link to sp500_companies.csv](https://myuva-my.sharepoint.com/:x:/r/personal/cxx6sw_virginia_edu/Documents/DS%204320%20Project%201/Data/csv/sp500_companies.csv?d=w018aa33171a64dafbb973df86d6e91a2&csf=1&web=1&e=gq4rqD) |
| ESG Data | ESG sustainability scores for S&P 500 companies | [Link to sp500_esg.csv](https://myuva-my.sharepoint.com/:x:/r/personal/cxx6sw_virginia_edu/Documents/DS%204320%20Project%201/Data/csv/sp500_esg.csv?d=w262ccc38a5e64f2f83ea3774310c3497&csf=1&web=1&e=Cd2t6G) |
| Stocks | Daily records (2014-2024) for S&P 500 companies | [Link to sp500_stocks.csv](https://myuva-my.sharepoint.com/:x:/r/personal/cxx6sw_virginia_edu/Documents/DS%204320%20Project%201/Data/csv/sp500_stocks.csv?d=w248e8eecd649454cbd0b90311f28ef51&csf=1&web=1&e=q2Whc7) |
| S&P Index | Daily record (1927-2026) of S&P 500 index | [Link to sp500_index.csv](https://myuva-my.sharepoint.com/:x:/r/personal/cxx6sw_virginia_edu/Documents/DS%204320%20Project%201/Data/csv/sp500_index.csv?d=w8bc98b055a494b8db279e87d73fda3e2&csf=1&web=1&e=hsryck) |
| Constituents | Additional information about S&P 500 companies | [Link to sp500_constituents.csv](https://myuva-my.sharepoint.com/:x:/r/personal/cxx6sw_virginia_edu/Documents/DS%204320%20Project%201/Data/csv/sp500_constituents.csv?d=w62325a5e97b748c08e2a6f08e0217165&csf=1&web=1&e=giOnBK) |

### Data Dictionary

**Table 3.** Companies Data Dictionary

| Name | Data Type | Description | Example | Uncertainty |
|----|----|----|----|----|
| Symbol | String | Company ticker symbol as seen on stock exchange | AAPL | |
| Exchange | String | Marketplace where financial securities are traded | NMS | |
| Short Name | String | Short name of company | Apple Inc. | |
| Long Name | String | Full name of company | Apple Inc, | |
| Sector | String | Economic segment of company | Technology | |
| Industry | String | Specific category within sector | Consumer Electrics | |
| EDITDA | Float | Earnings before interest, taxes, depreciation, and amortization | 134660997120 | EDITDA ± 1.96 * √(EDITDA) |
| Revenue Growth | Float | Percent increase in company sales | 0.061 | Revenue Growth ± 1.96 * √(Revent Growth) |
| City | String | City where company operations are headquartered | Cupertino | |
| State | String | State where company operations are headquartered | CA | |
| Country | String | Country where company operations are headquartered | United States | |
| Full Time Employees | Float | Number of full-time employees at company | 164000 | Full Time Employees ± round(1.96 * √(Full Time Employees)) |
| Long Business Summary | String | Information about company | Apple Inc. designs, manufactures, and markets smartphones ... | |
| Weight | Float | Percentage of S&P 500 Index staked in company | 0.06920915243972749 | Weight ± 1.96 * √(Weight) |

**Table 4.** ESG Data Dictionary

| Name | Data Type | Description | Example | Uncertainty |
|----|----|----|----|----|
| ESG ID | Int | Unique identifier for ESG record | 1002 | |
| Symbol | String | Company ticker symbol as seen on stock exchange | AAPL | |
| Full Name | String | Full name of company associated with ESG data | Apple Inc. | |
| GICS Sector | String | Economic sector classification based on Global Industry Classification Standard | Information Technology | |
| GICS Sub-Industry | String | More specific classification within GICS sector | Technology Hardware, Storage & Peripherals | |
| Environment Score | Float | Score measuring company environmental impact and sustainability practices | 0.46 | Mean ± 95% CI (based on standard error) |
| Social Score | Float | Score measuring company relationships with employees, customers, and communities | 7.39 | Mean ± 95% CI (based on standard error) |
| Governance Score | Float | Score measuring company leadership, audits, and shareholder rights | 9.37 | Mean ± 95% CI (based on standard error) |
| Total ESG | Float | Aggregate ESG score combining environmental, social, and governance factors | 17.22 | Mean ± 95% CI (based on standard error) |
| Highest Controversy | Int | Indicator of most severe ESG-related controversy involving the company | 3 | Proportion ± standard error |
| Percentile | Float | Percentile ranking of company ESG score relative to peers | 17.82 | Mean ± 95% CI (based on standard error) |
| Rating Year | Int | Year ESG rating was assigned | 2023 | |
| Rating Month | Int | Month ESG rating was assigned | 9 | |
| Market Cap | Float | Total market value of company's outstanding shares | 3296096681984 | Mean ± 95% CI (based on standard error) |
| Beta | Float | Measure of stock volatility relative to overall market | 1.240 | Mean ± 95% CI (based on standard error) |
| Overall Risk | Int | Composite risk score derived from ESG factors | 1 | Mean ± 95% CI (based on standard error) |

**Table 5.** Stocks Data Dictionary

| Name | Data Type | Description | Example | Uncertainty |
|----|----|----|----|----|
| Ticker ID | Int | Unique identifier for stock price record | 1009709 | |
| Date | Date | Trading date for stock price data | 2018-08-21 | |
| Symbol | String | Company ticker symbol as seen on stock exchange | ABT | |
| Adjusted Close | Float | Closing price adjusted for dividends and stock splits | 58.136356353759766 | Mean ± 95% CI (based on standard error) |
| Close | Float | Final trading price of stock at market close | 64.75  | Mean ± 95% CI (based on standard error) |
| High | Float | Highest trading price of stock during the day | 65.06999969482422 | Mean ± 95% CI (based on standard error) |
| Low | Float | Lowest trading price of stock during the day | 64.5 | Mean ± 95% CI (based on standard error) |
| Open | Float | Opening trading price of stock at market open | 64.91000366210938 | Mean ± 95% CI (based on standard error) |
| Volume | Int | Number of shares traded during the day | 4045500 | Mean ± 95% CI (based on standard error) |

**Table 6.** S&P 500 Index Data Dictionary

| Name | Data Type | Description | Example | Uncertainty |
|----|----|----|----|----|
| Date | Date | Trading date for S&P 500 index data | 2018-08-21 | |
| Open | Float | Opening value of the S&P 500 index at market open | 2861.510009765625 | Mean ± 95% CI (based on standard error) |
| High | Float | Highest value of the S&P 500 index during the trading day | 2873.22998046875 | Mean ± 95% CI (based on standard error) |
| Low | Float | Lowest value of the S&P 500 index during the trading day | 2861.320068359375 | Mean ± 95% CI (based on standard error) |
| Close | Float | Final value of the S&P 500 index at market close | 2862.9599609375 | Mean ± 95% CI (based on standard error) |
| Volume | Int | Total trading volume of all index constituents during the day | 3174010000 | Mean ± 95% CI (based on standard error) |

**Table 5.** Constituents Data Dictionary

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