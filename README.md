
# Visualizing Time Series Data - Lab

## Introduction

As mentioned in the lecture, time series visualizations play an important role in the analysis of time series data. Time series are often plotted to allow data diagnostics to identify temporal structures. 

In this lab, we'll cover main techniques for visualizing time series data in Python using the minimum daily temperatures over 10 years (1981-1990) in the city Melbourne, Australia again. You might remember from the lesson that the units are in degrees Celsius and there are 3,650 observations. The [source](https://datamarket.com/data/set/2324/daily-minimum-temperatures-in-melbourne-australia-1981-1990) of the data is credited as the Australian Bureau of Meteorology.

## Objectives

You will be able to:

* Explore the temporal structure of time series with line plots
* Understand and describe the distribution of observations using histograms and density plots
* Measure the change in the distribution over intervals using box and whisker plots and heat map plots

## Let's get started!

Import the necessary libraries


```python
# Load required libraries
import pandas as pd
from pandas import Series
import matplotlib.pyplot as plt
import statsmodels.api as sm

import warnings
warnings.filterwarnings('ignore')
```


```python
# Load the data from min_temp.csv and check the index
df = pd.read_csv('min_temp.csv')
df.index
```




    RangeIndex(start=0, stop=3650, step=1)



Check the info. Next, make sure the index is the timestamp.


```python
df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 3650 entries, 0 to 3649
    Data columns (total 2 columns):
    Date         3650 non-null object
    Daily_min    3650 non-null float64
    dtypes: float64(1), object(1)
    memory usage: 57.2+ KB



```python
df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Date</th>
      <th>Daily_min</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>1/1/81</td>
      <td>20.7</td>
    </tr>
    <tr>
      <td>1</td>
      <td>2/1/81</td>
      <td>17.9</td>
    </tr>
    <tr>
      <td>2</td>
      <td>3/1/81</td>
      <td>18.8</td>
    </tr>
    <tr>
      <td>3</td>
      <td>4/1/81</td>
      <td>14.6</td>
    </tr>
    <tr>
      <td>4</td>
      <td>5/1/81</td>
      <td>15.8</td>
    </tr>
  </tbody>
</table>
</div>



Check the info again


```python
df['Date'] = pd.to_datetime(df.Date, format='%d/%m/%y', dayfirst=True)

df.set_index('Date', inplace=True)

df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    DatetimeIndex: 3650 entries, 1981-01-01 to 1990-12-31
    Data columns (total 1 columns):
    Daily_min    3650 non-null float64
    dtypes: float64(1)
    memory usage: 57.0 KB


## Time Series line plot

Create a time series line plot for `temp_data`


```python
df.plot(figsize=(12,8))
```




    <matplotlib.axes._subplots.AxesSubplot at 0x1c266e7710>




![png](output_10_1.png)


Some distinguishable patterns appear when we plot the data. Here we can see a pattern in our time series i.e. temperature values are maximum at the beginning of each year and minimum at around the 6th month. Yes, we are talking about Australia here so this is normal. This cyclical pattern is known as seasonality and will be covered in later labs. 

## Time Series dot plot
For a dense time series, as seen above, you may want to change the style of a line plot for a more refined visualization with a higher resolution of events. One way could be to change the continuous line to dots, each representing one entry in the time series. This can be achieved with the `style` parameter of the line plot. Lets pass `style='b.` as an argument to `.plot()` function


```python
# Use dots instead on a continuous line and redraw the timeseries. 
df.plot(figsize=(12,8), style='b.')
```




    <matplotlib.axes._subplots.AxesSubplot at 0x1c266e70b8>




![png](output_12_1.png)


This plot helps us identify clear outliers in certain years!

## Grouping and Visualizing time series data

Now, let's group data by year and create a line plot for each year for direct comparison.
You'll regroup data per year using `Pandas.grouper()`. 

- Import pandas grouper and use it to group values by year.
- Rearrange the data so you can create subplots for each year.


```python
# Use pandas grouper to group values using annual frequency
from pandas import Grouper
yr_grps = df.groupby(pd.Grouper(freq='A'))
```


```python
#Create a new DataFrame and store yearly values in columns 
annual_temp = pd.DataFrame()
```


```python
for yr, grp in yr_grps:
    annual_temp[yr.year] = grp.values.ravel()
```


```python
# Plot overlapping yearly groups 
annual_temp.plot(subplots=True, legend=True, figsize=(20,15));
```


![png](output_17_0.png)


You can see 10 subplots corresponding to the number of columns in your new DataFrame. Each plot is 365 days in length following the annual frequency.

Now, plot the same plots in an overlapping way.


```python
annual_temp.plot(subplots=False, legend=True, figsize=(20,15));
```


![png](output_19_0.png)


We can see in both plots above that due to the dense nature of time-series (365 values) and a high correlation between the values in different years (i.e. similar temperature values for each year), we can not clearly identify any differences in these groups. However, if you try this on the CO2 dataset used in the last lab, you should be able to see a clear trend showing an increase every year. 

## Time Series Histogram

Create a histogram for your data.


```python
df.hist(figsize=(12,8));
```


![png](output_22_0.png)



```python
# Plot a histogram of the temperature dataset
annual_temp.hist(figsize=(15,10));
```


![png](output_23_0.png)


The plot shows a distribution that looks strongly Gaussian/Normal. The plotting function automatically selects the size of the bins based on the spread of values in the data.

## Time Series Density Plots
Create a time series density plot


```python
df.plot.kde(legend=True, figsize=(8,5));
```


![png](output_25_0.png)



```python
# annual_temp.plot.kde(subplots=True, legend=True, figsize=(20,15));
```


```python
annual_temp.plot.kde(subplots=False, legend=True, figsize=(12,8));
```


![png](output_27_0.png)


We can see that the density plot provides a clearer summary of the distribution of observations. We can see that perhaps the distribution is a little asymmetrical and perhaps a little pointy to be Gaussian.

## Time Series Box and Whisker Plots by Interval

Let's use our groups by years to plot a box and whisker plot for each year for direct comparison using `boxplot()`.


```python
# Generate a box and whiskers plot for temp_annual dataframe

annual_temp.boxplot(figsize=(15,8))
```




    <matplotlib.axes._subplots.AxesSubplot at 0x1c2b664908>




![png](output_30_1.png)


In our plot above, we don't see much difference in the mean temperature over years, however, we can spot some outliers showing extremely cold or hot days. 

We can also plot distribution across months within each year. Perform the following tasks to achieve this. 
1. Extract observations for the year 1990 only, the last year in the dataset.

2. Group observations by month, and add each month to a new DataFrame as a column.

3. Create 12 box and whisker plots, one for each month of 1990.


```python
# Use temp Dataset to extract values for 1990
temp_yr = df.loc['1990']

# Add each month to dataFrame as a column
grp_monthly = temp_yr.groupby(pd.Grouper(freq='M'))

temp_yr_monthly = pd.concat([pd.DataFrame(x[1].values) for x in grp_monthly], axis=1)

monthly_temp = pd.DataFrame(temp_yr_monthly)

# Set the column names for each month i.e. 1,2,3, .., 12
monthly_temp.columns = range(1,13)

# Plot the box and whiskers plot for each month 
monthly_temp.boxplot(figsize=(20,12))
plt.show();
```


![png](output_33_0.png)


We see 12 box and whisker plots, showing the significant change in the distribution of minimum temperatures across the months of the year from the Southern Hemisphere summer in January to the Southern Hemisphere winter in the middle of the year, and back to summer again.

## Time Series Heat Maps

Let's create a heatmap of the Minimum Daily Temperatures data. The `matshow()` function from the matplotlib library is used as no heatmap support is provided directly in Pandas.

1. Rotate (transpose) the `temp_annual` dataframe as a new matrix the matrix so that each row represents one year and each column one day. 
2. Use `matshow()` function to draw a heatmap for transposed yearly matrix. 


```python

##### Transpose the yearly group DataFrame and draw a heatmap with matshow()
heatmap = annual_temp.T
plt.matshow(heatmap, interpolation=None, aspect='auto', cmap=plt.cm.Spectral_r)
plt.show()
```


![png](output_36_0.png)


We can now see that the plot shows the cooler minimum temperatures in the middle days of the years and the warmer minimum temperatures in the start and ends of the years, and all the fading and complexity in between.

Following this intuition, let's draw another heatmap comparing the months of the year in 1990. Each column represents one month, with rows representing the days of the month from 1 to 31.


```python
# Draw a heatmap comparing the months of the year in 1990.
heatmap2 = monthly_temp.T
plt.matshow(heatmap2, fignum=None, interpolation=None, aspect='auto', cmap=plt.cm.Spectral_r)
plt.show()
```


![png](output_39_0.png)


The plot shows the same macro trend seen for each year on the zoomed level of month-to-month. We can also see some white patches at the bottom of the plot. This is missing data for those months that have fewer than 31 days, with February being quite an outlier with 28 days in 1990.

## Summary 

In this lab, we discovered how to explore and better understand a time-series dataset in Python and Pandas. You learned how to explore the temporal relationships with line, scatter, and autocorrelation plots. We also explored the distribution of observations with histograms and density plots and change in the distribution of observations with box and whisker and heat map plots.
