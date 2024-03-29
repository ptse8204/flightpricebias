---
title: "Exploratory Data Analysis"
excerpt: "Details of trends and results found in the datasets used in the project"
sitemap: true
permlink: /eda/
layout: single
---
![eda](https://user-images.githubusercontent.com/88422737/221440983-9d4ddf7f-cecb-4956-a573-19ed83a0c0bf.png)
*Image generated by DALL-E*

# Note
This page is still in development, some of the information and findings may be missing. The report provides more details than the website currently, [check the report for the details](https://ptse8204.github.io/flightpricebias/DSC180B_Report.pdf)

## Introduction and Abstracts of Findings

What are the thoughts when you are purchasing an flight ticket? Did you felt that you should be purchased it a bit earlier, or maybe a bit later so that prices would change? Though a lot of people knows that purchasing the fare at the last moment usually yield the highest price, it turns out that for average people, they purchase quite literally a very similar fare across the board. As our reaesrach shows, over 70% of all fares has a fare per mile of 20-30 cents.

We also found that most people are purchasing the median fare, rather than the highest fare or the lowest fare, as expected. These findings here helps aid our reasearch and model building the discover the bias related in the other sections.

Our scope of our data analysis spanned across over 5 years, from 2016 to until the Q3 of 2022 (the most current data when we have conducted the reaserach). In general, we discovered the significant impact of the pandemic to air travel and airfare prices, especially when comparing between pre-pandemic periods (2016-2019) with during the pandemic (2020-2021) and post-pandemic (2021-2022 Q3). Our other analysis found also found:
- Impact of flight distance on airfare
  - Ultra-short flights has a great impact on fare per mile
- Signifcant difference in fares between low-cost carriers and legacy carriers
  - Carriers defined as legacy carriers in our analysis are: American Airlines, Alaska Airlines, Delta Air Lines, United Airlines, Southwest Airlines, Jetblue, and Hawaiian Airlines.
  - Carriers defined as low-cost carriers in our analysis are: Frontier, Spirit and Allegiant Airlines.
- No significant variance between quarters in years
- Flight prices were relatively stable prior to the pandemic, even without factoring in inflation.
- Strong difference between the pricing of the protected groups and privilleged groups shows there are at least degree of some price discrimination.

## Datasets Used
* [Airline Origin and Destination Survey (DB1B) by the _Bureau of Transportation Statistics_](https://www.transtats.bts.gov/Tables.asp?QO_VQ=EFI&QO_anzr=Nv4yv0r%FDb4vtv0%FDn0q%FDQr56v0n6v10%FDf748rB%FD%FLQOEO%FM&QO_fu146_anzr=b4vtv0%FDn0q%FDQr56v0n6v10%FDf748rB)
* [Air Carrier Statistics (Form 41 Traffic)- All Carriers (T-100) (US only segment) by the _Bureau of Transportation Statistics_](https://www.transtats.bts.gov/Tables.asp?QO_VQ=EEE&QO_anzr=Nv4%FDPn44vr4%FDf6n6v56vp5%FD%FLS14z%FDHE%FDg4nssvp%FM-%FDNyy%FDPn44vr45&QO_fu146_anzr=Nv4%FDPn44vr45)
* [Median Household Income and Racial Demographics per Metropolitian Area by the _US Census Bureau_](https://www.census.gov/)

[**Click here for details of the dataset used**](eda_pages/dataset.md)

## Airline Origin and Destination Survey (DB1B) Findings
### Methodology Used for Feature Engineering and Data Cleaning
<details close>
  <summary><b>Processing the DB1B Dataset</b></summary>
<br>
The original Ticket in the DB1B dataset records information on the itinerary level. Since one itinerary might have multiple flights and destinations depending on whether it is a round trip or has multiple stops along the way. Therefore, we merge the Ticket dataset with the Market/Coupon dataset on itinerary ID, and it allows us to look closer into ticket information on an individual flight level. On average for each quarter the combined dataset has around 6 million rows and 26 useful features after we exclude other redundant columns, where each row represents a ticket and its associated information. Details of the columns and variables are available on the project website.

Since the DB1B dataset is build on the ticket level, meaning that the ticket could have segments that represents either be a one-way, a round-trip or a muilt-destination ticket. Therefore, there is not a clearly define destination. We decided to use observe the destination in dataset in based on various assumptions shown below, which are also implemented in our `sparkmanager.py`:
  <table>
    <tr>
      <th>Assumptions</th>
      <th>Method name in module</th>
      <th>Resulting dataset size (in rows)</th>
    </tr>
    <tr>
      <td>The last destination is the real destination</td>
      <td>`default`</td>
      <td>98898392 (total amount of tickets)</td>
    </tr>
    <tr>
      <td>The median point is the real destination</td>
      <td>`midpoint`</td>
      <td>98898392 (total amount of tickets)</td>
    </tr>
    <tr>
      <td>Each segment should be treated separately</td>
      <td>`segment`</td>
      <td>247175573 (all avaiable rows in the dataset)</td>
    </tr>
  </table>
<!-- ||||
|---|---|---|
||||
||||
|||| -->

After our data wrangling work, we immediately found the default approach is problematic because over 60% of the tickets are considered roundtrips. As a result, over 60% of tickets using such an approach results in the same origin and destination. The median approach shows some promising results, however it also means it is losing a lot of information that could be able to be represented in the segments. Therefore, after our experiment on the three approaches, we decided to treat each segment as an individual ticket in our future analysis as it would keep the most information intact.

</details>

<details close>
  <summary><b>CPI Adjustments</b></summary>
  <br>
  To further perform a better and more accurate analysis, we also used the cpi module in Python to change all of our prices into 2022 prices from their respective years. The prices shown below the EDA section are the original number of prices, unless specifically stated of using the cpi adjusted 2022 prices. 
 </details>

### General Statistics Findings
[**Due to page constrains, click here for the general findings**](eda_pages/db1b_general.md)
### Trends and Results Discovered During the Analysis
- [Discrimination in pricing found in based on race](eda_pages/race_discrimination.md)
- Discrimination in pricing found in based on income
Pages still in development, [check the report for the details](https://ptse8204.github.io/flightpricebias/DSC180B_Report.pdf)

## Air Carrier Statistics (T-100) Findings
### Subset of Dataset Used
In this dataset, we are only using the data that is a passenger flight, and filter the dataset using the code that we created in the [`t100.py` module](https://github.com/ptse8204/airlinedatabias/blob/main/src/t100.py). The code also generates statistics a key statistics that we used for our other models – `LOAD_FACTOR`, defined as the ratio of total of passgeners count and total seat count.
### Trends and Results Discovered During the Analysis
Page still in development, [check the report for the details](https://ptse8204.github.io/flightpricebias/DSC180B_Report.pdf)

## US Census Findings
### Subset of Dataset Used
For simplicity, we only used the 2022 data of the Median Household Income and Racial Demographics per Metropolitian Area. The main reason is there is lack of significant difference between the protected groups as a results between this years. As both racial composition and income demographic in the US did not signifcantly shift during the fiver years of our analysis between the metropolitian areas, as low-income and highly racial intergrated areas remain the with the same subset of areas.
### Methodology Used for Feature Engineering and Data Cleaning
#### Connecting the US Census Bureau Data to Bureau of Transportation Statistics Data
As both dataset are not directly linked together, as they lacked a common variable between them. We decided to manually inspect and use `CityMarketID` of the _Bureau of Transportation Statistics_ to link with Statistical Metropolitain Areas defined by the _US Census Bureau_. As shown in [city_id_census.csv](https://github.com/ptse8204/airlinedatabias/blob/main/lookup/city_id_census.csv), we have successfully linked over 750 cities (which a usual DB1B set contains roughly 780 of them) by using this method to perform our analysis.
#### Median Household Income Trends and Results Discovered During the Analysis
Histogram of low and high income groups (may include location if time permits)
#### Racial Demographics Fearture Engineering
From the dataset, in order to identitfy a protected group and a privillage group, we decided to use colored population ratio of the total population to evaluate such.
(histogram of such feature)
(mean, mode, median)
