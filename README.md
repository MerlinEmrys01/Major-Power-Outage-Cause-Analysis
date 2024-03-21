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

<iframe
  src="assets/Counts by Cause Category.html"
  width="900"
  height="800"
  frameborder="0"
></iframe>