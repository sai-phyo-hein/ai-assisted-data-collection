# ai-assisted-data-collection

### Introduction
As part of my self-study, this project is attempted to learn prompt configuration, context length, deployment of local AI models and comparison of their performance. The data collected is subjected to use for Merton Based valuation models which includes debts and assets of the companies as the contingent claims. The study only focused on the company tickers filed recently for chapter 11. The list of bankrupted tickers are extracted from https://stockanalysis.com/actions/bankruptcies. 

10-Q and 10-K files for each ticker starting from 5 years ahead of their bankruptcy are accessed from SEC website with the aid of a python library call 'edger-tool'. The required data fields like total short-term debt, total long-term debt, number of share outstanding and cash/cash equivalent were needed to be reviewed as different files has different tagging and naming. Tough, edger-tool has methods to extract specific balance-sheet, income statement, or other tables from the filings, some of the files raise error as the data format for such extraction were not included. These requirements lead me to use an AI model which will review the whole text files, which are readily included for almost all filings. 

Context length and cost of the available AI models become the obstacles for this step. Using API provider burns the budget to zero just after processing ~50 files: each file has at least 3 pages of text. Thus, locally deployable small models were the option to proceed. Firstly, some models were tested without considering the the context length and the resulting numbers were inaccurate when human-checked, and inconsistent given that the same optimized prompt was used for each re-run. 

Finally, large context length models like mistral-nemo:12b and gemma3:4b both of which had (128K context length) were applied to extract the data stored in attach database file. Ollama was used to host those models. The text files were preprocessed with keywords to reduce number of lines from 8~10 pages to 3~4 pages. The whole contents of the remaining lines were fed to the model chatbox using the prompt configured as in prompt.txt attached. 

### Database file
The database file consists of these tables: 
|TableName|Description|Remark|
|---|---|---|
|bankrupted|table of bankrupted tickers|from stockanalysis.com|
|financial2|financial data extracted for bankrupted tickers without cash data|used mistral-nemo model|
|financial3|financial data extracted for bankrupted tickers with cash data|used gemma3 model|
|ticker1|price data of ticker1|from yahoo finance|
|ticker2|price data of ticker2|from yahoo finance|
| ... | ... | ... |

### Data Description
table: bankrupted
|ColName|Description|Remark|
|---|---|---|
|Date|date of chapter 11 filing||
|Ticker|company ticker||
|Company|company name||
|Available|where price data is available on yahoo finance|this is time dependent and can change| 
|EndDate|same as Date||
|StartDate|an output from calculating Available|not useful| 

table: financial2 | financial3
|ColName|Description|Remark|
|---|---|---|
|ticker|company ticker||
|date|date of filing the financial report|directly extracted from SEC report| 
|shares|number of outstanding shares as of date report|directly extracted from SEC report|
|short_term_debt|total debt/liabilities due under 1 year|included fields/calculations are listed in prompt.txt|
|long_term_debt|total debt/liabilities due over 1 year|included fields/calculations are listed in prompt.txt|
|cash_equivalent|total cash and cash equivalent as of date report |included fields/calculations are listed in prompt.txt|
|notes|this column records breifs of how the cols in left are calculated or scaled ||

table: tickers
|ColName|Description|Remark|
|---|---|---|
|Date|date of daily price||
|Close|close price||






