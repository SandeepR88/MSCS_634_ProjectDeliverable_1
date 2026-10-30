# MSCS 634 – Project Deliverable 1
## Data Collection, Cleaning, and Exploration

**Project:** Advanced Data Mining for Data-Driven Insights and Predictive Modeling  
**Dataset:** UCI Online Retail  
**Deliverable:** 1 – Data Collection, Cleaning, and Exploration  
**Notebook:** `MSCS_634_ProjectDeliverable_1.ipynb`

---

## 1. Project Overview

This deliverable establishes the data preparation and exploratory analysis foundation for a larger data-mining project. Using the UCI Online Retail dataset, the notebook imports and inspects transaction records, identifies data-quality issues, prepares cleaned analytical datasets, and explores sales patterns through descriptive statistics and visualizations.

The analysis is intended to support the later project deliverables involving regression, classification, customer clustering, and association-rule mining.

**Important scope note:** The dataset contains historical transactions from December 2010 through December 2011. The results describe this retailer and this historical period; they should not be interpreted as current retail-market conditions.

## 2. Dataset Description and Justification

The UCI Online Retail dataset contains **541,909 transaction-line records** from a UK-based, non-store online retailer. The source period is December 1, 2010, through December 9, 2011.

The dataset has eight attributes:

| Attribute | Description |
|---|---|
| `InvoiceNo` | Invoice or transaction identifier. Invoice numbers beginning with `C` indicate cancellations. |
| `StockCode` | Product identifier. |
| `Description` | Product description. |
| `Quantity` | Number of units recorded on a transaction line. Negative values can represent returns. |
| `InvoiceDate` | Date and time of the transaction. |
| `UnitPrice` | Unit price in pounds sterling (£). |
| `CustomerID` | Customer identifier; some records do not have an identifier. |
| `Country` | Customer's country. |

### Why this dataset was selected

The dataset meets the assignment's minimum requirements of at least 8–10 attributes and 500 records: it has eight attributes and more than half a million transaction lines. It also provides useful information for multiple data-mining techniques:

- **Regression:** transaction, product, and time features can support revenue-related prediction.
- **Classification:** cancellation status can be examined as a classification target.
- **Clustering:** customer-level transaction summaries can support customer segmentation.
- **Association rules:** invoice and product identifiers can be used to identify products that occur together in baskets.

The dataset is therefore suitable for exploring both transaction-level and customer-level business questions.

**Dataset source:** [UCI Machine Learning Repository – Online Retail](https://archive.ics.uci.edu/dataset/352/online+retail)

## 3. Tools and Libraries

The notebook uses Python and the following libraries:

- **Pandas:** data loading, inspection, cleaning, grouping, and aggregation.
- **NumPy:** numerical operations.
- **Matplotlib:** plotting and visualization.
- **Seaborn:** statistical charts and visual exploration.
- **OpenPyXL:** reading the Excel dataset.
- **IPython.display:** displaying dataframes in the notebook.

Install the required packages, if they are not already available, with:

```bash
pip install pandas numpy matplotlib seaborn openpyxl scikit-learn jupyter
```

## 4. Repository Contents

The repository can be organized as follows:

```text
MSCS_634_ProjectDeliverable_1/
│
├── MSCS_634_ProjectDeliverable_1.ipynb
├── README.md
├── data/
│   └── Online Retail.xlsx
└── Screenshots/
    ├── 1. Data Import.PNG
    ├── 1. Dataset Structure.PNG
    ├── 3. Dataset Inspection.PNG
    ├── 4. Data Cleaning.PNG
    ├── 5. Data Cleaning.PNG
    ├── 6. Remaining Missing Values Review.PNG
    ├── 7. Distribution of Quantity and Unit Price.PNG
    ├── 8. Box Plots.PNG
    ├── 9. Monthly Revenue Trend.PNG
    ├── 10. Revenue By Month.PNG
    ├── 11. Top 10 Countries By Sales Revenue.PNG
    ├── 12. Sales Revenue By Country.PNG
    ├── 13. Top 10 Products by Units sold.PNG
    ├── 14. Product by Units sold.PNG
    ├── 15. Correlation Matrix.PNG
    └── 16. Summary Statistics.PNG
```

The notebook creates `data/` and `visualizations/` directories when it runs. It attempts to download the source Excel file into `data/Online Retail.xlsx` if the file is not already present. The screenshots in `Screenshots/` document the notebook execution and results.

**Reproducibility note:** The source Excel dataset is not included in the screenshots archive. Keep `Online Retail.xlsx` in the `data/` directory when running the notebook offline, or allow the notebook to download it. The generated chart files are saved in `visualizations/` when the relevant notebook cells are executed.

## 5. Data Loading and Initial Inspection

The notebook loads `Online Retail.xlsx` with Pandas and checks the dataset's dimensions, sample records, data types, descriptive statistics, unique values, and missing-value counts.

The initial import confirmed:

- **541,909 rows**
- **8 columns**
- **5,268 exact duplicate rows**
- **135,080 missing `CustomerID` values**
- **1,454 missing product descriptions**

The initial inspection also identified non-positive quantities and unit prices. These values were reviewed as data-quality conditions rather than assumed to be ordinary sales.

Evidence: [Data import screenshot](Screenshots/1.%20Data%20Import.PNG) · [Dataset structure screenshot](Screenshots/1.%20Dataset%20Structure.PNG) · [Dataset inspection screenshot](Screenshots/3.%20Dataset%20Inspection.PNG)

## 6. Data Cleaning and Preparation

The notebook applies the following steps:

1. **Preserved the original data:** a working copy was created so the imported dataframe remained unchanged.
2. **Standardized column labels and text:** column names and selected text fields were stripped of surrounding whitespace. Product descriptions were converted to uppercase to improve consistency during grouping.
3. **Converted data types:** `InvoiceDate` was parsed as a datetime field, while `Quantity` and `UnitPrice` were converted to numeric values. Invalid conversions were coerced to missing values.
4. **Removed exact duplicates:** duplicate transaction rows were removed.
5. **Removed records without essential transaction values:** rows missing a usable invoice date, quantity, or unit price were excluded from the cleaned transaction dataframe.
6. **Calculated line revenue:** `LineRevenue` was calculated as `Quantity × UnitPrice`.
7. **Identified cancellations:** a Boolean `IsCancellation` field was created using invoice numbers beginning with `C`.
8. **Created an ordinary-sales subset:** `sales_df` contains non-cancelled transactions with positive quantities and positive unit prices.
9. **Created a customer-identified subset:** `customer_sales_df` contains sales records with a known `CustomerID`, for customer-level analysis.

### Why returns, cancellations, and missing customer IDs were handled separately

Negative quantities and cancellation invoices can represent meaningful returns or cancelled transactions. They were retained in the general cleaned transaction dataframe instead of being automatically deleted. The sales-only subset excludes these records so that ordinary sales summaries are not distorted.

Missing `CustomerID` values were not filled with invented identifiers and were not used for customer-level analysis. However, those transactions remain useful for product- and country-level analysis. Missing product descriptions were not replaced with fabricated product names.

### Cleaning results

The execution screenshots show the following results:

| Measure | Result |
|---|---:|
| Original rows | 541,909 |
| Exact duplicates identified and removed | 5,268 |
| Rows after general cleaning | 536,641 |
| Sales-only rows | 524,878 |
| Sales rows with identified customers | 392,692 |
| Invalid dates after conversion | 0 |
| Missing customer IDs after cleaning | 135,037 |
| Missing product descriptions after cleaning | 1,454 |
| Cancellation invoice rows | 9,251 |
| Rows with negative quantity | 10,624 |
| Rows with zero or negative unit price | 2,512 |

The cleaning process reduced the original dataset by removing exact duplicates and records without usable date, quantity, or price values. The sales-only subset was then created for the EDA of ordinary sales.

### Remaining missing values

After general cleaning, the screenshots report:

| Column | Missing count | Missing percentage |
|---|---:|---:|
| `CustomerID` | 135,037 | 25.16% |
| `Description` | 1,454 | 0.27% |
| Other analyzed columns | 0 | 0.00% |

The missing customer identifiers are a limitation for customer segmentation, but they do not prevent all transaction-level analysis.

Evidence: [Data cleaning – quality checks](Screenshots/4.%20Data%20Cleaning.PNG) · [Data cleaning – prepared records](Screenshots/5.%20Data%20Cleaning.PNG) · [Remaining missing values](Screenshots/6.%20Remaining%20Missing%20Values%20Review.PNG)

## 7. Exploratory Data Analysis and Findings

The notebook explores quantity and unit-price distributions, possible outliers, monthly revenue, country-level revenue, product demand, and correlations among numeric sales variables.

### 7.1 Quantity and unit-price distributions

The histograms show that both quantity and unit price are strongly right-skewed: most recorded sales lines are concentrated at lower values, while a smaller number have much larger quantities or prices. For readability, the distribution plots cap displayed values at the 99th percentile; this is a visualization choice and does not change the underlying sales dataframe.

Evidence: [Quantity and unit-price distributions](Screenshots/7.%20Distribution%20of%20Quantity%20and%20Unit%20Price.PNG)

### 7.2 Outlier inspection

Boxplots and the interquartile range (IQR) rule were used to flag potential extreme values in quantity, unit price, and line revenue. The notebook treats these flags as diagnostic indicators rather than proof of data errors. High quantities may reflect legitimate wholesale purchases, so potentially valid transactions were not automatically removed.

Evidence: [Boxplots and outlier inspection](Screenshots/8.%20Box%20Plots.PNG)

### 7.3 Monthly revenue

The sales-only data contain **£10,642,110.80** in recorded line revenue. The monthly aggregation shows that revenue was not constant throughout the historical period. November 2011 had the highest recorded monthly revenue at approximately **£1,503,683.76**, while February 2011 had the lowest at approximately **£522,545.56**. Revenue increased notably during September, October, and November 2011, before falling in December 2011; December covers only part of the month in the source data.

| Month | Revenue (£) |
|---|---:|
| December 2010 | 821,452.73 |
| January 2011 | 689,811.61 |
| February 2011 | 522,545.56 |
| March 2011 | 716,215.26 |
| April 2011 | 536,968.49 |
| May 2011 | 769,296.61 |
| June 2011 | 760,547.01 |
| July 2011 | 718,076.12 |
| August 2011 | 757,841.38 |
| September 2011 | 1,056,463.75 |
| October 2011 | 1,151,263.73 |
| November 2011 | 1,503,683.76 |
| December 2011 | 637,790.33 |

Evidence: [Monthly revenue trend](Screenshots/9.%20Monthly%20Revenue%20Trend.PNG) · [Monthly revenue values](Screenshots/10.%20Revenue%20By%20Month.PNG)

### 7.4 Revenue by country

The United Kingdom generated by far the largest recorded revenue, approximately **£9,001,744.09**. The Netherlands and EIRE were the next largest contributors in the displayed top-ten results. This indicates that the sales in this dataset are highly concentrated in the UK, although the retailer also recorded transactions across other countries.

Top 10 countries by recorded sales revenue:

| Country | Revenue (£) |
|---|---:|
| United Kingdom | 9,001,744.09 |
| Netherlands | 285,446.34 |
| EIRE | 283,140.52 |
| Germany | 228,678.40 |
| France | 209,625.37 |
| Australia | 138,453.91 |
| Spain | 61,555.56 |
| Switzerland | 57,067.80 |
| Belgium | 41,199.34 |
| Sweden | 36,367.83 |

Evidence: [Top 10 countries chart](Screenshots/11.%20Top%2010%20Countries%20By%20Sales%20Revenue.PNG) · [Country revenue values](Screenshots/12.%20Sales%20Revenue%20By%20Country.PNG)

### 7.5 Products by units sold

The product aggregation indicates that **PAPER CRAFT, LITTLE BIRDIE** had the highest total units sold in the displayed top-ten results, with **80,995 units**. Other high-volume products included MEDIUM CERAMIC TOP STORAGE JAR, WORLD WAR 2 GLIDERS ASSTD DESIGNS, and JUMBO BAG RED RETROSPOT.

The results show why product demand should be considered separately from revenue: a product with high unit volume does not necessarily have the highest revenue because unit prices differ.

| Product | Units sold |
|---|---:|
| PAPER CRAFT, LITTLE BIRDIE | 80,995 |
| MEDIUM CERAMIC TOP STORAGE JAR | 78,033 |
| WORLD WAR 2 GLIDERS ASSTD DESIGNS | 54,951 |
| JUMBO BAG RED RETROSPOT | 48,371 |
| WHITE HANGING HEART T-LIGHT HOLDER | 37,872 |
| POPCORN HOLDER | 36,749 |
| PACK OF 72 RETROSPOT CAKE CASES | 36,396 |
| ASSORTED COLOUR BIRD ORNAMENT | 36,362 |
| RABBIT NIGHT LIGHT | 30,739 |
| MINI PAINT SET VINTAGE | 26,633 |

Evidence: [Top products chart](Screenshots/13.%20Top%2010%20Products%20by%20Units%20sold.PNG) · [Product unit totals](Screenshots/14.%20Product%20by%20Units%20sold.PNG)

### 7.6 Correlation among numeric sales variables

The correlation matrix reports:

| Variable pair | Correlation |
|---|---:|
| Quantity and line revenue | 0.91 |
| Unit price and line revenue | 0.14 |
| Quantity and unit price | Approximately 0.00 |

Quantity has a strong positive correlation with line revenue, while unit price has a weaker positive correlation. Quantity and unit price have almost no linear correlation in this subset. These are descriptive relationships, not evidence that one variable causes another. The strong quantity–revenue relationship is also expected because line revenue is calculated by multiplying quantity by unit price.

Evidence: [Numeric correlation matrix](Screenshots/15.%20Correlation%20Matrix.PNG)

### 7.7 Overall summary statistics

The notebook's final summary reports:

| Measure | Result |
|---|---:|
| General cleaned rows | 536,641 |
| Sales-only rows | 524,878 |
| Unique invoices in sales | 19,960 |
| Unique products in sales | 3,922 |
| Identified customers | 4,338 |
| Countries represented in sales | 38 |
| Total sales revenue | £10,642,110.80 |
| Average line revenue | £20.28 |
| Sales date range | December 1, 2010 – December 9, 2011 |

Evidence: [Summary statistics](Screenshots/16.%20Summary%20Statistics.PNG)

## 8. Challenges and How They Were Addressed

| Challenge | How it was addressed |
|---|---|
| Duplicate transaction lines | Identified and removed exact duplicate rows. |
| Missing customer identifiers | Retained the transactions for general sales analysis and created a separate customer-identified subset for customer-level work. |
| Missing product descriptions | Did not invent product names; description-dependent analysis excludes missing descriptions. |
| Returns and cancellations | Preserved them in the general cleaned transaction data and excluded them from the ordinary sales-only subset. |
| Non-positive quantities and prices | Reviewed them as data-quality conditions and excluded them from the sales-only subset. |
| Extreme quantities and prices | Used histograms, boxplots, and IQR flags; did not automatically delete potentially legitimate wholesale transactions. |
| Historical and partial-period data | Interpreted trends within the source date range and treated December 2011 as a partial month. |
| Customer-level analysis limitations | Restricted customer-level analysis to records with known customer identifiers. |

## 9. How the Findings Guide Future Modeling

The EDA findings inform the next stages of the project:

- **Regression – Deliverable 2:** Develop a clearly defined revenue prediction target. Avoid data leakage and recognize that predicting line revenue from both quantity and unit price is largely a direct reconstruction because `LineRevenue = Quantity × UnitPrice`. A more meaningful task could predict invoice-level or future-period revenue from information available before the outcome.
- **Classification – Deliverable 3:** Cancellation status may be evaluated as a target. First inspect class balance, then use appropriate evaluation measures such as precision, recall, F1-score, and a confusion matrix. Do not include variables that directly reveal the target in the predictors.
- **Clustering:** Aggregate known-customer transactions into recency, frequency, and monetary (RFM) features. Consider skewness, scaling, and cluster-quality evaluation.
- **Association-rule mining:** Create invoice-product baskets from valid positive-quantity sales. Exclude missing product descriptions and evaluate support, confidence, and lift when interpreting rules.
- **Outlier handling:** Compare robust scaling, log transformations, and sensitivity analysis before deciding whether extreme but plausible transactions should be excluded.

## 10. Limitations and Ethical Considerations

This dataset represents one retailer and a historical period of approximately one year. Its findings may not generalize to other retailers, countries, or current market conditions. Missing customer identifiers limit the completeness of customer-level analysis. Product codes and descriptions may also contain inconsistencies.

Customer identifiers should be treated as potentially linkable data and should not be published unnecessarily. Future predictive models should be assessed for data leakage, bias, and unintended consequences before being used to influence decisions about customers.

## 11. Conclusion

Deliverable 1 establishes a reproducible workflow for loading, inspecting, cleaning, and exploring the UCI Online Retail dataset. The notebook documents the removal of exact duplicates, the handling of missing and invalid transaction values, and the separation of general transaction records from ordinary sales and customer-identified sales subsets.

The EDA indicates that sales revenue is concentrated in the United Kingdom, varies substantially by month, and is strongly associated with quantity at the transaction-line level. Product demand, missing customer identifiers, and potential outliers provide important considerations for the next stages. These findings create a documented foundation for regression, classification, clustering, and association-rule mining in subsequent deliverables.

## 12. References

Chen, D. (2015). *Online Retail* [Dataset]. UCI Machine Learning Repository. https://doi.org/10.24432/C5BW33

Chen, D., Sain, S. L., & Guo, K. (2012). Data mining for the online retail industry: A case study of RFM model-based customer segmentation using data mining. *Journal of Database Marketing & Customer Strategy Management, 19*, 197–208. https://doi.org/10.1057/dbm.2012.17
