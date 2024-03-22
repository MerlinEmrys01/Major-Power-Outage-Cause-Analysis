# Watt's Up!
## A US Major Power Outage Analysis

This is a comprehensive project reflecting our skills in data cleaning & analysis, missingness analysis & imputation, hypothesis testing, prediction model building and model fairness analysis under the course DSC 80 at UC San Diego.

By Yiran Zhao and Chengxi Xu

## **Introduction**

In today's world, reliable electricity is essential. But what happens when the lights go out?

Our project explores an extensive dataset that documents major power outage events across the continental United States from January 2000 to July 2016. This dataset provides a comprehensive look at the severe weather-induced power outages along with various geographical, climatic, and economic characteristics of the affected states. With a total of 55 variables, the dataset offers a rich repository for analyzing the patterns and causes behind significant power outages that impact at least 50,000 customers or result in an unplanned firm load loss of at least 300 MW, defined as a major power outage by the Department of Energy.

Our main investigation question is: <strong><u>Are outage durations significantly impacted by the outage causes?</u></strong>

Now, why should this question matter to us and to our readers? Understanding the correlations between outage causes and their durations can have real-world applications, enhancing our preparedness and response strategies to minimize future power disruptions. Given that most people experience at least one power outage annually, identifying specific causes that significantly affect outage duration could inform better prevention and mitigation strategies, potentially reducing the frequency and severity of future outages.

We will be using the data `outage`, it contains 1535 rows with the first row being the unit for each column, meaning that there are 1534 recorded major outages throughout the US from January 2000 to July 2016 with relevant columns for our analysis including `'U.S._STATE'`, `'CAUSE.CATEGORY'`, `'OUTAGE.DURATION'`, `'DEMAND.LOSS.MW'`, `'CLIMATE.REGION'`, and `'CLIMATE.CATEGORY'`. These columns are crucial as they allow us to dissect the data with a focus on our main question, examining how different variables ranging from the cause of the outage to the climate conditions contribute to the duration of power outages.

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

We began our project by changing the `outage` dataset type from `xlsx` to `csv`, and imported data from the corrected file `outage.csv` into a pandas DataFrame. We identified that the first row contained units rather than actual data, so we removed it for clarity and accuracy. To ensure we only work with relevant data and to avoid doing unnecessary computation on irrelevant columns, we made a new dataframe called `clean` that only contains columns that are applicable to our analysis: `'U.S._STATE'`, `'CAUSE.CATEGORY'`, `'OUTAGE.DURATION'`, `'DEMAND.LOSS.MW'`, `'CLIMATE.REGION'`, and `'CLIMATE.CATEGORY'`. 

During the process, we noticed that some numeric columns like `OUTAGE.DURATION` and `DEMAND.LOSS.MW` had entries in string format that could not be used for quantitative analysis, so we used `pd.to_numeric` to transform them into usable numeric data. We also addressed the inconsistent representation of some missing values. Columns like `'OUTAGE.DURATION'` and `'DEMAND.LOSS.MW'` should not have entries with `'0'` values because all data in the dataset should be a record of a major power outage, thus all values in `'OUTAGE.DURATION'` and `'DEMAND.LOSS.MW'` should be greater than `0`. We replaced `'0'` with `NaN` for consistency, and will analyze the missingness in later steps.

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

This bar chart shows the distribution of `'CAUSE.CATEGORY'`:
<iframe
  src="assets/Counts by Cause Category.html"
  width="800"
  height="440"
  frameborder="0"
></iframe>

- We could tell from the chart that within the recorded major power outages, `severe weather`, `intentional attack`, and `system operability disruption` were the top 3 most popular causes, with `severe weather` being the most significant cause.

This pie chart shows the distribution of `'OUTAGE.DURATION'`. Since there were so many unique duration lengths, we engineered a feature called `Duration Category` and categorized the durations into 4 different levels, `Short`, `Medium`, `Long`, and `No Data`:

- Outages in the `Short` category last up to an hour (60 minutes)
- Outages in the `Medium` category last up to a day (1440 minutes)
- Outages in the `Long` category last more than a day (more than 1440 minutes)
- Outages in the `No Data` category have no recorded duration in the given dataset

<iframe
  src="assets/Duration Categories Distribution.html"
  width="800"
  height="440"
  frameborder="0"
></iframe>

- From this pie chart we could conclude that most of the recorded major power outages in this dataset have either `Medium` or `Long` outage durations.


### Bivariate Analysis

In our bivariate analysis, we will be using a box plot to see if there's a significant difference in average outage duration given different cause categories.

<iframe
  src="assets/Outage Duration by Cause Category.html"
  width="800"
  height="440"
  frameborder="0"
></iframe>

- From the box plot, we could tell that different cause categories have different average, median, and maximum durations, which might be a hint to suggest that there's a correlation between `cause category` and `outage duration`.

### Interesting Aggregates

We used `clean.groupby('CAUSE.CATEGORY')['OUTAGE.DURATION'].agg(['mean', 'median', 'std', 'min', 'max']).reset_index()` to find the `mean`, `median`, `standard deviation`, `minimum`, and `maximum` duration of each cause category, and organized them in an aggregate table:

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

From the table we can confirm that `fuel supply emergency` has the highest mean duration, but at the same time it also has the highest standard deviation, which means the data is more spread out. One interesting fact is that `public appeal` has the highest minimum outage duration of 30 minutes out of all other causes.

## **Assessment of Missingness**

### NMAR Analysis

When we examined the `'CLIMATE.CATEGORY'` column, we discovered a pattern of missing data that falls under Not Missing At Random (NMAR). It's crucial to recognize that the collection of climate category data operates independently from other outage related information. This independence means the presence or absence of climate category data does not depend on other variables/columns such as the specifics of the outage or oceanic El Niño/La Niña (ONI) index at the time of outage. So the lack of climate category data is directly linked to `'CLIMATE.CATEGORY'` itself rather than the observed data.

To validate our hypothesis that the missing `'CLIMATE.CATEGORY'` data was NMAR, we conducted MAR permutation testing with seemingly correlated columns like `'OUTAGE.DURATION'`, `'CLIMATE.REGION'`, `'DEMAND.LOSS.MW'`, etc. This process was aimed at identifying any potential relationships between the missing climate category instances and other features within the dataset. 

One example of MAR permutation testing is below:
<iframe
  src="assets/NMAR Example ('CLIMATE.CATEGORY', 'DEMAND.LOSS.MW').html"
  width="800"
  height="440"
  frameborder="0"
></iframe>

- This is an example of MAR test between `climate category` and `demand loss`. 
- Null Hypothesis: Missingness of `'CLIMATE.CATEGORY'` does NOT depend on `'DEMAND.LOSS.MW'`
- Alternative Hypothesis: Missingness of `'CLIMATE.CATEGORY'` depends on `'DEMAND.LOSS.MW'`
- The p-value for this test is around `0.703`, which suggests that we `fail the reject` the null hypothesis at 5% significance level, and the two columns are not dependent on each other.

The outcomes of this testing reinforced our initial understanding, revealing no significant association between the missing `'CLIMATE.CATEGORY'` data and other variables, therefore the missing data is NMAR.

With the above evidence, we recognized that the missing climate category data could not be accurately imputed from available data, so we opted to exclude the 9 rows featuring missing `'CLIMATE.CATEGORY'` data from our analysis to prevent the introduction of bias into our study and ensure the precision of our findings.

### Missingness Dependency

The missingness of `'OUTAGE.DURATION'` and `'DEMAND.LOSS.MW'` both depend on `'CAUSE.CATEGORY'`. We will be using the `Total Variation Distance` (TVD) as our test statistic to see if the selected columns are Missing At Random (MAR).

#### Testing whether `'OUTAGE.DURATION'` is MAR that depends on `'CAUSE.CATEGORY'`
- Null Hypothesis: Missingness of `'OUTAGE.DURATION'` does NOT depend on `'CAUSE.CATEGORY'`
- Alternative Hypothesis: Missingness of `'OUTAGE.DURATION'` depends on `'CAUSE.CATEGORY'`

<iframe
  src="assets/NMAR Example ('CAUSE.CATEGORY', 'OUTAGE.DURATION').html"
  width="800"
  height="440"
  frameborder="0"
></iframe>

- The p-value is `0.0`, suggesting that there is no significant evidence to say the missingness of `'OUTAGE.DURATION'` depends on `'CAUSE.CATEGORY'`, so we reject the null hypothesis here at 5% significance level.

#### Testing whether `'DEMAND.LOSS.MW'` is MAR that depends on `'CAUSE.CATEGORY'`
- Null Hypothesis: Missingness of `'DEMAND.LOSS.MW'` does NOT depend on `'CAUSE.CATEGORY'`
- Alternative Hypothesis: Missingness of `'DEMAND.LOSS.MW'` depends on `'CAUSE.CATEGORY'`

<iframe
  src="assets/NMAR Example ('CAUSE.CATEGORY', 'DEMAND.LOSS.MW').html"
  width="800"
  height="440"
  frameborder="0"
></iframe>

- Again, the p-value is `0.0`, suggesting that there is no significant evidence to say the missingness of `'DEMAND.LOSS.MW'` depends on `'CAUSE.CATEGORY'`, so we reject the null hypothesis here at 5% significance level.

### Imputation

During our data analysis, we identified that the missing data within the `'OUTAGE.DURATION'` and `'DEMAND.LOSS.MW'` columns occurs randomly, known as MAR (Missing At Random). Given this insight, we chose probabilistic imputation to fill in these gaps.

**Why Probabilistic Imputation?**

Probabilistic imputation was chosen for its ability to generate multiple plausible values for each missing entry that can mirror the distribution observed in our dataset. This method is well suited to our situation for a few reasons:

- It maintains the richness of our dataset by avoiding oversimplifications that might arise from single value imputations.
- It utilizes the existing data patterns to make informed estimations, so that our imputed values are as realistic and accurate as possible.
- It preserves the integrity and structure of our dataset, laying a solid foundation for subsequent analyses in the later parts of this project.

## **Hypothesis Testing**

We want to test whether the causes of power outage have significant impact on outage duration. In order to do so, we ran permutation tests on all causes with the `'OUTAGE.DURATION'` column.

**Null Hypothesis**: Causes of power outage have no significant effect on outage duration length.

**Alternative Hypothesis**: Causes of power outage have significant effect on outage duration length.

**Test Statistic**: Two-Tailed Test Difference In Means

**Significance Level**: standard 5% (0.05)

**p-value**: 0.0 for almost all tests except 0.664 for `operability disruption`

**Conclusion**: Almost all causes (except `operability disruption`) have a p-value of 0.0, which is smaller than our significance level 0.05, therefore it is safe to reject the null hypothesis, and we can conclude that the causes of power outage may have significant effect on outage duration length.

<iframe
  src="assets/Hypothesis.html"
  width="800"
  height="440"
  frameborder="0"
></iframe>





## **Framing a Prediction Problem**

Our goal in this prediction section is: **We want to predict the possible cause of a major power outage given some relevant information.**, thus this prediction question is a **multiclass classification** question.

We will be starting with a basic baseline **classifier** model that uses a **decision tree** model with 2 relevant columns, and hope to improve our model with an upgrade to **random forest** with 4 relevant columns in total.

Columns involved in the prediction process are:
- `'CAUSE.CATEGORY'` (We are **predicting** this)
- `'OUTAGE.DURATION'` (You can time the outage duration before knowing the cause of it)
- `'DEMAND.LOSS.MW'` (Many calculate the demand loss before figuring out the cause of outage because that can take a while)
- `'CLIMATE.REGION'` (You would know this at the time of outage)
- `'CLIMATE.CATEGORY'` (You would know this at the time of outage)

All columns that we are using to predict `'CAUSE.CATEGORY'` listed above are information that can be gathered before knowing what the cause of the power outage is and following the rule of “time of prediction”.

**Response Variable**: `'CAUSE.CATEGORY'`

In our project, we have identified `'CAUSE.CATEGORY'` as the key response variable for prediction, recognizing its substantial implications for effectively managing and mitigating the effects of major power outages. This foresight allows for a more strategic allocation of resources, enhances communication, safety protocols, and most importantly, aids in minimizing the length and impact of outages. At last, the ability to anticipate the cause of power outages empowers us to better safeguard our communities, <u>ensuring that no one is left in the dark, metaphorically and literally.</u>

**Prediction Evaluation Metric**: Accuracy Score using `.score()`

For evaluating our model, we chose to utilize the default `.score()` method, which provides the accuracy score. This metric measures the <u>ratio of correct predictions to the total predictions.</u>

Accuracy is particularly useful for our models' assessments due to its simple definition and easy application. If we are presenting our prediction to non-technical audience, it would be important to choose a metric that they could understand.

It also offers a baseline insight into the model’s effectiveness. However, we recognize accuracy's limitations in imbalanced datasets, where it might not fully capture the model's performance nuances. Thus, while accuracy was chosen for its initial clarity on the model's predictive capabilities, we remain open to incorporating more nuanced metrics in the future like Precision, Recall, and F1 Score for a comprehensive evaluation, depending on the dataset specifics and our analysis objectives.

**For comparison consistency, we will be using the same unseen and seen datasets for both the baseline model and the final model so we could compare their accuracy score on the same unit.**

## **Baseline Model**

Our baseline model is a **multiclass decision tree** used to predict the `'CAUSE.CATEGORY'` with 2 columns: `'OUTAGE.DURATION'` and `'DEMAND.LOSS.MW'`.

Both `'OUTAGE.DURATION'` and `'DEMAND.LOSS.MW'` are **quantitative** columns, we performed imputation on these features to handle any missing values, ensuring that our dataset was complete and ready for analysis. We purposely left those two variables as it is without performing any feature engineering to compare our baseline model with the improved one later. This baseline model is intended to be a very simple model to be compared to the final model.

To create our baseline model, we split our imputed dataset into training and testing sets using the basic `train_test_split()` method and ensured to exclude the response variable `'CAUSE.CATEGORY'` from X, the independent variable. Note that the same training and testing set will be used to train and test both the baseline and final prediction models.

To train our baseline model, we created a `pipeline` that only selected the `'OUTAGE.DURATION'` column and the `'DEMAND.LOSS.MW'` column. After training the decision tree classifier on the training set after applying the pipeline, we evaluated its performance on both the training and testing sets using `.score()`:

**Training Score/Accuracy**: 0.9921259842519685

**Testing Score/Accuracy**: 0.7617801047120419

The training and testing scores seemed quite different, as the training score was almost perfect and the testing score only reached about 76%. This is a sign that the prediction model **overfitted** on the training set, <u>reducing its generalization ability.</u>

## **Final Model**

To improve the accuracy of our prediction, we thought of a couple of steps to experiment to see which features would increase the accuracy score and make our predictions better.

First, we recognized that decision trees are very prone to overfitting, and in order to solve this problem, we updated our model to a **Random Forest**.

We wanted to add 2 more feature columns to improve the prediction accuracy, and we chose the following 2 categorical columns `'CLIMATE.REGION'` and `'CLIMATE.CATEGORY'`, because we thought they might have some correlation with one of the causes `severe weather` since weather and climate are very correlated.

In order to use those two categorical variables, we performed the following feature engineering:

`'CLIMATE.CATEGORY'`: only contains `'Warm'`, `'Cold'` or `'Normal'`
- it is an **ordinal** categorical variable that can be represented by a list of meaningful ordered numbers, therefore we performed <u>ordinal encoding</u> and added it as a feature in our prediction model

`'CLIMATE.REGION'`: since this variable describes the climate region of the outage, there is no order between its unique values
- it is a **nominal** categorical variable that can be made useful after performing <u>One Hot Encoding</u>

The resulting dataframe looks like this:

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
      <th>CLIMATE.REGION == East North Central</th>
      <th>CLIMATE.REGION == Central</th>
      <th>CLIMATE.REGION == South</th>
      <th>CLIMATE.REGION == Southeast</th>
      <th>CLIMATE.REGION == Northwest</th>
      <th>CLIMATE.REGION == Southwest</th>
      <th>CLIMATE.REGION == Northeast</th>
      <th>CLIMATE.REGION == West North Central</th>
      <th>CLIMATE.REGION == West</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Minnesota</td>
      <td>severe weather</td>
      <td>3060</td>
      <td>850</td>
      <td>East North Central</td>
      <td>2</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <td>Minnesota</td>
      <td>intentional attack</td>
      <td>1</td>
      <td>455</td>
      <td>East North Central</td>
      <td>2</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <td>Minnesota</td>
      <td>severe weather</td>
      <td>3000</td>
      <td>1250</td>
      <td>East North Central</td>
      <td>3</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <td>Minnesota</td>
      <td>severe weather</td>
      <td>2550</td>
      <td>746</td>
      <td>East North Central</td>
      <td>2</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <td>Minnesota</td>
      <td>severe weather</td>
      <td>1740</td>
      <td>700</td>
      <td>East North Central</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>

After feature engineering, we tried the same steps as our baseline model, except this time using a Random Forest, and got:

**Training Score/Accuracy**: 0.9991251093613298

**Testing Score/Accuracy**: 0.7329842931937173

The testing score only improved very slightly, which has no meaning in this case, and there is an increase in overfitting, therefore we decided to drop these two columns and only focus on our original columns `'OUTAGE.DURATION'` and `'DEMAND.LOSS.MW'`.

We carefully reviewed the data we have, and realized that we actually don't always have a good amount of data in every cause category for accurate prediction:

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
      <th>Count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>equipment failure</td>
      <td>57</td>
    </tr>
    <tr>
      <td>fuel supply emergency</td>
      <td>50</td>
    </tr>
    <tr>
      <td>intentional attack</td>
      <td>418</td>
    </tr>
    <tr>
      <td>islanding</td>
      <td>46</td>
    </tr>
    <tr>
      <td>public appeal</td>
      <td>69</td>
    </tr>
    <tr>
      <td>severe weather</td>
      <td>759</td>
    </tr>
    <tr>
      <td>system operability disruption</td>
      <td>126</td>
    </tr>
  </tbody>
</table>

To solve this, we decided to drop some outliers, which include outliers that fall outside of roughly 99% Confidence Interval, and those whose outage duration is under 10 minutes because a duration that's way too short is not helpful for us to identify a trend between duration and cause. It is ok to drop duration under 10 minutes because as we see from the **Univariate Analysis** part, the majority of the duration is greater than 1440 minutes, and even the durations labeled `short` are still within the range of an hour (60 minutes), so dropping rows under 10 minutes won't take out too much data.

After dropping the outliers, we introduced a search for the best hyperparameters to maximize our prediction accuracy. The potential hyperparameters are shown below:

`hyperparameters = {
    'max_depth': [4, 5, 6, 7, 10, None], 
    'min_samples_split': [2, 3, 4, 5, 10],
    'criterion': ['gini', 'entropy']
}`

Running the `GridSearchCV()` k fold cross validation, we got:
- **Best criterion**: entropy
- **Best max-depth**: 7
- **Best min_samples_split**: 3

Using the above hyperparameters we performed our final prediction:

**Training Score/Accuracy**: 0.8959435626102292

**Testing Score/Accuracy**: 0.7857142857142857

This final prediction model looks better than our baseline model and the previous attempts with other variables. As you can see, although the training score went down, but the testing score improved by almost 5%. This is a sign that we <u>reduced overfitting on the training model and improved generalization on our testing model.</u>


## **Fairness Analysis**

We want to analyze if our model is fair to predict results for two different groups (produce the same accuracy), therefore we separated the dataset into two groups:

- Group X: Electricity outage in cold climate category
- Group Y: Electricity outage in the rest of the climate category
- p-value: standard 5% (0.05)
- Test Statistic: difference in accuracy

We chose **RMSE** as our evaluation metric and **difference in accuracy** as the test statistic and performed permutation tests.

- **Null Hypothesis**: Group A and Group B have the same performance (average RMSE) under the final model so the model is fair.
- **Alternative Hypothesis**: Group A and Group B do NOT have the same performance (average RMSE) under the final model so the model is NOT fair.

The p-values we got after running the permutation tests were usually around 0.9, which indicates that it is safe to say that we failed to reject the null hypothesis, and that Group A and Group B most likely have the same performance (average RMSE) under the final model so the model is likely fair.

<iframe
  src="assets/fairness.html"
  width="800"
  height="440"
  frameborder="0"
></iframe>