# 🍜 Customer & Loyalty Behaviour Analysis 



## 📌 Project Overview
This project analyses customer transaction, menu, and membership data for **Danny’s Diner**, a small Japanese restaurant, to understand customer behaviour, spending patterns, product preferences, and the impact of a loyalty program.

Using **SQL**, the project answers business-focused questions around visit frequency, customer value, popular items, and loyalty incentives. It reflects real-world analytical tasks expected in **data analyst internship roles**, including customer segmentation, rule-based metrics, and post-membership behavioural analysis.



## 📂 Repository Structure
- **README.md** — Project documentation  
- **Case_Study_Solution.md** — Business questions, SQL queries, results, and analytical insights  
- **Members.csv** — Customer membership data (join dates)  
- **Menu.csv** — Menu items and pricing details  
- **Sales.csv** — Transaction-level customer order data  



## 🎯 Objectives
- Analyse customer visit frequency and engagement patterns  
- Measure total and pre-membership customer spending  
- Identify the most popular menu items overall and per customer  
- Evaluate the effectiveness of the loyalty program  
- Apply rule-based and time-based reward logic  
- Create clean, interpretable datasets for business inspection  



## 🧩 Key Dimensions
- **Customer ID**  
- **Order date**  
- **Product ID**  
- **Product name**  
- **Membership status** (pre / post membership)  
- **Loyalty join date**  



## 📏 Key Metrics
- Total customer spend  
- Distinct visit days  
- Purchase frequency per product  
- Pre- vs post-membership spending  
- Loyalty points earned  
- Post-membership purchase ranking  



## 🔧 Tools & Technologies

### SQL (MySQL)
- Multi-table `JOIN`s  
- Aggregations and `DISTINCT` counts  
- `CASE`-based business logic  
- Window functions (`ROW_NUMBER`, `RANK`)  
- Time-based filtering and rule prioritization  
- Loyalty points modelling  



## 🧠 Methodology

### 1. Data Understanding & Validation
- Inspected table structures and relationships  
- Verified consistency between transactions, menu items, and membership dates  
- Confirmed business rules for loyalty points and promotional windows  

### 2. SQL Analysis
Key analyses performed:
- Total and pre-membership customer spending analysis  
- Visit frequency using distinct visit days  
- Identification of most popular menu items  
- Customer-level preference analysis with tie handling  
- Loyalty points calculation with product-based multipliers  
- Time-bound loyalty promotion analysis  
- Ranking of post-membership purchases using window functions  

### 3. Dataset Creation
- Built enriched transaction-level datasets combining:
  - Orders  
  - Product details  
  - Membership status  
  - Post-membership purchase rankings  
- Designed outputs suitable for non-technical stakeholders  



## 🔍 Key Insights
- High-value customers can be identified through both spending and engagement frequency  
- Ramen emerges as a consistently popular menu item across customers  
- Loyalty incentives significantly affect reward outcomes beyond total spend  
- Purchase timing during promotional windows strongly influences points accumulation  
- Post-membership ranking enables deeper analysis of customer retention behaviour  


## 🧩 Skills Demonstrated
- SQL querying and relational data modelling  
- Window functions and analytical ranking logic  
- Business-rule implementation and validation  
- Customer behaviour and loyalty analysis  
- Translating query outputs into actionable business insights  


