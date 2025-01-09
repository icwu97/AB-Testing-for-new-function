# AB-Testing-for-new-function

# Background

From the existing Insta users in Mexico, and split them into two groups: Group A (control group) and Group B (experimental group). sers in Group A will remain unchanged and continue to see the original version of the Insta interface. Users in Group B, the experimental group, will experience a configuration change upon opening the app. They will see the Snapshot icon on the home screen and have access to the newly launched short video function.

By doing this small experiment in Mexico, we can see how the Snapshot feature affects how often users interact with it and check if it could do well in the Latin American market.


# Experimental Metrics

We will formulate the hypotheses we want to validate in the experiment:

H0: The original version of Insta is more favored by users.
H1: The new version of Insta with Snapshot is more favored by users.

H0 - Hypothesis to be challenged, H1 - Hypothesis to be supported.

α represents the Type I error rate (False Positive: rejecting the null hypothesis), and β represents the Type II error rate (False Negative: accepting the null hypothesis). The typical values for α are 0.05. Power can be calculated as 1 - β, representing the probability of correctly rejecting the null hypothesis when it is false.

```
from scipy.stats import norm
#norm.ppf() takes a percentage and returns a standard deviation multiplier for what value that percentage occurs at for a one-tail test on a standard normal distribution
norm.ppf(0.975)
```
Minmum sample size:
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

According to our calculations, the minimum sample size required for a single experimental group is X. Since we have two groups, the minimum sample size required for the experiment would be approximately 2X.

# Test Cycle

Once the minimum sample size is calculated, we can proceed with conducting the experiment A/B testing.
![image](https://github.com/user-attachments/assets/f3b76b39-bbd3-4433-8975-9b2fd362133d)

# Hypothesis Testing for Average Metrics

When conducting hypothesis testing for average metrics, normality, independence, homogeneity of variance, and adequate sample size are need to be satisfied.

![image](https://github.com/user-attachments/assets/3efc12cf-d03b-459c-9807-66cfb4692ea1)
![image](https://github.com/user-attachments/assets/db54f8c7-90bf-41a6-96e2-bdb91ba8e2db)

## The average usage duration per person
![image](https://github.com/user-attachments/assets/a78ca662-83cf-4336-a909-711f8f7a5001)

![image](https://github.com/user-attachments/assets/2f3f5328-829b-4145-80c6-b6c9ec8aa44c)

![image](https://github.com/user-attachments/assets/7ca7ed25-370c-4929-9f55-01d7e8023523)

Since the p-value is less than or equal to 0.05, we reject the null hypothesis. This means that the average time spent using the app in the experimental group is significantly higher than in the control group. Since the average time people use the app is our main metric, this result is critical.

## The average number of app usage per person.

![image](https://github.com/user-attachments/assets/e101f354-7b66-4d57-8704-4be29bf5285b)

![image](https://github.com/user-attachments/assets/33e66ad5-4479-432b-8ad1-d6e337b00270)

![image](https://github.com/user-attachments/assets/269ca2ca-ca28-4050-89e4-d543f9c9a80a)

Since the p-value is less than or equal to 0.05, we reject the null hypothesis. This means that  the average number of app usage per person in the experimental group is significantly higher than that in the control group.

# Hypothesis testing for retention metrics

Z-test is a statistical tool that helps to see how two groups compare in terms of their proportions. It helps find out if the sizes of two samples are the same or not.

Retention is a typical binary event (stay or not stay).
Next-day app retention (split by enrollment date):
Numerator = Cumulative retention count (users who enrolled on the 1st and used the app on the 2nd day) + (users who enrolled on the 2nd and used the app on the 3rd day) + ... + (users who enrolled on the 30th and used the app on the 31st day).
Denominator = Cumulative app usage count (cumulative count of users who enrolled).

![image](https://github.com/user-attachments/assets/22b9d4c0-9aad-4848-81db-b1576312d3a1)

The calculated p-value > 0. 05 shows that this difference is not significant. There is no significant difference in retention rates between the experimental and control groups.
The new feature can be deployed.

# Conclusion
However, it is important to note that the penetration rate of the new feature in the experimental group was less than 20%. This indicates that a majority of the users in the experimental group did not actually click to enter the Snapshot interface. We can imporve low version coverage to be validated by comparing the penetration rates of the new version between the experimental and control groups. Regardless of the reasons, we can be certain that even if only a small portion of the experimental group experienced the new feature, it has already brought significant benefits!













