# Data Analysis


```python
import duckdb
import logging
import numpy as np
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt

from sklearn.model_selection import train_test_split, GridSearchCV
from sklearn.ensemble import RandomForestRegressor
from sklearn.metrics import r2_score, mean_squared_error
```


```python
# Setup logger

logging.basicConfig(
    filename='./logs/data_analysis.log',
    level=logging.INFO, 
    format='%(asctime)s - %(levelname)s - %(message)s',
    filemode='w'
)
logger = logging.getLogger(__name__)
```

## Database Querying


```python
# Query the database

try:
    
    # Establish DuckDB connection
    con = duckdb.connect(database='./sp500.duckdb', read_only=False)
    logger.info("Connected to DuckDB instance")

    # Query the sp500 Database
    company_table = con.execute("""
        SELECT
            c.Symbol, c.Shortname, c.Sector, c.Industry,
            e.totalEsg, e.environmentScore, e.socialScore, e.governanceScore, e.percentile, e.beta, e.highestControversy, e.overallRisk,
            AVG(s.Open) AS avg_open, AVG(s.Close) AS avg_close, AVG(s.High) AS avg_high, AVG(s.Low) AS avg_low, AVG(s.Volume) AS avg_volume, AVG((s.Close - s.Open) / s.Open) AS avg_daily_return, STDDEV(s.Close) AS price_volatility
        FROM companies c
        LEFT JOIN esg e
            ON c.Symbol = e.Symbol
        LEFT JOIN stocks s
            ON c.Symbol = s.Symbol
        INNER JOIN constituents cn
            ON c.Symbol = cn.Symbol
        GROUP BY
            c.Symbol, c.Shortname, c.Sector, c.Industry,
            e.totalEsg, e.environmentScore, e.socialScore, e.governanceScore, e.percentile, e.beta, e.highestControversy, e.overallRisk
        ORDER BY avg_close DESC;
    """).df()
    logger.info("Query complete")

    # Close DuckDB connection
    con.close()
    logger.info("Closed DuckDB connection")

except Exception as e:
    print(f"An error occurred: {e}")
    logger.error(f"An error occurred: {e}")
```


```python
company_table.head()

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Symbol</th>
      <th>Shortname</th>
      <th>Sector</th>
      <th>Industry</th>
      <th>totalEsg</th>
      <th>environmentScore</th>
      <th>socialScore</th>
      <th>governanceScore</th>
      <th>percentile</th>
      <th>beta</th>
      <th>highestControversy</th>
      <th>overallRisk</th>
      <th>avg_open</th>
      <th>avg_close</th>
      <th>avg_high</th>
      <th>avg_low</th>
      <th>avg_volume</th>
      <th>avg_daily_return</th>
      <th>price_volatility</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>MTD</td>
      <td>Mettler-Toledo International, I</td>
      <td>Healthcare</td>
      <td>Diagnostics &amp; Research</td>
      <td>13.10</td>
      <td>1.00</td>
      <td>7.41</td>
      <td>4.69</td>
      <td>6.93</td>
      <td>1.139</td>
      <td>0.0</td>
      <td>5</td>
      <td>644.892789</td>
      <td>645.048911</td>
      <td>652.233365</td>
      <td>637.424992</td>
      <td>1.623200e+05</td>
      <td>0.000650</td>
      <td>469.949198</td>
    </tr>
    <tr>
      <th>1</th>
      <td>EQIX</td>
      <td>Equinix, Inc.</td>
      <td>Real Estate</td>
      <td>REIT - Specialty</td>
      <td>13.88</td>
      <td>3.44</td>
      <td>5.31</td>
      <td>5.13</td>
      <td>8.50</td>
      <td>0.700</td>
      <td>1.0</td>
      <td>5</td>
      <td>426.004031</td>
      <td>426.076441</td>
      <td>430.665350</td>
      <td>421.111144</td>
      <td>6.753230e+05</td>
      <td>0.000431</td>
      <td>254.737572</td>
    </tr>
    <tr>
      <th>2</th>
      <td>TDG</td>
      <td>Transdigm Group Incorporated</td>
      <td>Industrials</td>
      <td>Aerospace &amp; Defense</td>
      <td>38.72</td>
      <td>12.01</td>
      <td>17.71</td>
      <td>9.00</td>
      <td>90.45</td>
      <td>1.410</td>
      <td>2.0</td>
      <td>10</td>
      <td>394.221516</td>
      <td>394.298474</td>
      <td>398.558856</td>
      <td>389.727444</td>
      <td>4.060174e+05</td>
      <td>0.000629</td>
      <td>318.809429</td>
    </tr>
    <tr>
      <th>3</th>
      <td>NFLX</td>
      <td>Netflix, Inc.</td>
      <td>Communication Services</td>
      <td>Entertainment</td>
      <td>16.41</td>
      <td>0.09</td>
      <td>7.31</td>
      <td>9.01</td>
      <td>15.33</td>
      <td>1.262</td>
      <td>2.0</td>
      <td>9</td>
      <td>232.776050</td>
      <td>232.850524</td>
      <td>236.217270</td>
      <td>229.286021</td>
      <td>1.664855e+07</td>
      <td>0.000911</td>
      <td>210.464293</td>
    </tr>
    <tr>
      <th>4</th>
      <td>FDS</td>
      <td>FactSet Research Systems Inc.</td>
      <td>Financial Services</td>
      <td>Financial Data &amp; Stock Exchanges</td>
      <td>18.37</td>
      <td>4.32</td>
      <td>8.75</td>
      <td>5.30</td>
      <td>21.85</td>
      <td>0.748</td>
      <td>1.0</td>
      <td>6</td>
      <td>226.933893</td>
      <td>227.038344</td>
      <td>229.227513</td>
      <td>224.720395</td>
      <td>3.067631e+05</td>
      <td>0.000656</td>
      <td>129.810908</td>
    </tr>
  </tbody>
</table>
</div>




```python
company_table.isna().sum()
```




    Symbol                  0
    Shortname               0
    Sector                  0
    Industry                0
    totalEsg               69
    environmentScore       69
    socialScore            69
    governanceScore        69
    percentile             69
    beta                   69
    highestControversy     69
    overallRisk            69
    avg_open              310
    avg_close             310
    avg_high              310
    avg_low               310
    avg_volume            310
    avg_daily_return      310
    price_volatility      310
    dtype: int64




```python
# Data cleaning - drop NAs

company_table = company_table.dropna()

new_len = len(company_table)
print("Rows:", new_len)
```

    Rows: 142


The stocks table has lots of missing fields; the output company table has 66% NA values for the stock fields. Dropping the NAs hurts this analysis but does not prevent the development of a model. The cleaned table has 142 entries.

## Modeling


```python
# Data cleaning for modeling

# Drop unnecessary columns
company_table_modeling = company_table.drop(columns=["Symbol", "Shortname", "percentile"])

# Drop dummy variables
company_table_modeling = pd.get_dummies(company_table_modeling, columns=["Sector", "Industry"], drop_first=True)

# Separate features and target variable
X = company_table_modeling.drop("avg_daily_return", axis=1)
y = company_table_modeling["avg_daily_return"]

logger.info("Modeling preprocessing complete")
```


```python
# Train test split

X_train, X_test, y_train, y_test = train_test_split(X, y, train_size=0.7, random_state=45)

logger.info("Perform train/test split")
```


```python
# Initialize Random Forest object
rf = RandomForestRegressor(n_jobs=-1, random_state=45)

# Define parameters
params = {
    "n_estimators": [100, 200],
    "max_depth": [None, 10, 20],
    "min_samples_split": [2, 5]
}

# Set up Grid Search
grid = GridSearchCV(rf, params, cv=3, n_jobs=-1)
```


```python
# Fit the model 

grid.fit(X_train, y_train)

logger.info("Random Forest fitting complete")
```




<style>#sk-container-id-1 {
  /* Definition of color scheme common for light and dark mode */
  --sklearn-color-text: #000;
  --sklearn-color-text-muted: #666;
  --sklearn-color-line: gray;
  /* Definition of color scheme for unfitted estimators */
  --sklearn-color-unfitted-level-0: #fff5e6;
  --sklearn-color-unfitted-level-1: #f6e4d2;
  --sklearn-color-unfitted-level-2: #ffe0b3;
  --sklearn-color-unfitted-level-3: chocolate;
  /* Definition of color scheme for fitted estimators */
  --sklearn-color-fitted-level-0: #f0f8ff;
  --sklearn-color-fitted-level-1: #d4ebff;
  --sklearn-color-fitted-level-2: #b3dbfd;
  --sklearn-color-fitted-level-3: cornflowerblue;

  /* Specific color for light theme */
  --sklearn-color-text-on-default-background: var(--sg-text-color, var(--theme-code-foreground, var(--jp-content-font-color1, black)));
  --sklearn-color-background: var(--sg-background-color, var(--theme-background, var(--jp-layout-color0, white)));
  --sklearn-color-border-box: var(--sg-text-color, var(--theme-code-foreground, var(--jp-content-font-color1, black)));
  --sklearn-color-icon: #696969;

  @media (prefers-color-scheme: dark) {
    /* Redefinition of color scheme for dark theme */
    --sklearn-color-text-on-default-background: var(--sg-text-color, var(--theme-code-foreground, var(--jp-content-font-color1, white)));
    --sklearn-color-background: var(--sg-background-color, var(--theme-background, var(--jp-layout-color0, #111)));
    --sklearn-color-border-box: var(--sg-text-color, var(--theme-code-foreground, var(--jp-content-font-color1, white)));
    --sklearn-color-icon: #878787;
  }
}

#sk-container-id-1 {
  color: var(--sklearn-color-text);
}

#sk-container-id-1 pre {
  padding: 0;
}

#sk-container-id-1 input.sk-hidden--visually {
  border: 0;
  clip: rect(1px 1px 1px 1px);
  clip: rect(1px, 1px, 1px, 1px);
  height: 1px;
  margin: -1px;
  overflow: hidden;
  padding: 0;
  position: absolute;
  width: 1px;
}

#sk-container-id-1 div.sk-dashed-wrapped {
  border: 1px dashed var(--sklearn-color-line);
  margin: 0 0.4em 0.5em 0.4em;
  box-sizing: border-box;
  padding-bottom: 0.4em;
  background-color: var(--sklearn-color-background);
}

#sk-container-id-1 div.sk-container {
  /* jupyter's `normalize.less` sets `[hidden] { display: none; }`
     but bootstrap.min.css set `[hidden] { display: none !important; }`
     so we also need the `!important` here to be able to override the
     default hidden behavior on the sphinx rendered scikit-learn.org.
     See: https://github.com/scikit-learn/scikit-learn/issues/21755 */
  display: inline-block !important;
  position: relative;
}

#sk-container-id-1 div.sk-text-repr-fallback {
  display: none;
}

div.sk-parallel-item,
div.sk-serial,
div.sk-item {
  /* draw centered vertical line to link estimators */
  background-image: linear-gradient(var(--sklearn-color-text-on-default-background), var(--sklearn-color-text-on-default-background));
  background-size: 2px 100%;
  background-repeat: no-repeat;
  background-position: center center;
}

/* Parallel-specific style estimator block */

#sk-container-id-1 div.sk-parallel-item::after {
  content: "";
  width: 100%;
  border-bottom: 2px solid var(--sklearn-color-text-on-default-background);
  flex-grow: 1;
}

#sk-container-id-1 div.sk-parallel {
  display: flex;
  align-items: stretch;
  justify-content: center;
  background-color: var(--sklearn-color-background);
  position: relative;
}

#sk-container-id-1 div.sk-parallel-item {
  display: flex;
  flex-direction: column;
}

#sk-container-id-1 div.sk-parallel-item:first-child::after {
  align-self: flex-end;
  width: 50%;
}

#sk-container-id-1 div.sk-parallel-item:last-child::after {
  align-self: flex-start;
  width: 50%;
}

#sk-container-id-1 div.sk-parallel-item:only-child::after {
  width: 0;
}

/* Serial-specific style estimator block */

#sk-container-id-1 div.sk-serial {
  display: flex;
  flex-direction: column;
  align-items: center;
  background-color: var(--sklearn-color-background);
  padding-right: 1em;
  padding-left: 1em;
}


/* Toggleable style: style used for estimator/Pipeline/ColumnTransformer box that is
clickable and can be expanded/collapsed.
- Pipeline and ColumnTransformer use this feature and define the default style
- Estimators will overwrite some part of the style using the `sk-estimator` class
*/

/* Pipeline and ColumnTransformer style (default) */

#sk-container-id-1 div.sk-toggleable {
  /* Default theme specific background. It is overwritten whether we have a
  specific estimator or a Pipeline/ColumnTransformer */
  background-color: var(--sklearn-color-background);
}

/* Toggleable label */
#sk-container-id-1 label.sk-toggleable__label {
  cursor: pointer;
  display: flex;
  width: 100%;
  margin-bottom: 0;
  padding: 0.5em;
  box-sizing: border-box;
  text-align: center;
  align-items: start;
  justify-content: space-between;
  gap: 0.5em;
}

#sk-container-id-1 label.sk-toggleable__label .caption {
  font-size: 0.6rem;
  font-weight: lighter;
  color: var(--sklearn-color-text-muted);
}

#sk-container-id-1 label.sk-toggleable__label-arrow:before {
  /* Arrow on the left of the label */
  content: "▸";
  float: left;
  margin-right: 0.25em;
  color: var(--sklearn-color-icon);
}

#sk-container-id-1 label.sk-toggleable__label-arrow:hover:before {
  color: var(--sklearn-color-text);
}

/* Toggleable content - dropdown */

#sk-container-id-1 div.sk-toggleable__content {
  max-height: 0;
  max-width: 0;
  overflow: hidden;
  text-align: left;
  /* unfitted */
  background-color: var(--sklearn-color-unfitted-level-0);
}

#sk-container-id-1 div.sk-toggleable__content.fitted {
  /* fitted */
  background-color: var(--sklearn-color-fitted-level-0);
}

#sk-container-id-1 div.sk-toggleable__content pre {
  margin: 0.2em;
  border-radius: 0.25em;
  color: var(--sklearn-color-text);
  /* unfitted */
  background-color: var(--sklearn-color-unfitted-level-0);
}

#sk-container-id-1 div.sk-toggleable__content.fitted pre {
  /* unfitted */
  background-color: var(--sklearn-color-fitted-level-0);
}

#sk-container-id-1 input.sk-toggleable__control:checked~div.sk-toggleable__content {
  /* Expand drop-down */
  max-height: 200px;
  max-width: 100%;
  overflow: auto;
}

#sk-container-id-1 input.sk-toggleable__control:checked~label.sk-toggleable__label-arrow:before {
  content: "▾";
}

/* Pipeline/ColumnTransformer-specific style */

#sk-container-id-1 div.sk-label input.sk-toggleable__control:checked~label.sk-toggleable__label {
  color: var(--sklearn-color-text);
  background-color: var(--sklearn-color-unfitted-level-2);
}

#sk-container-id-1 div.sk-label.fitted input.sk-toggleable__control:checked~label.sk-toggleable__label {
  background-color: var(--sklearn-color-fitted-level-2);
}

/* Estimator-specific style */

/* Colorize estimator box */
#sk-container-id-1 div.sk-estimator input.sk-toggleable__control:checked~label.sk-toggleable__label {
  /* unfitted */
  background-color: var(--sklearn-color-unfitted-level-2);
}

#sk-container-id-1 div.sk-estimator.fitted input.sk-toggleable__control:checked~label.sk-toggleable__label {
  /* fitted */
  background-color: var(--sklearn-color-fitted-level-2);
}

#sk-container-id-1 div.sk-label label.sk-toggleable__label,
#sk-container-id-1 div.sk-label label {
  /* The background is the default theme color */
  color: var(--sklearn-color-text-on-default-background);
}

/* On hover, darken the color of the background */
#sk-container-id-1 div.sk-label:hover label.sk-toggleable__label {
  color: var(--sklearn-color-text);
  background-color: var(--sklearn-color-unfitted-level-2);
}

/* Label box, darken color on hover, fitted */
#sk-container-id-1 div.sk-label.fitted:hover label.sk-toggleable__label.fitted {
  color: var(--sklearn-color-text);
  background-color: var(--sklearn-color-fitted-level-2);
}

/* Estimator label */

#sk-container-id-1 div.sk-label label {
  font-family: monospace;
  font-weight: bold;
  display: inline-block;
  line-height: 1.2em;
}

#sk-container-id-1 div.sk-label-container {
  text-align: center;
}

/* Estimator-specific */
#sk-container-id-1 div.sk-estimator {
  font-family: monospace;
  border: 1px dotted var(--sklearn-color-border-box);
  border-radius: 0.25em;
  box-sizing: border-box;
  margin-bottom: 0.5em;
  /* unfitted */
  background-color: var(--sklearn-color-unfitted-level-0);
}

#sk-container-id-1 div.sk-estimator.fitted {
  /* fitted */
  background-color: var(--sklearn-color-fitted-level-0);
}

/* on hover */
#sk-container-id-1 div.sk-estimator:hover {
  /* unfitted */
  background-color: var(--sklearn-color-unfitted-level-2);
}

#sk-container-id-1 div.sk-estimator.fitted:hover {
  /* fitted */
  background-color: var(--sklearn-color-fitted-level-2);
}

/* Specification for estimator info (e.g. "i" and "?") */

/* Common style for "i" and "?" */

.sk-estimator-doc-link,
a:link.sk-estimator-doc-link,
a:visited.sk-estimator-doc-link {
  float: right;
  font-size: smaller;
  line-height: 1em;
  font-family: monospace;
  background-color: var(--sklearn-color-background);
  border-radius: 1em;
  height: 1em;
  width: 1em;
  text-decoration: none !important;
  margin-left: 0.5em;
  text-align: center;
  /* unfitted */
  border: var(--sklearn-color-unfitted-level-1) 1pt solid;
  color: var(--sklearn-color-unfitted-level-1);
}

.sk-estimator-doc-link.fitted,
a:link.sk-estimator-doc-link.fitted,
a:visited.sk-estimator-doc-link.fitted {
  /* fitted */
  border: var(--sklearn-color-fitted-level-1) 1pt solid;
  color: var(--sklearn-color-fitted-level-1);
}

/* On hover */
div.sk-estimator:hover .sk-estimator-doc-link:hover,
.sk-estimator-doc-link:hover,
div.sk-label-container:hover .sk-estimator-doc-link:hover,
.sk-estimator-doc-link:hover {
  /* unfitted */
  background-color: var(--sklearn-color-unfitted-level-3);
  color: var(--sklearn-color-background);
  text-decoration: none;
}

div.sk-estimator.fitted:hover .sk-estimator-doc-link.fitted:hover,
.sk-estimator-doc-link.fitted:hover,
div.sk-label-container:hover .sk-estimator-doc-link.fitted:hover,
.sk-estimator-doc-link.fitted:hover {
  /* fitted */
  background-color: var(--sklearn-color-fitted-level-3);
  color: var(--sklearn-color-background);
  text-decoration: none;
}

/* Span, style for the box shown on hovering the info icon */
.sk-estimator-doc-link span {
  display: none;
  z-index: 9999;
  position: relative;
  font-weight: normal;
  right: .2ex;
  padding: .5ex;
  margin: .5ex;
  width: min-content;
  min-width: 20ex;
  max-width: 50ex;
  color: var(--sklearn-color-text);
  box-shadow: 2pt 2pt 4pt #999;
  /* unfitted */
  background: var(--sklearn-color-unfitted-level-0);
  border: .5pt solid var(--sklearn-color-unfitted-level-3);
}

.sk-estimator-doc-link.fitted span {
  /* fitted */
  background: var(--sklearn-color-fitted-level-0);
  border: var(--sklearn-color-fitted-level-3);
}

.sk-estimator-doc-link:hover span {
  display: block;
}

/* "?"-specific style due to the `<a>` HTML tag */

#sk-container-id-1 a.estimator_doc_link {
  float: right;
  font-size: 1rem;
  line-height: 1em;
  font-family: monospace;
  background-color: var(--sklearn-color-background);
  border-radius: 1rem;
  height: 1rem;
  width: 1rem;
  text-decoration: none;
  /* unfitted */
  color: var(--sklearn-color-unfitted-level-1);
  border: var(--sklearn-color-unfitted-level-1) 1pt solid;
}

#sk-container-id-1 a.estimator_doc_link.fitted {
  /* fitted */
  border: var(--sklearn-color-fitted-level-1) 1pt solid;
  color: var(--sklearn-color-fitted-level-1);
}

/* On hover */
#sk-container-id-1 a.estimator_doc_link:hover {
  /* unfitted */
  background-color: var(--sklearn-color-unfitted-level-3);
  color: var(--sklearn-color-background);
  text-decoration: none;
}

#sk-container-id-1 a.estimator_doc_link.fitted:hover {
  /* fitted */
  background-color: var(--sklearn-color-fitted-level-3);
}
</style><div id="sk-container-id-1" class="sk-top-container"><div class="sk-text-repr-fallback"><pre>GridSearchCV(cv=3, estimator=RandomForestRegressor(n_jobs=-1, random_state=45),
             n_jobs=-1,
             param_grid={&#x27;max_depth&#x27;: [None, 10, 20],
                         &#x27;min_samples_split&#x27;: [2, 5],
                         &#x27;n_estimators&#x27;: [100, 200]})</pre><b>In a Jupyter environment, please rerun this cell to show the HTML representation or trust the notebook. <br />On GitHub, the HTML representation is unable to render, please try loading this page with nbviewer.org.</b></div><div class="sk-container" hidden><div class="sk-item sk-dashed-wrapped"><div class="sk-label-container"><div class="sk-label fitted sk-toggleable"><input class="sk-toggleable__control sk-hidden--visually" id="sk-estimator-id-1" type="checkbox" ><label for="sk-estimator-id-1" class="sk-toggleable__label fitted sk-toggleable__label-arrow"><div><div>GridSearchCV</div></div><div><a class="sk-estimator-doc-link fitted" rel="noreferrer" target="_blank" href="https://scikit-learn.org/1.6/modules/generated/sklearn.model_selection.GridSearchCV.html">?<span>Documentation for GridSearchCV</span></a><span class="sk-estimator-doc-link fitted">i<span>Fitted</span></span></div></label><div class="sk-toggleable__content fitted"><pre>GridSearchCV(cv=3, estimator=RandomForestRegressor(n_jobs=-1, random_state=45),
             n_jobs=-1,
             param_grid={&#x27;max_depth&#x27;: [None, 10, 20],
                         &#x27;min_samples_split&#x27;: [2, 5],
                         &#x27;n_estimators&#x27;: [100, 200]})</pre></div> </div></div><div class="sk-parallel"><div class="sk-parallel-item"><div class="sk-item"><div class="sk-label-container"><div class="sk-label fitted sk-toggleable"><input class="sk-toggleable__control sk-hidden--visually" id="sk-estimator-id-2" type="checkbox" ><label for="sk-estimator-id-2" class="sk-toggleable__label fitted sk-toggleable__label-arrow"><div><div>best_estimator_: RandomForestRegressor</div></div></label><div class="sk-toggleable__content fitted"><pre>RandomForestRegressor(max_depth=20, min_samples_split=5, n_estimators=200,
                      n_jobs=-1, random_state=45)</pre></div> </div></div><div class="sk-serial"><div class="sk-item"><div class="sk-estimator fitted sk-toggleable"><input class="sk-toggleable__control sk-hidden--visually" id="sk-estimator-id-3" type="checkbox" ><label for="sk-estimator-id-3" class="sk-toggleable__label fitted sk-toggleable__label-arrow"><div><div>RandomForestRegressor</div></div><div><a class="sk-estimator-doc-link fitted" rel="noreferrer" target="_blank" href="https://scikit-learn.org/1.6/modules/generated/sklearn.ensemble.RandomForestRegressor.html">?<span>Documentation for RandomForestRegressor</span></a></div></label><div class="sk-toggleable__content fitted"><pre>RandomForestRegressor(max_depth=20, min_samples_split=5, n_estimators=200,
                      n_jobs=-1, random_state=45)</pre></div> </div></div></div></div></div></div></div></div></div>




```python
# Explore the best model

print(grid.best_params_)
print(grid.best_score_)
```

    {'max_depth': 20, 'min_samples_split': 5, 'n_estimators': 200}
    0.19695408034221887



```python
# Predict on the test set and evaluate performance

pred = grid.best_estimator_.predict(X_test)

mse = mean_squared_error(y_test, pred)
r2 = r2_score(y_test, pred)

logger.info("Evaluate on the test set")

print("MSE:", mse)
print("R2 Score:", r2)
```

    MSE: 4.4127229137494874e-08
    R2 Score: 0.25071271139634355



```python
# Determine feature importance

feature_importance = pd.DataFrame({
    "Feature": X.columns,
    "Importance": grid.best_estimator_.feature_importances_}
)

feature_importance = feature_importance.sort_values(by="Importance", ascending=False)

print(feature_importance.head(15))

logger.info("Determine feature importance")
```

                           Feature  Importance
    12            price_volatility    0.253103
    1             environmentScore    0.158964
    4                         beta    0.157933
    58    Industry_Medical Devices    0.052349
    21           Sector_Technology    0.043907
    0                     totalEsg    0.040898
    11                  avg_volume    0.036766
    2                  socialScore    0.036315
    37             Industry_Copper    0.025515
    35  Industry_Computer Hardware    0.022024
    3              governanceScore    0.020398
    6                  overallRisk    0.019621
    9                     avg_high    0.019335
    7                     avg_open    0.016515
    8                    avg_close    0.014114



```python
# Visualize feature importance

top_features = feature_importance.head(7)

plt.figure(figsize=(10,7))
plt.barh(top_features['Feature'], top_features['Importance'], color="darkorange")
plt.xlabel("Coefficient")
plt.title("Feature Importance - Random Forest")
plt.tight_layout()
plt.savefig("./graphics/feature_importance.png")
plt.show()

```


    
![png](data_analysis_files/data_analysis_17_0.png)
    


### Analysis Rationale

The analysis process was guided by the goal of understanding how company characteristics and ESG factors relate to stock performance across S&P 500 firms. The target variable selected was avg_daily_return, as it provides a normalized measure of performance that allows for meaningful comparison across companies with different price levels. This choice reflects a focus on relative returns rather than absolute price movements, which are less informative in cross-sectional analysis. A Random Forest regression model was selected due to its ability to capture non-linear relationships and interactions between variables without requiring strong parametric assumptions. This is particularly important given the complexity of financial markets and the potential interplay between ESG scores, risk metrics, and sector classifications. Additionally, Random Forest models are robust to overfitting and can handle a mix of feature types effectively, making them well-suited for this dataset.

To improve model performance and ensure appropriate parameter selection, a grid search  was implemented to evaluate combinations of hyperparameters such as the number of trees and maximum tree depth. This process helps identify a model configuration that generalizes well to unseen data rather than relying on arbitrary parameter choices. Model performance was evaluated using the R² metric, which measures the proportion of variance in average daily returns explained by the model. This metric was chosen because it provides an interpretable measure of explanatory power in a regression context. While R² does not capture all aspects of predictive performance, it offers a useful baseline for assessing how well the selected features account for variation in stock returns. 

## Visualization


```python
# Visualize ESG Scores vs Performance

plt.figure(figsize=(8, 6))

z = np.polyfit(company_table["totalEsg"], company_table["avg_daily_return"], 1)
p = np.poly1d(z)

plt.scatter(company_table["totalEsg"], company_table["avg_daily_return"])
plt.plot(company_table["totalEsg"], p(company_table["totalEsg"]), color="Red", linewidth=3)
plt.xlabel("Total ESG Score")
plt.ylabel("Average Daily Return")
plt.title("ESG Score vs Stock Performance")
plt.savefig("./graphics/esg_performance.png")
plt.show()

logger.info("Visualize ESG scores vs performance")
```


    
![png](data_analysis_files/data_analysis_20_0.png)
    



```python
# Visualizer performance by sector

sector_performance = company_table.groupby("Sector")["avg_daily_return"].mean().sort_values()
sector_performance_pct = sector_performance * 100

plt.figure(figsize=(12, 7))

colors = ['RoyalBlue'] * len(sector_performance_pct)
colors[0] = 'Crimson'
colors[-1] = 'Green'

bars = plt.barh(sector_performance_pct.index, sector_performance_pct.values, color=colors, edgecolor='black', linewidth=0.5)

plt.title("Average Daily Return by Sector (S&P 500)", fontsize=15, pad=15, weight='bold')
plt.xlabel("Average Daily Return (%)", fontsize=12)
plt.ylabel("Sector", fontsize=12)

for bar in bars:
    width = bar.get_width()
    plt.text(width + 0.001, bar.get_y() + bar.get_height() / 2, f"{width:.2f}%", va='center', fontsize=10)

plt.axvline(0, color='black', linewidth=1)
plt.grid(axis='x', linestyle='--', alpha=0.4)
plt.gca().spines['top'].set_visible(False)
plt.gca().spines['right'].set_visible(False)
plt.xticks(fontsize=10)
plt.yticks(fontsize=12)
plt.tight_layout()
plt.savefig("./graphics/sector_performance.png")
plt.show()

logger.info("Visualize performance by sector")
```


    
![png](data_analysis_files/data_analysis_21_0.png)
    



```python
# Visualize distribution of returns

plt.figure(figsize=(8, 6))
plt.hist(company_table["avg_daily_return"], bins=30)
plt.title("Distribution of Average Returns")
plt.xlabel("Average Return")
plt.ylabel("Frequency")
plt.savefig("./graphics/average_returns.png")
plt.show()

logger.info("Visualize distribution of returns")
```


    
![png](data_analysis_files/data_analysis_22_0.png)
    



```python
# Visualize distribution of ESG scores

plt.figure(figsize=(8, 6))
plt.hist(company_table["totalEsg"], bins=30)
plt.title("Distribution of ESG Scores")
plt.xlabel("ESG Score")
plt.ylabel("Frequency")
plt.savefig("./graphics/esg_distribution.png")
plt.show()

logger.info("Visualize distribution of ESG scores")
```


    
![png](data_analysis_files/data_analysis_23_0.png)
    



```python
# Heatmap of features

plt.figure(figsize=(10, 8))
sns.heatmap(company_table.corr(numeric_only=True), annot=False)
plt.title("Correlation Matrix")
plt.savefig("./graphics/feature_heatmap.png")
plt.show()

logger.info("Plot heatmap of features")
```


    
![png](data_analysis_files/data_analysis_24_0.png)
    



```python
# Visualize volatility vs return

plt.figure(figsize=(8, 6))
plt.scatter(company_table["price_volatility"], company_table["avg_daily_return"])
plt.xlabel("Volatility")
plt.ylabel("Average Return")
plt.title("Risk vs Return")
plt.savefig("./graphics/risk_return.png")
plt.show()

logger.info("Visualize risk vs return")
```


    
![png](data_analysis_files/data_analysis_25_0.png)
    


### Visualization Rationale

The visualization process was designed to highlight key relationships within the dataset while being appealing and understandable for a general audience. A combination of visualization types were selected to capture different analytical perspectives. A plot of feature importance was used to identify which variables most strongly influence model predictions, providing insight into the relative impact of ESG metrics, risk factors, and company characteristics. Various other plots were implemented to examine relationships between variables such as ESG scores and average returns or risk. Plots of variable distributions were used to understand the spread key variables like returns and ESG scores. Additionally, a correlation heatmap was included to provide a high-level view of relationships across all numerical features. Together, these visualization types support both exploratory analysis and the display of meaningful insights.

The sector-level visualization was specifically designed to provide insight into how performance varies across different areas of the market. By aggregating average daily returns by sector and displaying them in a horizontal bar chart, the visualization enables a comparison of stocker performance across industries. This visualization helps contextualize individual company results within broader market segments, highlighting which sectors contribute most to overall returns. Overall, it takes a high-level view to display macro market trends, reinforcing the importance of sector in understanding stock performance within the S&P 500.
