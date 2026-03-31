# Data Preparation


```python
import numpy as np
import pandas as pd
import duckdb
import logging
```


```python
# Setup logger

logging.basicConfig(
    filename='./logs/data_prep.log',
    level=logging.INFO, 
    format='%(asctime)s - %(levelname)s - %(message)s',
    filemode='w'
)
logger = logging.getLogger(__name__)
```


```python
# Load data
companies = pd.read_csv("./data/source/sp500_companies.csv")
esg = pd.read_csv("./data/source/sp500_esg_data.csv")
index = pd.read_csv("./data/source/sap500.csv")
stocks = pd.read_csv("./data/source/sp500_stocks.csv")
constituents = pd.read_csv("./data/source/sp500_constituents.csv")

logger.info("Loaded raw data")
```


```python
# Data Cleaning
# Remove Columns from Companies
companies = companies.drop(['Currentprice', 'Marketcap'], axis=1)

logger.info("Data cleaning complete")
```


```python
# Add Primary Keys to tables

# Add Constituent ID to constituents
constituents['ConstituentID'] = range(1000, 1000 + len(constituents))

# Add ESG ID to esg
esg['esgID'] = range(1000, 1000 + len(esg)) 

# Add Ticker Value ID to stocks
stocks['TickerID'] = range(1000000, 1000000 + len(stocks)) 

logger.info("Added primary keys to tables")

```


```python
# Reorder tables to move primary keys to front

companies = companies[['Symbol'] + [c for c in companies.columns if c != 'Symbol']]
esg = esg[['esgID'] + [c for c in esg.columns if c != 'esgID']]
index = index[['Date'] + [c for c in index.columns if c != 'Date']]
stocks = stocks[['TickerID'] + [c for c in stocks.columns if c != 'TickerID']]
constituents = constituents[['ConstituentID'] + [c for c in constituents.columns if c != 'ConstituentID']]

logger.info("Set primary keys as first column")
```


```python
# Load files into DuckDB

try:

    # Establish DuckDB connection
    con = duckdb.connect(database='./sp500.duckdb', read_only=False)
    logger.info("Connected to DuckDB instance")

    # Add tables to database
    con.execute(f"""
        CREATE OR REPLACE TABLE companies AS
        SELECT * FROM companies;
    """)

    con.execute(f"""
        CREATE OR REPLACE TABLE esg AS
        SELECT * FROM esg;
    """)

    con.execute(f"""
        CREATE OR REPLACE TABLE index AS
        SELECT * FROM index;
    """)

    con.execute(f"""
        CREATE OR REPLACE TABLE stocks AS
        SELECT * FROM stocks;
    """)

    con.execute(f"""
        CREATE OR REPLACE TABLE constituents AS
        SELECT * FROM constituents;
    """)

    logger.info("Added tables to DuckDB")

    # Close DuckDB connection
    con.close()
    logger.info("Closed DuckDB connection")

except Exception as e:
    print(f"An error occurred: {e}")
    logger.error(f"An error occurred: {e}")
```


```python
# Save files to csv

companies.to_csv('./data/csv_out/sp500_companies.csv', index=False)
esg.to_csv('./data/csv_out/sp500_esg.csv', index=False)
index.to_csv('./data/csv_out/sp500_index.csv', index=False)
stocks.to_csv('./data/csv_out/sp500_stocks.csv', index=False)
constituents.to_csv('./data/csv_out/sp500_constituents.csv', index=False)

logger.info("Save tables as csv files")
```


```python
# Save files to parquet

companies.to_parquet('./data/parquet_out/sp500_companies.parquet')
esg.to_parquet('./data/parquet_out/sp500_esg.parquet')
index.to_parquet('./data/parquet_out/sp500_index.parquet')
stocks.to_parquet('./data/parquet_out/sp500_stocks.parquet')
constituents.to_parquet('./data/parquet_out/sp500_constituents.parquet')

logger.info("Save tables as parquet files")
```
