# Instagram A/B Testing

# Background

Instagram conducted an A/B test to evaluate the impact of its newly launched feature, Snapshot, designed to allow users to share short video clips on the platform alongside photos. This feature aims to diversify content, enhance user experience, and increase overall engagement. 

The test was carried out in Mexico, from the existing Instagram users in Mexico, where users were divided into two groups, and split them into two groups: Group A (control group) and Group B (experimental group). Users in Group A will remain unchanged and continue to see the original version of the Insta interface. Users in Group B, the experimental group, will experience a configuration change upon opening the app. They will see the Snapshot icon on the home screen and have access to the newly launched short video function.

By doing this small experiment in Mexico, we can see how the Snapshot feature affects how often users interact with it and check if it could do well in the Latin American market.


# Experimental Metrics

We will formulate the hypotheses we want to validate in the experiment:

H0: No difference between the original version and the new version of Instagram.
H1: The new version of Insta with Snapshot is more favored by users.


α represents the Type I error rate (False Positive: rejecting the null hypothesis), and β represents the Type II error rate (False Negative: accepting the null hypothesis). The typical values for α are 0.05. Power can be calculated as 1 - β, representing the probability of correctly rejecting the null hypothesis when it is false.

```
from scipy.stats import norm
#norm.ppf() takes a percentage and returns a standard deviation multiplier for what value that percentage occurs at for a one-tail test on a standard normal distribution
norm.ppf(0.975)
```
1.959963984540054

## Power of the Test Determination:

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
#calculate the minimum sample size
print('The minimal sample size for each group is',round(sample_size))
```
The minimal sample size for each group is 44651.

According to our calculations, the minimum sample size required for a single experimental group is 44651. Since we have two groups, the minimum sample size required for the experiment would be 89302.

# Test Cycle

Once the minimum sample size is calculated, we can proceed with conducting the experiment A/B testing.
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
## The average usage duration per person
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
<img src="(https://github.com/user-attachments/assets/24a520ac-48eb-41c1-8348-2fff68bd4371)" width="400" height="200">


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

#normality test
```
import scipy.stats as stats
k2, p = stats.normaltest(dfA['app use hours'])
alpha = 0.05  # significance level
if p < alpha:
    print("Sample data does not follow normal distribution, p =", p)
else:
    print("Sample data follows normal distribution, p =", p)
```

```
#T-test
import scipy.stats as stats

group1 = dfA['app use hours']
group2 = dfB['app use hours']
stats.ttest_ind(a=group1, b=group2, equal_var=True)
```

Since the p-value is less than or equal to 0.05, we reject the null hypothesis. This means that the average time spent using the app in the experimental group is significantly higher than in the control group. Since the average time people use the app is our main metric, this result is critical.


## The average number of app usage per person.

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


<img src="https://github.com/user-attachments/assets/9816772a-3162-4abb-ae60-a373fa4dccf6" width="400" height="200">

```
#normality test
import scipy.stats as stats
k2, p = stats.normaltest(dfA['app use times'])
alpha = 0.05  # significance level
if p < alpha:
    print("Sample data does not follow normal distribution, p =", p)
else:
    print("Sample data follows normal distribution, p =", p)
```

```
#average number of app usage per person
group1 = dfA['app use times']
group2 = dfB['app use times']
stats.ttest_ind(a=group1, b=group2, equal_var=True)
```

Since the p-value is less than or equal to 0.05, we reject the null hypothesis. This means that  the average number of app usage per person in the experimental group is significantly higher than that in the control group.

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

The calculated p-value > 0. 05 shows that this difference is not significant. There is no significant difference in retention rates between the experimental and control groups.
The new feature can be deployed.

# Conclusion
However, it is important to note that the penetration rate of the new feature in the experimental group was less than 20%. This indicates that a majority of the users in the experimental group did not actually click to enter the Snapshot interface. We can imporve low version coverage to be validated by comparing the penetration rates of the new version between the experimental and control groups. Regardless of the reasons, we can be certain that even if only a small portion of the experimental group experienced the new feature, it has already brought significant benefits!













