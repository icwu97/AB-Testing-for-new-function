# Instagram A/B Testing

# Background

Instagram conducted an A/B test to evaluate the impact of its newly launched feature, Snapshot, designed to allow users to share short video clips on the platform alongside photos. This feature aims to diversify content, enhance user experience, and increase overall engagement. 

The test was carried out in Mexico, from the existing Instagram users in Mexico, where users were divided into two groups, and split them into two groups: Group A (control group) and Group B (experimental group). Users in Group A will remain unchanged and continue to see the original version of the Insta interface. Users in Group B, the experimental group, will experience a configuration change upon opening the app. They will see the Snapshot icon on the home screen and have access to the newly launched short video function.

By doing this small experiment in Mexico, we can see how the Snapshot feature affects how often users interact with it and check if it could do well in the Latin American market.


# Experimental Metrics

We will formulate the hypotheses we want to validate in the experiment:

- H0: No difference between the original version and the new version of Instagram.
- H1: The new version of Insta with Snapshot is more favored by users.


α represents the Type I error rate (False Positive: rejecting the null hypothesis), and β represents the Type II error rate (False Negative: accepting the null hypothesis). The typical values for α are 0.05. Power can be calculated as 1 - β, representing the probability of correctly rejecting the null hypothesis when it is false.

```
from scipy.stats import norm
#norm.ppf() takes a percentage and returns a standard deviation multiplier for what value that percentage occurs at for a one-tail test on a standard normal distribution
norm.ppf(0.975)
```
1.959963984540054

## Power of the Test Determination: (Minimum sample size)

For Instagram's A/B test of the Snapshot feature, the power of the determination was calculated to assess three key metrics: 
- The average usage duration per person, which measures how long users spend engaging with the app.
- The average number of app usage sessions per person, which captures user frequency and interaction levels.
- The hypothesis testing for retention metrics, which evaluates whether users return to the app after their initial engagement. 
These metrics were used to determine whether the new feature significantly impacts user engagement and retention and evaluation of Snapshot's effectiveness.
```
alpha = 0.05
power = 0.8
delta = 450*0.005 # Statistically, the average usage duration of InstaMagnet per person over the past two weeks is calculated. It is expected that SnapFlicks will bring a minimum improvement of 0.5% to the product metric.
SD = 120 # The standard deviation of the metric based on the data collected over the past two weeks is calculated.
variance = SD**2
sample_size = 2 * (norm.ppf(1 - alpha / 2) + norm.ppf(power)) ** 2 * variance / (delta ** 2)
#calculate the power of determination
print('The power of determination for each group is',round(sample_size))
```
The power of determination for each group is 44651.

According to our calculations, the power of determination required for a single experimental group is 44651. Since we have two groups, the power of determination required for the experiment would be 89302.

# Test Cycle

Once the power of determination is calculated, we can proceed with conducting the experiment A/B testing.
```
DAU = 8000000 # The current average daily active user count in Mexico.
proportion = 0.001 #A single experiment consumes 0.1% of the traffic.
total_sample = sample_size*2 # The minimum sample size for a single group multiplied by 2 groups.
test_duration = total_sample / (DAU*proportion)
# Compute the cycle
print('AB test result will be available after',round(test_duration),'days')
```
AB test result will be available after 11 days.


# Hypothesis Testing for Average Metrics

When conducting hypothesis testing for average metrics, normality, independence, homogeneity of variance, and adequate sample size are need to be satisfied.
```
#input data
import pandas as pd
dfA = pd.read_excel('AB_test_sample.xlsx', sheet_name = 'GroupA')
dfB = pd.read_excel('AB_test_sample.xlsx', sheet_name = 'GroupB')
```
```
#The control group data.
dfA.describe()
```
```
#The experimental group data.
dfB.describe()
```
## The average usage duration per person - App use hours
```
import matplotlib.pyplot as plt

plt.figure(figsize=(10, 5))
plt.hist(dfA['app use hours'], bins=100, color='red', alpha=0.7, label='Group A')
plt.title('Distribution of App Use Hours: Group A')
plt.xlabel('App Use Hours')
plt.ylabel('Frequency')
plt.legend()
plt.show()

plt.figure(figsize=(10, 5))
plt.hist(dfB['app use hours'], bins=100, color='green', alpha=0.7, label='Group B')
plt.title('Distribution of App Use Hours: Group B')
plt.xlabel('App Use Hours')
plt.ylabel('Frequency')
plt.legend()
plt.show()

```
<img src="https://github.com/user-attachments/assets/d142b7b1-6148-4933-ae9a-25dda5179624" width="400" height="200">
<img src="https://github.com/user-attachments/assets/5219a416-8060-44ef-8e20-38d2f71f427a" width="400" height="200">

```
import matplotlib.pyplot as plt

plt.figure(figsize=(10, 5))
plt.hist(dfA['app use hours'], bins=100, color='red', alpha=0.7, label='Group A')
plt.hist(dfB['app use hours'], bins=100, color='green', alpha=0.7, label='Group B')
plt.title('Distribution of App Use Hours: Group A vs. Group B')
plt.xlabel('App Use Hours')
plt.ylabel('Frequency')
plt.legend()
plt.show()
```
<img src="https://github.com/user-attachments/assets/53b5b0da-4359-4ec1-84fa-d79d8e1637fc" width="400" height="200">


## Normality test
```
import scipy.stats as stats
k2, p = stats.normaltest(dfA['app use hours'])
alpha = 0.05  # significance level
if p < alpha:
    print("Sample data does not follow normal distribution, p =", p)
else:
    print("Sample data follows normal distribution, p =", p)
```
Sample data follows normal distribution, p = 0.4538848510421052

```
#T-test
import scipy.stats as stats

group1 = dfA['app use hours']
group2 = dfB['app use hours']
stats.ttest_ind(a=group1, b=group2, equal_var=True)
```
Ttest_indResult(statistic=-3.6934297450364797, pvalue=0.0002213804493662174)


Since the p-value is less than or equal to 0.05, we reject the null hypothesis. This means that the average time spent using the app in the experimental group is significantly higher than in the control group. Since the average time people use the app is our main metric, this result is critical.


## The average number of app usage per person. - App use times

```
import matplotlib.pyplot as plt

plt.figure(figsize=(10, 5))
plt.hist(dfA['app use times'], bins=35, color='red', alpha=0.7, label='Group A')
plt.title('Distribution of App Use Times: Group A')
plt.xlabel('App Use Times')
plt.ylabel('Frequency')
plt.legend()
plt.show()

plt.figure(figsize=(10, 5))
plt.hist(dfB['app use times'], bins=35, color='green', alpha=0.7, label='Group B')
plt.title('Distribution of App Use Times: Group B')
plt.xlabel('App Use Times')
plt.ylabel('Frequency')
plt.legend()
plt.show()
```

<img src="https://github.com/user-attachments/assets/a806f702-c4ec-4e63-a18a-ead71799f280" width="400" height="200">
<img src="https://github.com/user-attachments/assets/ff5e8152-1389-4384-9398-9d1f0b581634" width="400" height="200">

```
plt.figure(figsize=(10, 5))
plt.hist(dfA['app use times'], bins=40, color='red', alpha=0.7, label='Group A - App Use Times')
plt.hist(dfB['app use times'], bins=40, color='green', alpha=0.7, label='Group B - App Use Times')
plt.title('Distribution of App Use Times: Group A vs. Group B')
plt.xlabel('App Use Times')
plt.ylabel('Frequency')
plt.legend()
plt.show()
```
<img src="https://github.com/user-attachments/assets/bc0dfd51-39cb-4192-965f-4d4346688700" width="400" height="200">


## Normality test

```
import scipy.stats as stats
k2, p = stats.normaltest(dfA['app use times'])
alpha = 0.05  # significance level
if p < alpha:
    print("Sample data does not follow normal distribution, p =", p)
else:
    print("Sample data follows normal distribution, p =", p)
```
Sample data follows normal distribution, p = 0.4616520932198871


```
#average number of app usage per person
group1 = dfA['app use times']
group2 = dfB['app use times']
stats.ttest_ind(a=group1, b=group2, equal_var=True)
```
Ttest_indResult(statistic=-5.6512085520876845, pvalue=1.5980360297263646e-08)

Since the p-value is less than or equal to 0.05, we reject the null hypothesis. This means that the average number of app usage per person in the experimental group is significantly higher than that in the control group.

# Hypothesis testing for retention metrics

Z-test is a statistical tool that helps to see how two groups compare in terms of their proportions. It helps find out if the sizes of two samples are the same or not.

Retention is a typical binary event (stay or not stay).
Next-day app retention (split by enrollment date):
Numerator = Cumulative retention count (users who enrolled on the 1st and used the app on the 2nd day) + (users who enrolled on the 2nd and used the app on the 3rd day) + ... + (users who enrolled on the 30th and used the app on the 31st day).
Denominator = Cumulative app usage count (cumulative count of users who enrolled).

```
from statsmodels.stats.proportion import proportions_ztest
zscore, pvalue = proportions_ztest(retained, all_obs)
print('zscore = {:.4f}, pvalue = {:.4f}'.format(zscore, pvalue))
```
zscore = 0.9323, pvalue = 0.3512

The calculated p-value > 0. 05 shows that this difference is not significant. There is no significant difference in retention rates between the experimental and control groups.
The new feature still can be deployed.

# Conclusion

The A/B testing results demonstrate that Instagram's newly introduced Snapshot feature has a significant impact on user engagement metrics, the observed engagement improvements highlight the potential business value of the new feature:

1. App Use Hours
   - Both groups follow a similar pattern, but Group B has a slight shift toward higher app usage hours, indicating improved engagement due to the Snapshot feature. The increased app usage duration directly correlates with higher ad impressions, boosting revenue potential.

2. App Use Times
   - Group B has a noticeable increase in session frequency compared to Group A, suggesting that Snapshot encourages users to open the app more often. The imapact of higher app use times enhance engagement opportunities, increasing user habit formation and the potential for serving targeted ads.

3. Retention Metrics
   - No statistically significant difference was observed between the two groups in terms of next-day retention rates.
  
The A/B test results show that the experimental group (Group B) outperformed the control group (Group A) in key engagement metrics, such as app use hours and app use times. Group B users spent significantly more time on the app and opened it more frequently, indicating increased user engagement driven by the Snapshot feature. However, no significant difference was observed in next-day retention rates between the two groups. These findings suggest that while the feature effectively boosts engagement, its long-term retention impact may require further optimization. Improving the feature’s visibility and adoption rate could maximize its potential benefits for Instagram’s user experience and revenue.













