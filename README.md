# Watt's Up!
## A US Major Power Outage Analysis

This is a comprehensive project reflecting our skills in data cleaning & analysis, missingness analysis & imputation, hypothesis testing, prediction model building and model fairness analysis under the course DSC 80 at UC San Diego.

By Yiran Zhao and Chengxi Xu

## **Introduction**

In today's world, reliable electricity is essential. But what happens when the lights go out?

Our project explores an extensive dataset that documents major power outage events across the continental United States from January 2000 to July 2016. This dataset provides a comprehensive look at the severe weather-induced power outages along with various geographical, climatic, and economic characteristics of the affected states. With a total of 55 variables, the dataset offers a rich repository for analyzing the patterns and causes behind significant power outages that impact at least 50,000 customers or result in an unplanned firm load loss of at least 300 MW, defined as a major power outage by the Department of Energy.

Our main investigation question is: <strong><u>Are outage durations significantly impacted by the outage causes?</u></strong>

Now, why should this question matter to us and to our readers? Understanding the correlations between outage causes and their durations can have real-world applications, enhancing our preparedness and response strategies to minimize future power disruptions. Given that most people experience at least one power outage annually, identifying specific causes that significantly affect outage duration could inform better prevention and mitigation strategies, potentially reducing the frequency and severity of future outages.

We will be using the data `outage`, it contains 1535 rows with the first row being the unit for each column, meaning that there are 1534 recorded major outages throughout the US from January 2000 to July 2016 with relevant columns for our analysis including `'U.S._STATE'`, `'CAUSE.CATEGORY'`, `'OUTAGE.DURATION'`, `'DEMAND.LOSS.MW'`, `'CLIMATE.REGION'`, and `'CLIMATE.CATEGORY'`. These columns are crucial as they allow us to dissect the data through the lens of our central question, examining how different variables ranging from the cause of the outage to the climate conditions contribute to the duration of power outages.

Here are the relevant columns for our study:

- `'U.S._STATE'`: Represents all the states in the continental U.S., offering geographic context to each outage event.
- `'CAUSE.CATEGORY'`: Categories of all events causing the major power outages, central to our investigation.
- `'OUTAGE.DURATION'`: Duration of outage events (in minutes), key to understanding the impact of each outage.
- `'DEMAND.LOSS.MW'`: Amount of peak demand lost during an outage event (in Megawatts), indicating the severity of the outage.
- `'CLIMATE.REGION'`: U.S. Climate regions as specified by the National Centers for Environmental Information, providing climatic context.
- `'CLIMATE.CATEGORY'`: Represents the climate episodes corresponding to the years, offering insights into how weather conditions influence outages.

<a href="https://www.sciencedirect.com/science/article/pii/S2352340918307182" target="_blank">Note: Descriptions of the relevant columns are taken from this dataset's official publisher. Click here to see the full dataset and more detailed descriptions for each variable.</a>

## **Data Cleaning and Exploratory Data Analysis**

### Data Cleaning 

We began our project by changing the `outage` dataset type from `xlsx` to `csv`, and imported data from the corrected file `outage.csv` into a pandas DataFrame. We identified that the first row contained units rather than actual data, so we removed it for clarity and accuracy. To ensure we only work with relevant data and to avoide doing unnecessary computation on irrelevant columns, we made a new dataframe called `clean` that only contains columns that pertained to our analysis: `'U.S._STATE'`, `'CAUSE.CATEGORY'`, `'OUTAGE.DURATION'`, `'DEMAND.LOSS.MW'`, `'CLIMATE.REGION'`, and `'CLIMATE.CATEGORY'`. 

During the process, we noticed that some numeric columns like `OUTAGE.DURATION` and `DEMAND.LOSS.MW` had entries in string format that cannot be used for quantitative analysis, so we used `pd.to_numeric` to transform them into usable numeric data. We also addressed the inconsistent representation of some missing values. Columns like `'OUTAGE.DURATION'` and `'DEMAND.LOSS.MW'` should not have entries with `'0'` values because all data in the dataset should be a record of major power outage, thus all values in `'OUTAGE.DURATION'` and `'DEMAND.LOSS.MW'` should be greater than `0`. We replaced `'0'` with `NaN` for consistency, and will analyze the missingness in later steps.

We also discovered that there are some values in the `'DEMAND.LOSS.MW'` column that are smaller than 300 megawatts, which does not make sense in this dataset as it contains only major power outages with at least 300 MW demand loss, therefore we treated those values as incorrect entries and converted them into `NaN` as well for consistency, and will be addressing them later on in the project.

Lastly, we renamed the `'U.S._STATE'` column to `'US.STATE'` to align with the consistent naming conventions used across the dataset. The first five rows of the cleansed dataframe can be seen below using `clean.head()`:

 <style>
.markdown-table {
  font-size: 90%; 
  width: 100%; 
}
.markdown-table th {
  background-color: #f2f2f2; 
}
.markdown-table tr:nth-child(even) {
  background-color: #f9f9f9; 
}
.markdown-table tr:nth-child(odd) {
  background-color: #ffffff;
}
</style>

<table class="markdown-table">
  <thead>
    <tr>
      <th>US.STATE</th>
      <th>CAUSE.CATEGORY</th>
      <th>OUTAGE.DURATION</th>
      <th>DEMAND.LOSS.MW</th>
      <th>CLIMATE.REGION</th>
      <th>CLIMATE.CATEGORY</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Minnesota</td>
      <td>severe weather</td>
      <td>3060</td>
      <td>nan</td>
      <td>East North Central</td>
      <td>normal</td>
    </tr>
    <tr>
      <td>Minnesota</td>
      <td>intentional attack</td>
      <td>1</td>
      <td>nan</td>
      <td>East North Central</td>
      <td>normal</td>
    </tr>
    <tr>
      <td>Minnesota</td>
      <td>severe weather</td>
      <td>3000</td>
      <td>nan</td>
      <td>East North Central</td>
      <td>cold</td>
    </tr>
    <tr>
      <td>Minnesota</td>
      <td>severe weather</td>
      <td>2550</td>
      <td>nan</td>
      <td>East North Central</td>
      <td>normal</td>
    </tr>
    <tr>
      <td>Minnesota</td>
      <td>severe weather</td>
      <td>1740</td>
      <td>&lt;NA&gt;</td>
      <td>East North Central</td>
      <td>warm</td>
    </tr>
  </tbody>
</table>


### Univariate Analysis

Since our main question is centered around power outage durations and causes, we will be doing univariate analysis on the distributions of `'CAUSE.CATEGORY'` and `'OUTAGE.DURATION'`.

<iframe
  src="assets/Counts by Cause Category.html"
  width="900"
  height="1000"
  frameborder="0"
></iframe>

This bar chart shows the distribution of `'CAUSE.CATEGORY'`, and we could tell from the chart that within the recoreded major power outages, `severe weather`, `intentional attack`, and `system operability disruption` are the top 3 most popular cuases, with `severe weather` being the most significant cause.s



<iframe
  src="assets/Duration Categories Distribution.html"
  width="900"
  height="1000"
  frameborder="0"
></iframe>




aggregate table:
<style>
.markdown-table {
  font-size: 90%;
  width: 100%;
}
.markdown-table th {
  background-color: #f2f2f2;
}
.markdown-table tr:nth-child(even) {
  background-color: #f9f9f9;
}
.markdown-table tr:nth-child(odd) {
  background-color: #ffffff;
}
</style>

<table class="markdown-table">
  <thead>
    <tr>
      <th>CAUSE.CATEGORY</th>
      <th>mean</th>
      <th>median</th>
      <th>std</th>
      <th>min</th>
      <th>max</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>equipment failure</td>
      <td>1850.56</td>
      <td>224</td>
      <td>10618.28</td>
      <td>1</td>
      <td>78377</td>
    </tr>
    <tr>
      <td>fuel supply emergency</td>
      <td>13484.03</td>
      <td>3960</td>
      <td>21012.32</td>
      <td>1</td>
      <td>108653</td>
    </tr>
    <tr>
      <td>intentional attack</td>
      <td>521.93</td>
      <td>92.5</td>
      <td>1561.35</td>
      <td>1</td>
      <td>21360</td>
    </tr>
    <tr>
      <td>islanding</td>
      <td>200.55</td>
      <td>77.5</td>
      <td>306.11</td>
      <td>1</td>
      <td>1254</td>
    </tr>
    <tr>
      <td>public appeal</td>
      <td>1468.45</td>
      <td>455</td>
      <td>2032.24</td>
      <td>30</td>
      <td>11867</td>
    </tr>
    <tr>
      <td>severe weather</td>
      <td>3899.71</td>
      <td>2464</td>
      <td>5144.38</td>
      <td>1</td>
      <td>49320</td>
    </tr>
    <tr>
      <td>system operability disruption</td>
      <td>747.09</td>
      <td>222</td>
      <td>2249.38</td>
      <td>5</td>
      <td>23187</td>
    </tr>
  </tbody>
</table>
