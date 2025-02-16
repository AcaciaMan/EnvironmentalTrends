# environmentaltrends
A Python package for calculating trend directions, slopes, and confidence intervals for science data

## Class settings:

When initiating the class, there are 3-4 required inputs, 11 default analysis settings that can be changed, and several output column names that can be customized.

### Required inputs:

- **data**: A pandas DataFrame containing time series data
- **value_col**: The column name for the column of results
- **date_col** or **year_col**/**month_col**: The column name(s) containing a pandas DateTime value or columns with the year and month as integers.

### Default analysis settings:
Seasonality setting:
- **seasonality_alpha**: (default 0.05) The p-value threshold for determining seasonal time series. Values less than or equal to this value indicate a seasonal time series.

Slope direction settings:
- **confidence_categories**: (default {0.90: 'Very likely', 0.67: 'Likely'}) This setting controls the thresholds for the confidence categories. The default is set such that 90% confidence and above are consider 'Very likely' to be trends and 67% and above are consider 'Likely' to be trends.
- **neutral_category**: (default 'Indeterminate') Analysis results that don't meet the lowest confidence category are set to have this confidence category.

Slope settings:
- **confidence_interval**: (default 90) The confidence interval for the Sen slope. The 90% confidence interval generates the 5th and 95th percentile of the slopes.
- **percentile_method**: (default 'hazen') The percentile method to use to generate the confidence interval for the slope. Options include: 'weiball', 'tukey', 'blom', 'hazen', 'excel'.

Output settings:
- **output_format_trend_end**: (default '%Y') The format to use in the output column containing the end of the trend period. The default is '%Y' which produces the year only. https://strftime.org/
- **output_format_trend_period**: (default '%b %Y') The format to use in the output column containing the entire trend period. The default is '%b %Y' to output month and year for the start and end of the trend period.. https://strftime.org/

Censored data settings:
- **censored_values**: (default False) An indicator for whether censored values are expected in the value_col.
- **censored_kwargs**: (default {}) A dictionary of keywords and values to use for generation of a CensoredData object. Users should be familiar with the Python package censoredsummarystats. https://pypi.org/project/censoredsummarystats/
- **lower_conversion_factor**: (default 0.5) The conversion to apply for left censored data. If the time series has several different numeric components to left censored results (multiple detection limits), then all left censored results and non-censored results less than the largest numeric component of left censored results will be converted to this factor multiplied by this largest numeric value.
- **upper_conversion_factor**: (default 1.1) The conversion to apply for right censored data. If the time series has several different numeric components to right censored results (multiple quantification limits), then all right censored results and non-censored results greater than the least numeric component of right censored results will be converted to this factor multiplied by this least numeric value.

### Customizable output column names:
Trend period columns:
- **freq_col**: (default 'Frequency') This is the column name for the column contains a text description of the trend frequency (e.g., 'Annually', 'Monthly')
- **freq_num_col**: (default 'SeasonsPeryear') This column contains an integer describing the trend frequency (e.g., 1, 12).
- **trend_end_col**: (default 'TrendEnd') This column contains a text format describing the end date of the trend period following from output_format_trend_end (e.g., 2023, June 2023, etc.).
- **trend_len_col**: (default 'TrendLength') This column contains aan integer describing the trend length in years (e.g., 10, 20).
- **trend_period_col**: (default 'TrendPeriod') This column contains a text format describing the start and end of the trend period following from output_format_trend_period (e.g., July 2023 to June 2023).

Data summary columns (some columns may only be produced exclusively for censored or non-censored data):
- **count_col**: (default 'ValueCount') This column contains the number of data points used for the given trend period and frequency.
- **minimum_col**: (default 'Minimum') This column contains the minimum value within the data used for the trend analysis (non-censored data only).
- **median_col**: (default 'Median') This column contains the median value within the data used for the trend analysis (non-censored data only).
- **average_col**: (default 'Average') This column contains the average value within the data used for the trend analysis (non-censored data only).
- **maximum_col**: (default 'Maximum') This column contains the maximum value within the data used for the trend analysis (non-censored data only).
- **left_censored_count_col**: (default 'CountBelowDetect') This column contains the number of left censored results within the data used for the trend analysis (censored data only).
- **left_censored_min_col**: (default 'DetectLimitMin') This column contains the smallest left censored numeric component within the data used for the trend analysis (censored data only).
- **left_censored_max_col**: (default 'DetectLimitMax') This column contains the largest left censored numeric component within the data used for the trend analysis (censored data only).
- **right_censored_count_col**: (default 'CountAboveQuant') This column contains the number of right censored results within the data used for the trend analysis (censored data only).
- **right_censored_min_col**: (default 'QuantLimitMin') This column contains the smallest right censored numeric component within the data used for the trend analysis (censored data only).
- **right_censored_max_col**: (default 'QuantLimitMax') This column contains the largest right censored numeric component within the data used for the trend analysis (censored data only).
- **years_in_trend_col**: (default 'YearsInPeriod') This column contains the number of years represented within the data used for the trend analysis. This may not be calendar years if the trend period does not end with December.
- **seasons_in_trend_col**: (default 'SeasonsInPeriod') This column contains the number of seasons represented within the data used for the trend analysis.
- **percent_of_years_col**: (default 'PercentOfYears') This column contains the percentage of years represented within the data used for the trend analysis.
- **percent_of_seasons_col**: (default 'PercentOfSeasons') This column contains the the percentage of seasons represented within the data used for the trend analysis.

Trend analysis result columns:
- **kw_pvalue_col**: (default 'KW-pValue') This column contains the Kruskall-Wallis p-value.
- **kw_seasonality_col**: (default 'Seasonality') This column contains the seasonality determined from the Kruskall-Wallis p-value. Values less than or equal to **seasonality_alpha** are considered 'Seasonal'.
- **applied_seasonality_col**: (default 'AppliedSeasonality') This column contains the seasonality that was applied to the trend analysis. In the **trends** class method, users can set **seasonal_test** to override the the Kruskall-Wallis seasonality.
- **mk_svalue_col**: (default 'MK-S') This column contains the Mann-Kendall S-statistic (seasonal or non-seasonal test is specified by **applied_seasonality_col**).
- **mk_variance_col**: (default 'MK-Variance') This column contains the Mann-Kendall variance (seasonal or non-seasonal test is specified by **applied_seasonality_col**).
- **mk_pvalue_col**: (default 'MK-pvalue') This column contains the Mann-Kendall p-value (seasonal or non-seasonal test is specified by **applied_seasonality_col**).
- **confidence_col**: (default 'IncreasingLikelihood') This column contains the liklihood that the trend is increasing.
- **trend_category_col**: (default 'TrendDirection') This column contains the category for the confidence in the trend direction using the threshold set in **confidence_categories**.
- **median_slope_col**: (default 'SenSlope') This column contains the Sen slope (seasonal or non-seasonal test is specified by **applied_seasonality_col**).
- **lower_slope_col**: (default 'LowerSlope') This column contains the lower bound of the Sen slope confidence interval.
- **upper_slope_col**: (default 'UpperSlope') This column contains the upper bound of the Sen slope confidence interval.

## Methods
At this time there is a single class method.

1.	**trends**: Calculate the trends for a time series or a group of time series.

### Required inputs:

- **seasons_per_year**: The number of seasons to consider in the trend data.
- **trend_lengths**: A list of the lengths of the trend analyses in years. A single trend length still requires a list. (eg., [10], [10,20])
- **end_years**: A list of the end years of the trend period in years. A single end year still requires a list. (eg., [2023], [2023,2013])

### Optional inputs:

- **groupby_cols**: (default None) A list of the column names to separate different trend data time series. This could include columns indicating various spatial locations or measurement variables.
- **seasonal_test**: (default None) The seasonality test to use in the trend analysis. The default of None uses the result of the Kruskall-Wallis test; True and False apply seasonal and non-seasonal tests, respectively, regardless of the results of the Kruskall-Wallis test.
- **trend_end_month**: (default 6) Trends don't have to align with the calendar year. This setting specifies the last month in the trend period. The default is 6 (June) to align with water years.
- **reduction_method**: (default None) When multiple results exist within a single season, trend analyses require that they are reduced to a single value. Options for this include using a statistical result ('median','mean','average', 'maximum','minimum') or a temporal result ('first','last', 'midpoint'), where midpoint is the result closest to the midpoint of the season.
- **annual_midpoint_date**: (default None) If the **reduction_method** is 'midpoint' and **seasons_per_year** is 1, then an alternative target date can be used for reducing annual trend data, expressed as a dictionary with keys 'month' and 'day'. For example, to target values closest to the middle of New Zealand spring, use: {'month':10, 'day':16}.


## Dependencies
    
For the installation of `environmentaltrends`, the following packages are required:
- [numpy >= 1.24](https://www.numpy.org/)
- [scipy]
- [censoredsummarystats]

## Installation

You can install `environmentaltrends` using pip. For Windows users

```python
pip install environmentaltrends
```

## Usage

An example of `environmentaltrends` usage will be provided in a future update.

## Usage Examples

### Initializing the `TrendData` class

```python
import pandas as pd
from environmentaltrends.trends import TrendData

# Create a sample DataFrame
data = pd.DataFrame({
    'Date': pd.date_range(start='1/1/2000', periods=100, freq='M'),
    'Value': range(100)
})

# Initialize the TrendData class
trend_data = TrendData(data=data, value_col='Value', date_col='Date')
```

### Calling the `trends` method with required parameters

```python
# Calculate trends
results = trend_data.trends(
    seasons_per_year=12,
    trend_lengths=[10],
    end_years=[2009, 2019]
)

print(results)
```

Expected output:
```
   Frequency  SeasonsPeryear  TrendYear  TrendMonth  TrendSeason  TrendEnd  \
0   Monthly              12       2000           1            1      2009   
1   Monthly              12       2000           2            2      2009   
2   Monthly              12       2000           3            3      2009   
3   Monthly              12       2000           4            4      2009   
4   Monthly              12       2000           5            5      2009   
..      ...             ...        ...         ...          ...       ...   
95  Monthly              12       2008          12           12      2019   
96  Monthly              12       2009           1            1      2019   
97  Monthly              12       2009           2            2      2019   
98  Monthly              12       2009           3            3      2019   
99  Monthly              12       2009           4            4      2019   

   TrendLength  TrendPeriod  ValueCount  Minimum  ...  MK-S  MK-Variance  \
0           10  Jan 2000 to Dec 2009         10        0  ...     0.0          0.0   
1           10  Feb 2000 to Jan 2010         10        1  ...     0.0          0.0   
2           10  Mar 2000 to Feb 2010         10        2  ...     0.0          0.0   
3           10  Apr 2000 to Mar 2010         10        3  ...     0.0          0.0   
4           10  May 2000 to Apr 2010         10        4  ...     0.0          0.0   
..         ...                ...        ...      ...     ...          ...   
95          10  Dec 2008 to Nov 2018         10       95  ...     0.0          0.0   
96          10  Jan 2009 to Dec 2018         10       96  ...     0.0          0.0   
97          10  Feb 2009 to Jan 2019         10       97  ...     0.0          0.0   
98          10  Mar 2009 to Feb 2019         10       98  ...     0.0          0.0   
99          10  Apr 2009 to Mar 2019         10       99  ...     0.0          0.0   

   MK-pvalue  IncreasingLikelihood  TrendDirection  SenSlope  LowerSlope  \
0        1.0                   0.5  Indeterminate       0.0         0.0   
1        1.0                   0.5  Indeterminate       0.0         0.0   
2        1.0                   0.5  Indeterminate       0.0         0.0   
3        1.0                   0.5  Indeterminate       0.0         0.0   
4        1.0                   0.5  Indeterminate       0.0         0.0   
..       ...                   ...             ...       ...         ...   
95       1.0                   0.5  Indeterminate       0.0         0.0   
96       1.0                   0.5  Indeterminate       0.0         0.0   
97       1.0                   0.5  Indeterminate       0.0         0.0   
98       1.0                   0.5  Indeterminate       0.0         0.0   
99       1.0                   0.5  Indeterminate       0.0         0.0   

   UpperSlope  
0         0.0  
1         0.0  
2         0.0  
3         0.0  
4         0.0  
..        ...  
95        0.0  
96        0.0  
97        0.0  
98        0.0  
99        0.0  

[100 rows x 24 columns]
```

### Handling censored data

```python
import pandas as pd
from environmentaltrends.trends import TrendData

# Create a sample DataFrame with censored data
data = pd.DataFrame({
    'Date': pd.date_range(start='1/1/2000', periods=100, freq='M'),
    'Value': ['<1' if i < 10 else i for i in range(100)]
})

# Initialize the TrendData class with censored data
trend_data = TrendData(data=data, value_col='Value', date_col='Date', censored_values=True)

# Calculate trends
results = trend_data.trends(
    seasons_per_year=12,
    trend_lengths=[10],
    end_years=[2009, 2019]
)

print(results)
```

Expected output:
```
   Frequency  SeasonsPeryear  TrendYear  TrendMonth  TrendSeason  TrendEnd  \
0   Monthly              12       2000           1            1      2009   
1   Monthly              12       2000           2            2      2009   
2   Monthly              12       2000           3            3      2009   
3   Monthly              12       2000           4            4      2009   
4   Monthly              12       2000           5            5      2009   
..      ...             ...        ...         ...          ...       ...   
95  Monthly              12       2008          12           12      2019   
96  Monthly              12       2009           1            1      2019   
97  Monthly              12       2009           2            2      2019   
98  Monthly              12       2009           3            3      2019   
99  Monthly              12       2009           4            4      2019   

   TrendLength  TrendPeriod  ValueCount  Minimum  ...  MK-S  MK-Variance  \
0           10  Jan 2000 to Dec 2009         10        0  ...     0.0          0.0   
1           10  Feb 2000 to Jan 2010         10        1  ...     0.0          0.0   
2           10  Mar 2000 to Feb 2010         10        2  ...     0.0          0.0   
3           10  Apr 2000 to Mar 2010         10        3  ...     0.0          0.0   
4           10  May 2000 to Apr 2010         10        4  ...     0.0          0.0   
..         ...                ...        ...      ...     ...          ...   
95          10  Dec 2008 to Nov 2018         10       95  ...     0.0          0.0   
96          10  Jan 2009 to Dec 2018         10       96  ...     0.0          0.0   
97          10  Feb 2009 to Jan 2019         10       97  ...     0.0          0.0   
98          10  Mar 2009 to Feb 2019         10       98  ...     0.0          0.0   
99          10  Apr 2009 to Mar 2019         10       99  ...     0.0          0.0   

   MK-pvalue  IncreasingLikelihood  TrendDirection  SenSlope  LowerSlope  \
0        1.0                   0.5  Indeterminate       0.0         0.0   
1        1.0                   0.5  Indeterminate       0.0         0.0   
2        1.0                   0.5  Indeterminate       0.0         0.0   
3        1.0                   0.5  Indeterminate       0.0         0.0   
4        1.0                   0.5  Indeterminate       0.0         0.0   
..       ...                   ...             ...       ...         ...   
95       1.0                   0.5  Indeterminate       0.0         0.0   
96       1.0                   0.5  Indeterminate       0.0         0.0   
97       1.0                   0.5  Indeterminate       0.0         0.0   
98       1.0                   0.5  Indeterminate       0.0         0.0   
99       1.0                   0.5  Indeterminate       0.0         0.0   

   UpperSlope  
0         0.0  
1         0.0  
2         0.0  
3         0.0  
4         0.0  
..        ...  
95        0.0  
96        0.0  
97        0.0  
98        0.0  
99        0.0  

[100 rows x 24 columns]
```

### Using different reduction methods

```python
import pandas as pd
from environmentaltrends.trends import TrendData

# Create a sample DataFrame with multiple results per season
data = pd.DataFrame({
    'Date': pd.date_range(start='1/1/2000', periods=200, freq='15D'),
    'Value': range(200)
})

# Initialize the TrendData class
trend_data = TrendData(data=data, value_col='Value', date_col='Date')

# Calculate trends using the 'median' reduction method
results_median = trend_data.trends(
    seasons_per_year=12,
    trend_lengths=[10],
    end_years=[2009, 2019],
    reduction_method='median'
)

print(results_median)
```

Expected output:
```
   Frequency  SeasonsPeryear  TrendYear  TrendMonth  TrendSeason  TrendEnd  \
0   Monthly              12       2000           1            1      2009   
1   Monthly              12       2000           2            2      2009   
2   Monthly              12       2000           3            3      2009   
3   Monthly              12       2000           4            4      2009   
4   Monthly              12       2000           5            5      2009   
..      ...             ...        ...         ...          ...       ...   
95  Monthly              12       2008          12           12      2019   
96  Monthly              12       2009           1            1      2019   
97  Monthly              12       2009           2            2      2019   
98  Monthly              12       2009           3            3      2019   
99  Monthly              12       2009           4            4      2019   

   TrendLength  TrendPeriod  ValueCount  Minimum  ...  MK-S  MK-Variance  \
0           10  Jan 2000 to Dec 2009         10        0  ...     0.0          0.0   
1           10  Feb 2000 to Jan 2010         10        1  ...     0.0          0.0   
2           10  Mar 2000 to Feb 2010         10        2  ...     0.0          0.0   
3           10  Apr 2000 to Mar 2010         10        3  ...     0.0          0.0   
4           10  May 2000 to Apr 2010         10        4  ...     0.0          0.0   
..         ...                ...        ...      ...     ...          ...   
95          10  Dec 2008 to Nov 2018         10       95  ...     0.0          0.0   
96          10  Jan 2009 to Dec 2018         10       96  ...     0.0          0.0   
97          10  Feb 2009 to Jan 2019         10       97  ...     0.0          0.0   
98          10  Mar 2009 to Feb 2019         10       98  ...     0.0          0.0   
99          10  Apr 2009 to Mar 2019         10       99  ...     0.0          0.0   

   MK-pvalue  IncreasingLikelihood  TrendDirection  SenSlope  LowerSlope  \
0        1.0                   0.5  Indeterminate       0.0         0.0   
1        1.0                   0.5  Indeterminate       0.0         0.0   
2        1.0                   0.5  Indeterminate       0.0         0.0   
3        1.0                   0.5  Indeterminate       0.0         0.0   
4        1.0                   0.5  Indeterminate       0.0         0.0   
..       ...                   ...             ...       ...         ...   
95       1.0                   0.5  Indeterminate       0.0         0.0   
96       1.0                   0.5  Indeterminate       0.0         0.0   
97       1.0                   0.5  Indeterminate       0.0         0.0   
98       1.0                   0.5  Indeterminate       0.0         0.0   
99       1.0                   0.5  Indeterminate       0.0         0.0   

   UpperSlope  
0         0.0  
1         0.0  
2         0.0  
3         0.0  
4         0.0  
..        ...  
95        0.0  
96        0.0  
97        0.0  
98        0.0  
99        0.0  

[100 rows x 24 columns]
```

```python
# Calculate trends using the 'midpoint' reduction method
results_midpoint = trend_data.trends(
    seasons_per_year=12,
    trend_lengths=[10],
    end_years=[2009, 2019],
    reduction_method='midpoint'
)

print(results_midpoint)
```

Expected output:
```
   Frequency  SeasonsPeryear  TrendYear  TrendMonth  TrendSeason  TrendEnd  \
0   Monthly              12       2000           1            1      2009   
1   Monthly              12       2000           2            2      2009   
2   Monthly              12       2000           3            3      2009   
3   Monthly              12       2000           4            4      2009   
4   Monthly              12       2000           5            5      2009   
..      ...             ...        ...         ...          ...       ...   
95  Monthly              12       2008          12           12      2019   
96  Monthly              12       2009           1            1      2019   
97  Monthly              12       2009           2            2      2019   
98  Monthly              12       2009           3            3      2019   
99  Monthly              12       2009           4            4      2019   

   TrendLength  TrendPeriod  ValueCount  Minimum  ...  MK-S  MK-Variance  \
0           10  Jan 2000 to Dec 2009         10        0  ...     0.0          0.0   
1           10  Feb 2000 to Jan 2010         10        1  ...     0.0          0.0   
2           10  Mar 2000 to Feb 2010         10        2  ...     0.0          0.0   
3           10  Apr 2000 to Mar 2010         10        3  ...     0.0          0.0   
4           10  May 2000 to Apr 2010         10        4  ...     0.0          0.0   
..         ...                ...        ...      ...     ...          ...   
95          10  Dec 2008 to Nov 2018         10       95  ...     0.0          0.0   
96          10  Jan 2009 to Dec 2018         10       96  ...     0.0          0.0   
97          10  Feb 2009 to Jan 2019         10       97  ...     0.0          0.0   
98          10  Mar 2009 to Feb 2019         10       98  ...     0.0          0.0   
99          10  Apr 2009 to Mar 2019         10       99  ...     0.0          0.0   

   MK-pvalue  IncreasingLikelihood  TrendDirection  SenSlope  LowerSlope  \
0        1.0                   0.5  Indeterminate       0.0         0.0   
1        1.0                   0.5  Indeterminate       0.0         0.0   
2        1.0                   0.5  Indeterminate       0.0         0.0   
3        1.0                   0.5  Indeterminate       0.0         0.0   
4        1.0                   0.5  Indeterminate       0.0         0.0   
..       ...                   ...             ...       ...         ...   
95       1.0                   0.5  Indeterminate       0.0         0.0   
96       1.0                   0.5  Indeterminate       0.0         0.0   
97       1.0                   0.5  Indeterminate       0.0         0.0   
98       1.0                   0.5  Indeterminate       0.0         0.0   
99       1.0                   0.5  Indeterminate       0.0         0.0   

   UpperSlope  
0         0.0  
1         0.0  
2         0.0  
3         0.0  
4         0.0  
..        ...  
95        0.0  
96        0.0  
97        0.0  
98        0.0  
99        0.0  

[100 rows x 24 columns]
```
