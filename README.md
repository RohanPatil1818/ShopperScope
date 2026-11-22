
<h2 align="center">🛍️ ShopperScope — Customer Behavior & Retail Revenue Intelligence</h2><p align="center"> <strong>From shopping transactions to smarter retail decisions.</strong><br> <em>An end-to-end customer analytics project exploring purchase value, product preferences, subscriptions, discounts, and shopping experiences.</em> </p><p align="center"> <img src="https://img.shields.io/badge/3%2C900-Customers-19354C?style=flat-square" alt="3,900 customers" /> <img src="https://img.shields.io/badge/%24233%2C081-Recorded%20Purchases-008B87?style=flat-square" alt="$233,081 recorded purchase value" /> <img src="https://img.shields.io/badge/3-Interactive%20BI%20Pages-8957A1?style=flat-square" alt="3 interactive BI pages" /> </p><p align="center"> <img alt="Python" src="https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white" /> <img alt="SQL Server" src="https://img.shields.io/badge/SQL_Server-CC2927?style=for-the-badge&logo=microsoftsqlserver&logoColor=white" /> <img alt="Power BI" src="https://img.shields.io/badge/Power_BI-F2C811?style=for-the-badge&logo=powerbi&logoColor=black" /> <img alt="Jupyter" src="https://img.shields.io/badge/Jupyter-F37626?style=for-the-badge&logo=jupyter&logoColor=white" /> <img alt="Analytics" src="https://img.shields.io/badge/Retail_Analytics-008B87?style=for-the-badge" /> </p><p align="center"> <a href="#-executive-summary">Overview</a> · <a href="#-dataset--data-pipeline">Data Pipeline</a> · <a href="#-data-resources">Data Resources</a> · <a href="#-power-bi-dashboard">Dashboard</a> · <a href="#-verified-business-insights">Insights</a> · <a href="#-recommendations--profitability">Recommendations</a> · <a href="#-how-to-run">Setup</a> </p>
Project focus: Customer behavior • Retail revenue • Product performance • Business intelligence. This is a descriptive and diagnostic analytics project—not a churn-prediction model. Because the source has no dated transactions, observed churn outcomes, or cost/margin data, retention rates and profitability uplift are not reported as measured results.

📌 Executive Summary
What makes customers spend—and where should a retailer look for its next opportunity? ShopperScope explores this question through 3,900 US customer-level shopping records, examining purchase value across product categories, demographics, subscription choices, discounts, and delivery options.

Rather than stopping at charts, the project connects three practical stages of analysis: Python prepares and enriches the data, SQL Server answers eight business-focused questions, and a three-page interactive Power BI report turns those findings into an accessible view for decision-makers.

The result is a portfolio project built around clear metrics, explainable insights, and testable commercial recommendations—with a deliberate distinction between what the data shows and what still requires experimentation.

KPI	Verified result	Definition
💰 Recorded purchase value	$233,081	SUM(purchase_amount)
👥 Customers / records	3,900	DISTINCTCOUNT(customer_id); IDs are unique in this dataset
🧾 Average purchase amount	$59.76	AVG(purchase_amount)
⭐ Average review rating	3.75 / 5	After category-median imputation of 37 missing ratings
🛍️ Product types	25	Distinct items purchased
🔖 Subscribers	1,053 (27.00%)	subscription_status = Yes
🏷️ Discount-flagged purchases	1,677 (43.00%)	discount_applied = Yes
📍 Locations	50	Distinct US state names recorded
Business goal: Help retail stakeholders understand who contributes purchase value, which product groups perform strongly, and how subscription, discount, and shipping choices relate to spending. Use those patterns to prioritize smarter merchandising and marketing experiments—not to claim outcomes the available data cannot prove.

💡 Headline takeaway: Clothing represents 44.73% of recorded purchase value, while subscriber purchases average $59.49 versus $59.87 for non-subscribers. The opportunity is to test category and membership strategies against cost and margin data, rather than assume higher sales or retention.

❓ Key Business Questions
The analysis is organized around the questions a retail manager, marketing lead, or BI stakeholder would ask before making a campaign, assortment, or service decision:

How does recorded purchase value vary by gender, age group, category, and season?
Which discount-using customers spent more than the overall average?
Which five products have the highest average review ratings?
Do Standard and Express shipping customers differ in average purchase value?
Do subscribers and non-subscribers differ in customers, purchase value, and average spend?
Which five products have the highest rate of discounted purchases?
How many customers fall into the SQL-defined New, Returning, and Loyal historical-purchase groups?
Which products are most frequently purchased within each category?
🗂️ Dataset & Data Pipeline
Good decisions begin with trustworthy data. The workflow starts with source profiling, handles missing values, creates analysis-ready features, and brings the prepared records into SQL Server and Power BI. Each transformation has a clear purpose: improving consistency or making an important customer question answerable.

Input: data/raw/customer_shopping_behavior.csv (provided in the ZIP under .xls, but its bytes are UTF-8 CSV, not an Excel workbook). A copy of the uploaded source dataset is available in the project Google Drive data folder. The original dataset publisher/source URL is not documented: [Insert Original Dataset Source URL].

Dataset attribute	Description
Initial shape	3,900 rows × 18 columns
Row grain	One recorded purchase per unique Customer ID in this snapshot
Domain	US retail shopping: Clothing, Accessories, Footwear, Outerwear
Customer attributes	Customer ID, age, gender, location, subscription status
Basket/product attributes	Item purchased, category, size, color, season, purchase amount (USD)
Behavior/service attributes	Review rating, shipping, discount, promo code, previous purchases, payment method, purchase frequency
Missing values	37 null review ratings; all other source columns have 0 nulls
After notebook transformations	3,900 rows × 19 columns (17 retained source fields + 2 engineered fields)
Processing steps verified in the notebook
The Python notebook turns the source extract into a more consistent analytical dataset while preserving all 3,900 records:

Profile: Inspect info(), describe(), and null counts across all 18 source columns.
Impute reviews: Fill the 37 missing Review Rating values with the median within each product Category.
Standardize schema: Convert columns to snake_case; rename purchase_amount_(usd) to purchase_amount.
Engineer age bands: Use pd.qcut(age, q=4) with labels Young Adult, Adult, Middle-aged, Senior (data-driven quartiles, not fixed demographic thresholds).
Engineer frequency days: Map reported purchase frequencies to days: Weekly=7; Fortnightly/Bi-Weekly=14; Monthly=30; Quarterly/Every 3 Months=90; Annually=365.
Remove redundant field: Confirm promo_code_used == discount_applied for all 3,900 records, then drop promo_code_used.
Load SQL Server: Connect via SQLAlchemy + pyodbc using Windows authentication and upload the transformed dataframe to customer_behaviour.dbo.customers.
Query & visualize: Run eight T-SQL questions and explore the resulting model in three Power BI report pages.
SQL methods and segment rules
SQL turns the prepared dataset into stakeholder answers. The supplied queries compare average purchase values, investigate discount usage, rank products, and categorize customers by reported purchase history. The SQL file demonstrates GROUP BY, SUM, AVG, COUNT, TOP, a subquery, conditional aggregation with CASE, CTEs, and ROW_NUMBER() OVER (PARTITION BY ...).

The historical-purchase groups use reported previous_purchases, not repeated orders observed in this dataset:

SQL label	Rule	Customers
New	previous_purchases = 1	83
Returning	previous_purchases BETWEEN 2 AND 10	701
Loyal	previous_purchases > 10	3,116
SQL precision note: Discount-rate calculation should use 100.0 * SUM(CASE WHEN ... THEN 1 ELSE 0 END) / NULLIF(COUNT(*), 0) to avoid integer division in SQL Server. The notebook does not build an RFM model, a classification model, or a churn label.

📂 Data Resources
<p align="center"> <strong>Explore the data behind every insight.</strong><br> <sub>Access the source shopping dataset and follow the path from raw records to the prepared analytical model.</sub> </p>
Transparent data access matters. The Google Drive archive contains the provided shopping source, while the Python notebook documents the cleaning and feature-engineering steps needed to recreate the analysis.

<p align="center"> <a href="https://drive.google.com/drive/u/0/folders/1m4MMWPt4n4TAem09iTgvVeE7K9fsEgGx"> <img alt="View Dataset on Google Drive" src="https://img.shields.io/badge/View_Dataset-Google_Drive-4285F4?style=for-the-badge&logo=googledrive&logoColor=white" /> </a> </p>
📁 Resource	📋 Description	🔗 Access
Original shopping dataset	customer_shopping_behavior.xls — the uploaded source file, containing 3,900 customer shopping records. The project notebook processes its CSV-formatted contents.	Open data folder
Python-transformed dataset	Prepared locally using the notebook's missing-value treatment, column standardization, and engineered fields.	Run the analysis notebook
Data availability: The linked Google Drive folder was found with the original dataset file. A separate cleaned-data export was not visible in that folder during review. To allow recruiters to open the files, set Google Drive sharing to Anyone with the link → Viewer. The Drive copy is not a substitute for citing the original dataset publisher.

📊 Power BI Dashboard
Three pages. Three levels of business understanding. The Power BI experience moves from an executive snapshot, to spending and service comparisons, to product-level performance. Its interactive filters help users explore the same customer dataset from different commercial angles.

The visuals below are actual screenshots linked to the three pages uploaded in the ShopperScope GitHub repository.

Page 1 — Customer Overview
See the retail business at a glance. The opening page brings together purchase-value and customer KPIs, then shows how customer composition and spending vary across demographic and merchandise categories.

<p align="center"> <a href="https://github.com/RohanPatil1818/ShopperScope/blob/main/Screenshot%201.png"> <img src="https://raw.githubusercontent.com/RohanPatil1818/ShopperScope/main/Screenshot%201.png" alt="Customer Overview — Power BI dashboard page 1" width="900" /> </a> </p><p align="center"><sub><strong>Figure 1.</strong> Executive KPIs and customer composition by age group, category, and subscription status. <a href="https://github.com/RohanPatil1818/ShopperScope/blob/main/Screenshot%201.png">View full screenshot on GitHub</a></sub></p>
KPI cards: Revenue (USD); Customers; Average Purchase (USD); Average Review (/5).
Charts: Revenue by age group; Customers by age group; Revenue by category; Customers by category; Customer mix by subscription.
Slicers: Gender, Category, Subscription status, Shipping type.
Page 2 — Spending & Service
Understand the choices surrounding each purchase. This page compares subscriber and non-subscriber spending, gender-based purchase value, and Standard versus Express shipping—useful starting points for designing experiments, not evidence of cause and effect.

<p align="center"> <a href="https://github.com/RohanPatil1818/ShopperScope/blob/main/Screenshot%202.png"> <img src="https://raw.githubusercontent.com/RohanPatil1818/ShopperScope/main/Screenshot%202.png" alt="Spending & Service — Power BI dashboard page 2" width="900" /> </a> </p><p align="center"><sub><strong>Figure 2.</strong> Subscription economics, gender spending, and shipping-service comparison. <a href="https://github.com/RohanPatil1818/ShopperScope/blob/main/Screenshot%202.png">View full screenshot on GitHub</a></sub></p>
KPI cards: Revenue; Average Purchase; Subscriber Average Purchase; Non-Subscriber Average Purchase.
Charts/table: Revenue by gender; Average purchase by subscription; Standard vs Express average purchase; Subscription scorecard.
Slicers: Gender, Category, Season, Discount applied.
Page 3 — Product Performance
Move from customer patterns to product decisions. The final page highlights review scores and popular items across categories, supporting assortment reviews and product-experience discussions.

<p align="center"> <a href="https://github.com/RohanPatil1818/ShopperScope/blob/main/Screenshot%203.png"> <img src="https://raw.githubusercontent.com/RohanPatil1818/ShopperScope/main/Screenshot%203.png" alt="Product Performance — Power BI dashboard page 3" width="900" /> </a> </p><p align="center"><sub><strong>Figure 3.</strong> Product types, customer/purchase count, review quality, and leading products within each merchandise category. <a href="https://github.com/RohanPatil1818/ShopperScope/blob/main/Screenshot%203.png">View full screenshot on GitHub</a></sub></p>
KPI cards: Product Types; Recorded Purchases; Average Review (/5).
Charts: Top five products by average review rating; top products within Clothing, Accessories, Footwear, and Outerwear (two listed outerwear products).
Slicers: Gender, Season, Subscription status, Discount applied.
Metric definitions
Measure	Definition / caution
Revenue	Sum of recorded purchase_amount in USD; not gross profit
Customers	Distinct customer_id; also equals row count in this one-row-per-customer snapshot
Average purchase	Total recorded purchase value / number of records
Average review	Mean of review_rating after filling category-median values
Subscriber / non-subscriber averages	Average purchase filtered by subscription_status
Product types	Distinct item_purchased
Recorded purchases	3,900 dataset records; cannot reconstruct full customer purchase history
Churn rate / LTV / contribution margin	[Insert Exact Metric] — cannot be calculated from supplied snapshot
Power BI validation: The PBIX report layout and visual field bindings were inspected. The embedded measure definitions and refresh connections were not independently executed in Power BI Desktop; verify DAX, slicer cross-filtering, and display totals before publishing.

🔎 Verified Business Insights
The most useful insight is not always the biggest number—it is the decision that number informs. The findings below connect verified customer and purchase metrics to practical questions about merchandising, promotions, subscriptions, and service design.

Finding	Evidence	Commercial meaning
Clothing dominates the category mix	$104,264 across 1,737 purchases; 44.73% of recorded value	Prioritize merchandising and category-level tests
Gender revenue differs mainly by customer count	Male: 2,652 customers / $157,890; Female: 1,248 / $75,191	Male records contribute more total value, while female average spend ($60.25) slightly exceeds male ($59.54)
Subscription is not a higher-ticket guarantee	Subscriber average $59.49 vs non-subscriber $59.87	A $0.38 observed difference is descriptive, not causal
Subscription and discount are strongly confounded	100% of subscribers have Discount Applied=Yes vs 21.92% of non-subscribers	Cannot attribute effects to subscription independent of discount
Discount is common, not proof of uplift	1,677 discounted records (43.00%); average $59.28 vs $60.13 without discount	Test incremental margin rather than assuming discounts increase spending
Express and Standard differ modestly	Express average $60.48; Standard $58.46	Correlation may reflect shopper selection, not shipping-caused basket expansion
Fall has the highest seasonal sum	Fall $60,018 vs Summer $55,777	Plan comparative campaigns; data has season labels, not dates or same-customer time series
Gloves lead average review	3.86/5 after imputation	A product-quality / merchandising follow-up candidate
Where discount usage is most concentrated: Hat 50.00%, Sneakers 49.66%, Coat 49.07%, Sweater 48.17%, and Pants 47.37%. These are shares of purchases marked as discounted, not measured promotional lift; rates were recalculated using decimal arithmetic.

What the evidence suggests: Clothing deserves focused assortment testing; subscription economics need to be separated from discount effects; and shipping differences warrant careful experiments before the business invests in incentives.

Interpretation boundary: This dataset contains no observed churn outcome, customer tenure, transaction date, renewal event, service contact, or margin. No statistically supported churn drivers, lost revenue, or causal profit benefits can be claimed. The New/Returning/Loyal SQL labels describe counts of previous purchases only.

💡 Recommendations & Profitability
Turn observations into controlled, measurable business actions. ShopperScope is most valuable when it helps teams decide what to test next: which offers are worth funding, which categories merit attention, and whether customer-facing perks generate value after their costs.

The recommendations below are experiment proposals grounded in the observed dataset, not claims that a specific intervention has already improved profit or retention.

Priority	Action to test	Evidence-based rationale	How to measure success
1 — Subscription economics	Test membership benefits without stacking discounts	All current subscribers in the dataset are discount-flagged; their average purchase is not higher	Incremental contribution after membership and incentive costs
2 — Apparel merchandising	Test product bundles, cross-sell, and inventory prioritization in Clothing	Clothing contributes 44.73% of recorded purchase value	Basket value, attach rate, contribution margin
3 — Discount governance	Run category/product-level holdout tests and margin-based offer rules	43.00% of records used discounts; lift is unmeasured	Net margin lift vs a non-discount control group
4 — Service design	Test shipping offers by basket tier rather than assuming Express drives spend	Express vs Standard averages differ by $2.02	Incremental purchase value minus delivery subsidy
5 — Customer development	Trial second-/next-purchase reminders for lower-history customers	83 records have exactly one previous purchase	Measured later repeat rate (requires longitudinal orders)
6 — Product experience	Investigate low-rated products and feature highly rated products	Product review comparisons are available	Returns, complaints, purchase conversion, margin
Measuring commercial impact
A promising campaign should create incremental contribution, not merely a higher purchase total. The project offers a starting formula, while transparently marking the financial inputs that the source data does not provide.

Financial model — inputs not supplied:

Incremental net contribution
  = incremental orders × contribution margin per order
    − discounts and incentives − additional servicing/campaign cost

Observed incremental orders = [Insert Exact Metric]
Contribution margin per order = [Insert Exact Metric]
Campaign and shipping costs = [Insert Exact Metric]
Measured churn rate = [Insert Exact Metric]
Customer acquisition cost = [Insert Exact Metric]
Net profit uplift = [Insert Exact Metric]
Use randomized controls or a defensible quasi-experiment. No ROI, retention improvement, or churn reduction has been demonstrated by this dataset.

🚀 How to Run
Explore the work from source data to the final dashboard. The steps below recreate the Python → SQL Server → Power BI workflow on a local machine; replace any database connection settings with your own environment.

Clone your repository:
git clone https://github.com/RohanPatil1818/ShopperScope.git
cd ShopperScope
Create a Python environment and install packages:
python -m venv .venv
# Windows PowerShell: .venv\Scripts\Activate.ps1
# Git Bash: source .venv/Scripts/activate
# macOS/Linux: source .venv/bin/activate
pip install -r requirements.txt
Run notebook: Start Jupyter (jupyter notebook), open notebooks/Customer_Shopping_Behaviour_Analysis.ipynb, and execute the cleaning cells. The packaged notebook reads the data from ../data/raw/customer_shopping_behavior.csv when its working directory is notebooks/. Run the SQL connection/upload cells only after configuring your own SQL Server.
Connect SQL Server: Install Microsoft ODBC Driver 18, create database customer_behaviour, and edit the notebook's server = "localhost" and authentication settings as appropriate. It writes dbo.customers with if_exists='fail'; if the table already exists, manually choose a safe refresh approach instead of blindly rerunning the upload.
Run business queries: Open sql/SQLQuery(Questions-Answer).sql in SSMS, select customer_behaviour, and execute questions after the dbo.customers table is loaded. Update the discount-rate SQL to use 100.0 for decimal division.
Open Power BI: Load powerbi/Customer Analysis - Enhanced.pbix in Power BI Desktop. Update the SQL connection/server if requested, refresh, and validate card totals and slicers. The README displays the three screenshots uploaded at the GitHub repository root (Screenshot 1.png, Screenshot 2.png, Screenshot 3.png).
Reproduction note: The original uploaded ZIP did not contain screenshot image files, but all three dashboard images have now been linked from GitHub. The supplied .xls file was actually CSV text, so the packaged source data is correctly named .csv. The original publisher/source URL is still unknown.

⚠️ Analytical Limitations & Next Steps
A reliable analyst knows where the evidence ends. ShopperScope provides a clear picture of the available customer-shopping snapshot. It does not, on its own, reveal who churned, why a customer behaved a certain way, or whether a campaign would be profitable.

There are 3,900 unique customer IDs and 3,900 rows; this is not a customer-order history that can calculate real repeat rates.
No order timestamps or churn outcomes exist, so recency, survival analysis, RFM recency, churn prediction, and period-over-period growth cannot be validated.
Previous Purchases and Frequency of Purchases are reported attributes rather than independently observed historical orders.
Original publisher/source attribution is missing: [Insert Original Dataset Source URL]. The project copy is linked under Data Resources.
Subscribers are entirely within discount-flagged records, so subscription impact must be tested with controlled comparisons.
Profitability needs product costs, margins, incentive amounts, marketing expense, and acquisition cost.
Future work: obtain time-stamped order history, define inactivity/churn criteria, build genuine cohort retention metrics, add contribution-margin views, then test churn scoring and alerts.
<p align="center"> <strong>Built with curiosity. Grounded in data. Designed for better retail decisions.</strong><br> <sub>Python · pandas · NumPy · SQLAlchemy · pyodbc · Microsoft SQL Server · Power BI</sub> </p>




$0
