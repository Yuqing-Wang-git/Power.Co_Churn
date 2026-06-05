# Power.Co_Churn

## Table of Contents


* [Project Background](#project-background)

* [Project Goals](#project-goals)


* [Project Deep-Dive](#project-deep-dive)

    * [Data Overview](#data-overview)

        *[Client Data](#client-data)

        *[Pricing Data](#pricing-data)


    
* [Churn and price sensitivity  hypothesis](#price-sensitivity-and-churn-hypothesis)

    * [Churn and client profile](#Churn_and_client_profile])

    * [Price and date of modification](#Price_and_date_of_modification])

* [Clarifying Questions, Assumptions, and Caveats](#clarifying-questions-assumptions-and-caveats)
    * [Questions for Stakeholders Prior to Project Advancement](#questions-for-stakeholders-prior-to-project-advancement)
    * [Assumptions and Caveats](#assumptions-and-caveats)



## Project Background
This project delivers a data science solution engineered by **BCG X** for **PowerCo**, an energy utility provider, to address customer attrition (**churn**). 

 PowerCo leadership operates under the hypothesis that **price sensitivity** is the primary driver of churn, assuming that customers migrate to competitors strictly to obtain lower volumetric or fixed utility rates.Therefore, this project directly investigates the validity of the price sensitivity hypothesis against alternative operational and behavioral variables.

Beyond hypothesis verification, the primary aim of this project is to build an end-to-end predictive framework and deliver actionable business recommendations.



## Project Goals

1. **Audits Client Profiles and Historical Pricing**: Audit customer profiles (electricity & gas consumption, sales channels, net margin etc,) and historical pricing structures and trends to handle **anomalies** and isolate **initial behavioral patterns**.

2. **Hypothesis Verification:** Quantifying the statistical correlation between historical price changes and customer exit rates to evaluate the economic viability of rate restructuring.

3. **Formulate Actionable Business Recommendations**: Translate empirical data insights and risk trends into targeted customer retention strategies, allowing PowerCo to protect annual contract value.

## Executive Summary



TBD
TBD
TBD
TBD


## Project Deep-Dive



### Data Overview

<table>
  <tr>
    <td valign="top" width="300" style="border: none;">
      <img src="images/data_structure.png" alt="data structure" width="350">
    </td>
    <td valign="top" style="border: none; padding-left: 20px;">


* The project utilizes two relational datasets(Client Data and Pricing Data) connected via a one-to-many relationship.

* client_data contains 14,606 rows $\times$ 27 columns and price_data contains 193,002 rows $\times$ 8 columns



* Too see detailed information, [Click here](Data/Data_Description.pdf)


    </td>
  </tr>
</table>

<div style="clear: both;"></div>

#### Client Data
To investigate distinct client profiles across the portfolio, we analyze four core metrics—electricity consumption, gas consumption, subscribed power, and net margin. Together, these define a client's overall footprint, highlighting how heavily a small cohort of enterprise accounts weights total operational and financial demand.


![portfolio_baseline_percentages](images/portfolio_baseline_percentages.png)

![client distributions](images/client_distributions.png)
  Key Finding


* **The Baseline Churn Rate:** The overall churn rate for the portfolio sits at 9.7%，which means any customer segment or pricing pocket with an attrition rate significantly higher than 9.7% represents an elevated risk zone.

* **The Product Mix Structure**: The portfolio is heavily dominated by single-utility clients, with 81.8% of the customer base subscribing to electricity-only plans. Dual-fuel clients (gas subscribers) account for only 18.2% of the portfolio, confirming that gas functions as a smaller, auxiliary product line.


* **Structural Skewness:** All operational and financial features exhibit heavy right-skewness. This indicates that PowerCo's portfolio is heavily built on Small and Medium Enterprises (SMEs), with a long tail of high-volume industrial outliers.

* **Financial Risk Implication:** Most customers generate low margins. This makes overall company profitability highly vulnerable to the loss or churn of a few high-value accounts.










#### Pricing Data

To investigate price fluctuations and seasonality, we must plot fixed fees and variable rates separately because their completely.By applying Min-Max Normalization to scale all values between $0.0$ and $1.0$, 
we can directly compare the exact timing and velocity of price changes across off-peak, peak, and mid-peak windows.
![Price trend fix ](images/price_trend_fix.png)



![Price trend var ](images/price_trend_var.png)

key findings: 

* **Controlled Growth in Fixed Costs** : Fixed fees across all time periods show a steady, near-linear upward trajectory throughout 2015. 

* **The Summer Collapse in Variable Rates :** variable unit energy rates expose a massive pricing anomaly around August, where off-peak and peak rates crashed from their annual highs ($1.0$) down to their annual lows ($0.0$). Meanwhile, mid-peak rates decoupled entirely from this crash, continuing a steady climb through December.

### price sensitivity and churn hypothesis

To address the client's request, we are testing our churn and price sensitivity hypotheses. 

Unfortunately, we cannot directly link cost changes with churn behavior; commercial B2B clients operate under fixed-term agreements and rarely exit mid-cycle due to steep penalties. Customer attrition is structurally restricted to the natural expiration of the contract (date_end). Consequently, price dissatisfaction acts as a lagging indicator, suppressing the visible decision to churn until the official renewal window opens.

However, we can discover patterns by analyzing churn rates against client profiles. Furthermore, we can evaluate the time metrics of price fluctuations alongside the exact date clients last modified their product subscription (date_modif_prod) to see if plan updates correlate with long-term retention.

#### Churn and client profile

![client_margin_vs_features_scatter](images/client_margin_vs_features_scatter.png)

![client_margin_vs_features_filtered_scatter](images/client_margin_vs_features_filtered_scatter.png)

key findings: 

* **High-Value Churn Vulnerability** : The unfiltered view exposes severe revenue risk, dominated by a single churned client at the absolute top of the scale. This single account generates nearly $25,000 in net margin, proving that the loss of just one high-margin outlier can financially equal losing hundreds of standard core clients combined.  

* **Uniform Churn Risk Across Margin Levels:** Churn is not a behavior exclusive to low-margin or unprofitable clients. The red "Churned" data points are distributed evenly alongside the blue "Retained" points from the very bottom of the margin scale up past $1,000, meaning a client's specific profit margin profile does not inherently link with their churning behavior




#### Price and date of modification

We need to define the scope carefully, since the price history data only covers the year of 2015, clients with `date_end` prior to Jan 1st, 2016 should not be included. Besides, We need to mostly pay attention to those who had modified their product subscription during 2015 when investigating client behavior with price fluctuation. 

![Modification_Timelines_chart](images/Modification_Timelines_chart.png)

![Modification_Timelines](images/Modification_Timelines.png)


##### 1:  The 2015 Modification Spike

* **Observation:** there is the massive concentration of account modification activity in a single: 34.1% or more of clients last modified their product subscriptions during 2015, and only 14.9% (2,176 clients) hold contracts that were last modified in any other year (spanning from 2003 up to 2016).

* **What this means:** the 2015 spike  indicates a major structural event occurred in 2015. This was likely a forced migration, a massive pricing update, a company-wide campaign, or a large batch of legacy contracts all expiring simultaneously, forcing clients to select new terms.


##### 2. The Churn Rate Breakdown

* **Observation:**
Modified in 2015: 9.8% Churn Rate (1,215 lost clients / 11,215 retained).
Other Years: 9.4% Churn Rate (204 lost clients / 1,972 retained)

* **What this means:** Clients who touched their accounts in 2015 are churning at a slightly higher rate than clients sitting on older (or newer) legacy plans.


![combined_modification_and_fixed_price_trends](images/combined_modification_and_fixed_price_trends.png)

##### 1. The Fixed Price Chart

* **Observation:** Overall, the modification volume (bars) strongly correlates with the volatility of fixed capacity rates (lines). Following eight months of relative price stability and negligible account activity, a sudden, sharp spike in the Peak fixed rate in September triggered a massive, sustained surge in modifications throughout Q4.

* **What it means:** This lagging correlation proves that account attrition is highly event-driven; enterprise clients will passively accept stable base rates but will immediately and defensively renegotiate or downgrade their contracts in response to sudden pricing shocks.


![combined_modification_and_price_trends](images/combined_modification_and_price_trends.png)

##### 2. The variable Price Chart

* **Observation:**

The overall volatility in pricing trends does not show a strong or consistent correlation with the volume of account modifications for that year.

* **What it means:**
The variable price doesn't seem like a trigger for product plan modification. 

![combined_correlation_dashboard](images/combined_correlation_dashboard.png)


##### 1. Prices and modification date correlation heatmap


##### 2. Prices and churn correlation heatmap

### Conclusion: 


## Recommendations
Based on your findings above, list your strategic, actionable recommendations for the business.

## Clarifying Questions, Assumptions, and Caveats
An introductory sentence regarding the gaps in the data or initial scope constraints.

### Questions for Stakeholders Prior to Project Advancement
List out questions you would ask data engineering, product managers, or business leaders before taking this project to the next step.




testing changes

### Assumptions and Caveats
Detail the data limitations, missing timelines, or specific contexts assumed during your EDA.