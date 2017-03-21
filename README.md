# Python Markdown Test 3/20
----

# NASDAQ Stock Indices

#### March 2017
<u>Statistics 141B:</u> Data and Web Technologies<br>
<u>Contributors:</u> Jeremy Weidner, Weizhou Wang, Audrey Chu, and Yuji Mori

### I. Abstract

To study NASDAQ stock prices for the technology, finance, health care, and energy industry sectors across time.  With the application of python and utilization of the Yahoo! Finance API, Yahoo Query Language (YQL), and New York Times API, we will gather and clean out a dataset for a time period of ten years for approximately 1770 companies.  Our data will incorporate the closing prices for each day and then average these prices for each respective sector.  In analyzing the stock prices, we will use interactive data visualization as well as attempt to create a time series ARIMA (Autoregressive Integrated Moving Average) model.

### II. Questions of Interest
- How do stock prices differ among industry sectors?
- Can we explain unusual trends in past prices by relating them to major historical events?
- Which month for which sector has the least volatility?


```python
import sys
import csv
import json
import requests
import requests_cache
import numpy as np
import pandas as pd
#from yahoo_finance import Share
from pprint import pprint 
from datetime import datetime
import matplotlib.pyplot as plt
import math

import matplotlib as mpl
%matplotlib inline

import seaborn as sns
sns.set_style('white', {"xtick.major.size": 2, "ytick.major.size": 2})
flatui = ["#9b59b6", "#3498db", "#95a5a6", "#e74c3c", "#34495e", "#2ecc71","#f4cae4"]
sns.set_palette(sns.color_palette(flatui,7))

import missingno as msno

#this is for time series
from  __future__ import print_function
from scipy import stats
import statsmodels.api as sm
from statsmodels.graphics.api import qqplot

#this is for plotting the prices
import plotly
plotly.tools.set_credentials_file(username="audchu",api_key="fPSZjEJ6wqYjzolZ8wNI")
import plotly.plotly as py
import plotly.graph_objs as go
from datetime import datetime
from pandas_datareader import data,wb

#this is for streaming plot
from plotly.grid_objs import Grid,Column
import time
```


```python
requests_cache.install_cache('cache')
```


```python
# Yahoo! YQL API
PUBLIC_API_URL = 'https://query.yahooapis.com/v1/public/yql'
OAUTH_API_URL = 'https://query.yahooapis.com/v1/yql'
DATATABLES_URL = 'store://datatables.org/alltableswithkeys'

def myreq(ticker, start, end):
    '''
    input ticker & dates as strings form 'YYYY-MM-DD'
    '''
    params = {'format':'json',
             'env':DATATABLES_URL}
    query = 'select * from yahoo.finance.historicaldata where symbol = "{}" and startDate = "{}" and endDate = "{}"'.format(ticker,start, end)
    params.update({'q':query})
    req = requests.get(PUBLIC_API_URL, params=params)
    req.raise_for_status()
    req = req.json()
    if req['query']['count'] > 0:
        result = req['query']['results']['quote']
        return result
    else:
        pass
```


```python
def price2(ticker):
    """
    Return closing prices for stocks from years 2006 to 2016.
    """
    
    date=[]
    price=[]
    report = []
    
    years = [2006,2007,2008,2009,2010,2011,2012,2013,2014,2015,2016]
    for x in range(len(years)):
        c = myreq(ticker,'{}-01-01'.format(years[x]),'{}-12-31'.format(years[x]))
        try:
        
            for i in range(0,len(c)):
                date.append(pd.to_datetime(c[i]["Date"]))
                price.append(float(c[i][u'Close']))
                datef = pd.DataFrame(date)
                pricef = pd.DataFrame(price)
                table1 = pd.concat([datef,pricef],axis = 1)
                table1.columns = ['Date', ticker]
                table1 = table1.set_index("Date")
            
        except Exception:
            table1 = pd.DataFrame()
    
    return table1
```


```python
csv = pd.read_csv('./companylist.csv')
# We want to keep "Finance, Health Care, Technology, Energy"
newcsv = csv[csv["Sector"].isin(["Finance", "Energy","Health Care","Technology"])].reset_index()
del newcsv["index"]
```


```python
whole_list = newcsv['Symbol']
```


```python
'''
for l in whole_list:
    get = price2(l)
    try:
        df = pd.concat([df,get],axis = 1)    # concat. by column 
    except NameError:
        df = pd.DataFrame(get)    # initialize automatically
# SAVE THE RESULT LOCALLY:
df.to_pickle('mydf')
'''
```




    "\nfor l in whole_list:\n    get = price2(l)\n    try:\n        df = pd.concat([df,get],axis = 1)    # concat. by column \n    except NameError:\n        df = pd.DataFrame(get)    # initialize automatically\n# SAVE THE RESULT LOCALLY:\ndf.to_pickle('mydf')\n"




```python
df = pd.read_pickle('mydf')
```


```python
# This allows us to control size of a dataframe displayed to examine our data in depth
pd.options.display.max_columns = 20
pd.options.display.max_rows = 30
```


```python
df.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>PIH</th>
      <th>FCCY</th>
      <th>SRCE</th>
      <th>VNET</th>
      <th>TWOU</th>
      <th>JOBS</th>
      <th>ABEO</th>
      <th>ABIL</th>
      <th>ABMD</th>
      <th>AXAS</th>
      <th>...</th>
      <th>ZLTQ</th>
      <th>ZN</th>
      <th>ZION</th>
      <th>ZIONW</th>
      <th>ZIOP</th>
      <th>ZIXI</th>
      <th>ZGNX</th>
      <th>ZSAN</th>
      <th>ZYNE</th>
      <th>ZNGA</th>
    </tr>
    <tr>
      <th>Date</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2006-01-02</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2006-01-03</th>
      <td>NaN</td>
      <td>21.499973</td>
      <td>25.830002</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>14.95</td>
      <td>0.51</td>
      <td>NaN</td>
      <td>9.35</td>
      <td>5.55</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>76.480003</td>
      <td>NaN</td>
      <td>3.60</td>
      <td>1.93</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2006-01-04</th>
      <td>NaN</td>
      <td>21.499973</td>
      <td>25.659998</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>14.79</td>
      <td>0.47</td>
      <td>NaN</td>
      <td>9.62</td>
      <td>5.55</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>77.019997</td>
      <td>NaN</td>
      <td>4.00</td>
      <td>2.04</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2006-01-05</th>
      <td>NaN</td>
      <td>20.999980</td>
      <td>25.820004</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>16.15</td>
      <td>0.46</td>
      <td>NaN</td>
      <td>9.55</td>
      <td>5.81</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>77.720001</td>
      <td>NaN</td>
      <td>4.00</td>
      <td>2.20</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2006-01-06</th>
      <td>NaN</td>
      <td>20.519969</td>
      <td>25.950002</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>17.08</td>
      <td>0.45</td>
      <td>NaN</td>
      <td>9.75</td>
      <td>5.95</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>78.529999</td>
      <td>NaN</td>
      <td>4.25</td>
      <td>2.09</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 1675 columns</p>
</div>




```python
final = newcsv.reset_index()
df_long = df.transpose()
sector  = final[['Symbol','Sector']]
sector = sector.set_index('Symbol')
```


```python
final = df_long.join(sector)
```


```python
final.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>2006-01-02 00:00:00</th>
      <th>2006-01-03 00:00:00</th>
      <th>2006-01-04 00:00:00</th>
      <th>2006-01-05 00:00:00</th>
      <th>2006-01-06 00:00:00</th>
      <th>2006-01-09 00:00:00</th>
      <th>2006-01-10 00:00:00</th>
      <th>2006-01-11 00:00:00</th>
      <th>2006-01-12 00:00:00</th>
      <th>2006-01-13 00:00:00</th>
      <th>...</th>
      <th>2016-12-20 00:00:00</th>
      <th>2016-12-21 00:00:00</th>
      <th>2016-12-22 00:00:00</th>
      <th>2016-12-23 00:00:00</th>
      <th>2016-12-26 00:00:00</th>
      <th>2016-12-27 00:00:00</th>
      <th>2016-12-28 00:00:00</th>
      <th>2016-12-29 00:00:00</th>
      <th>2016-12-30 00:00:00</th>
      <th>Sector</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>PIH</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>7.601000</td>
      <td>7.583000</td>
      <td>7.400000</td>
      <td>7.650000</td>
      <td>NaN</td>
      <td>7.400000</td>
      <td>7.400000</td>
      <td>7.250000</td>
      <td>7.800000</td>
      <td>Finance</td>
    </tr>
    <tr>
      <th>FCCY</th>
      <td>NaN</td>
      <td>21.499973</td>
      <td>21.499973</td>
      <td>20.999980</td>
      <td>20.519969</td>
      <td>20.249976</td>
      <td>19.999980</td>
      <td>20.369968</td>
      <td>20.000001</td>
      <td>20.779999</td>
      <td>...</td>
      <td>17.549999</td>
      <td>17.350000</td>
      <td>17.280001</td>
      <td>17.350000</td>
      <td>NaN</td>
      <td>18.100000</td>
      <td>18.250000</td>
      <td>18.000000</td>
      <td>18.700001</td>
      <td>Finance</td>
    </tr>
    <tr>
      <th>SRCE</th>
      <td>NaN</td>
      <td>25.830002</td>
      <td>25.659998</td>
      <td>25.820004</td>
      <td>25.950002</td>
      <td>25.999997</td>
      <td>25.999997</td>
      <td>25.999997</td>
      <td>25.940002</td>
      <td>25.989997</td>
      <td>...</td>
      <td>45.000000</td>
      <td>44.439999</td>
      <td>44.400002</td>
      <td>44.200001</td>
      <td>NaN</td>
      <td>44.740002</td>
      <td>44.700001</td>
      <td>45.330002</td>
      <td>44.660000</td>
      <td>Finance</td>
    </tr>
    <tr>
      <th>VNET</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>7.320000</td>
      <td>7.100000</td>
      <td>6.990000</td>
      <td>7.050000</td>
      <td>NaN</td>
      <td>7.150000</td>
      <td>7.090000</td>
      <td>6.960000</td>
      <td>7.010000</td>
      <td>Technology</td>
    </tr>
    <tr>
      <th>TWOU</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>33.049999</td>
      <td>32.099998</td>
      <td>30.740000</td>
      <td>30.809999</td>
      <td>NaN</td>
      <td>30.549999</td>
      <td>30.340000</td>
      <td>29.770000</td>
      <td>30.150000</td>
      <td>Technology</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 2821 columns</p>
</div>




```python
hc = final[final['Sector'] == 'Health Care']
```


```python
# take median within groups for each recorded date:
avg_sector = final.groupby('Sector').median().reset_index('Sector')
avg_sector = avg_sector.set_index('Sector')
avg_sector = avg_sector.dropna(thresh=4, axis = 1) # this drops if a column does not have at least two non NA's
```


```python
# Dates as index for plotting
# This is basically the original DF (transposed and transposed back)
# but the columns are now the Sector averages.
avg_T = avg_sector.transpose()
```


```python
avg_T.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>Sector</th>
      <th>Energy</th>
      <th>Finance</th>
      <th>Health Care</th>
      <th>Technology</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2006-01-03 00:00:00</th>
      <td>13.565</td>
      <td>23.250000</td>
      <td>6.945685</td>
      <td>11.245</td>
    </tr>
    <tr>
      <th>2006-01-04 00:00:00</th>
      <td>13.460</td>
      <td>23.309999</td>
      <td>6.925000</td>
      <td>11.655</td>
    </tr>
    <tr>
      <th>2006-01-05 00:00:00</th>
      <td>13.750</td>
      <td>23.459999</td>
      <td>6.990000</td>
      <td>11.770</td>
    </tr>
    <tr>
      <th>2006-01-06 00:00:00</th>
      <td>13.700</td>
      <td>23.400000</td>
      <td>7.019992</td>
      <td>11.775</td>
    </tr>
    <tr>
      <th>2006-01-09 00:00:00</th>
      <td>13.790</td>
      <td>23.500000</td>
      <td>7.100000</td>
      <td>12.050</td>
    </tr>
  </tbody>
</table>
</div>




```python
def ts_slider(sector,sec_name):
    trace = go.Scatter(x=avg_T.index,y=sector)
    data = [trace]
    layout = dict(
        title=sec_name + ' Sector Closing Prices: Time series with Range Slider',
        xaxis=dict(
            rangeselector=dict(
                buttons=list([
                    dict(count=1,
                         label='1m',
                         step='month',
                         stepmode='backward'),
                    dict(count=6,
                         label='6m',
                         step='month',
                         stepmode='backward'),
                    dict(count=1,
                        label='YTD',
                        step='year',
                        stepmode='todate'),
                    dict(count=1,
                        label='1y',
                        step='year',
                        stepmode='backward'),
                    dict(step='all')
                ])
            ),
            rangeslider=dict(),
            type='date'
        )
    )

    fig = dict(data=data, layout=layout)
    return py.iplot(fig)
```


```python
ts_slider(avg_T.Energy,"Energy")
```




<iframe id="igraph" scrolling="no" style="border:none;" seamless="seamless" src="https://plot.ly/~audchu/58.embed" height="525px" width="100%"></iframe>




```python

```

## Volatility Analysis


```python
# A new DF for the difference between each day:
delta_df = pd.DataFrame()
for sect in avg_T.columns:
    delta_df[sect] = np.log(avg_T[sect].shift(1)) - np.log(avg_T[sect])
delta_df.columns = map(lambda name: '{} Changes'.format(name),avg_T.columns)

# On what day did the stock price spike the most?
abs(delta_df).idxmax()
```




    Energy Changes        2008-10-06
    Finance Changes       2008-12-01
    Health Care Changes   2008-11-19
    Technology Changes    2008-12-01
    dtype: datetime64[ns]




```python
shade = delta_df['Energy Changes']
```


```python
delta_df.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Energy Changes</th>
      <th>Finance Changes</th>
      <th>Health Care Changes</th>
      <th>Technology Changes</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2006-01-03</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2006-01-04</th>
      <td>0.007771</td>
      <td>-0.002577</td>
      <td>0.002982</td>
      <td>-0.035812</td>
    </tr>
    <tr>
      <th>2006-01-05</th>
      <td>-0.021316</td>
      <td>-0.006414</td>
      <td>-0.009343</td>
      <td>-0.009819</td>
    </tr>
    <tr>
      <th>2006-01-06</th>
      <td>0.003643</td>
      <td>0.002561</td>
      <td>-0.004282</td>
      <td>-0.000425</td>
    </tr>
    <tr>
      <th>2006-01-09</th>
      <td>-0.006548</td>
      <td>-0.004264</td>
      <td>-0.011333</td>
      <td>-0.023086</td>
    </tr>
  </tbody>
</table>
</div>




```python
plot_cols = list(delta_df)

# 2 axes for 2 subplots
fig, axes = plt.subplots(4,1, figsize=(10,7), sharex=True)
#delta_df[plot_cols].plot(subplots=True, ax=axes)
delta_df[plot_cols].plot(subplots=True, ax=axes)
#plt.ylim([-0.20,0.150])
plt.show()
```


![png](output_27_0.png)


### Changes of Average Stock Price by Month


```python
avg_T.index= pd.to_datetime(avg_T.index)
avg_month = avg_T.groupby([avg_T.index.year, avg_T.index.month]).mean()
avg_month.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Sector</th>
      <th>Energy</th>
      <th>Finance</th>
      <th>Health Care</th>
      <th>Technology</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th rowspan="5" valign="top">2006</th>
      <th>1</th>
      <td>14.183000</td>
      <td>23.605500</td>
      <td>7.159611</td>
      <td>11.686500</td>
    </tr>
    <tr>
      <th>2</th>
      <td>15.404737</td>
      <td>23.857619</td>
      <td>7.334495</td>
      <td>11.895263</td>
    </tr>
    <tr>
      <th>3</th>
      <td>14.699130</td>
      <td>23.953911</td>
      <td>7.656957</td>
      <td>12.523913</td>
    </tr>
    <tr>
      <th>4</th>
      <td>15.968421</td>
      <td>23.920786</td>
      <td>7.665814</td>
      <td>13.029474</td>
    </tr>
    <tr>
      <th>5</th>
      <td>17.850000</td>
      <td>23.418863</td>
      <td>7.096370</td>
      <td>12.887273</td>
    </tr>
  </tbody>
</table>
</div>




```python
plot_cols
```




    ['Energy Changes',
     'Finance Changes',
     'Health Care Changes',
     'Technology Changes']




```python
# A new DF for the difference between each month's average:
delta_avg = pd.DataFrame()

for sect in avg_month.columns:
    delta_avg[sect] = np.log(avg_month[sect].shift(1)) - np.log(avg_month[sect])
delta_avg.columns = map(lambda name: '{} Changes'.format(name),avg_month.columns)

col = []
for i in range(len(delta_avg)):
    dt = delta_avg.index[i] + (10, 10, 10, 10)
    dt_obj = datetime(*dt[0:6])
    col.append(pd.to_datetime(dt_obj))
```


```python

```


```python
delta_avg['Timestamp'] = col
```


```python
delta_avg = delta_avg.set_index('Timestamp')
```


```python
delta_avg
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Energy Changes</th>
      <th>Finance Changes</th>
      <th>Health Care Changes</th>
      <th>Technology Changes</th>
    </tr>
    <tr>
      <th>Timestamp</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2006-01-10 10:10:10</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2006-02-10 10:10:10</th>
      <td>-0.082631</td>
      <td>-0.010624</td>
      <td>-0.024133</td>
      <td>-0.017706</td>
    </tr>
    <tr>
      <th>2006-03-10 10:10:10</th>
      <td>0.046887</td>
      <td>-0.004028</td>
      <td>-0.043026</td>
      <td>-0.051500</td>
    </tr>
    <tr>
      <th>2006-04-10 10:10:10</th>
      <td>-0.082825</td>
      <td>0.001384</td>
      <td>-0.001156</td>
      <td>-0.039574</td>
    </tr>
    <tr>
      <th>2006-05-10 10:10:10</th>
      <td>-0.111390</td>
      <td>0.021206</td>
      <td>0.077187</td>
      <td>0.010974</td>
    </tr>
    <tr>
      <th>2006-06-10 10:10:10</th>
      <td>-0.022834</td>
      <td>0.014280</td>
      <td>0.042359</td>
      <td>0.089027</td>
    </tr>
    <tr>
      <th>2006-07-10 10:10:10</th>
      <td>-0.010213</td>
      <td>0.006151</td>
      <td>0.071923</td>
      <td>0.061305</td>
    </tr>
    <tr>
      <th>2006-08-10 10:10:10</th>
      <td>0.041277</td>
      <td>-0.007239</td>
      <td>0.080343</td>
      <td>0.004462</td>
    </tr>
    <tr>
      <th>2006-09-10 10:10:10</th>
      <td>0.035880</td>
      <td>-0.020717</td>
      <td>-0.092788</td>
      <td>-0.032277</td>
    </tr>
    <tr>
      <th>2006-10-10 10:10:10</th>
      <td>-0.067018</td>
      <td>-0.005338</td>
      <td>-0.065543</td>
      <td>-0.044895</td>
    </tr>
    <tr>
      <th>2006-11-10 10:10:10</th>
      <td>-0.031399</td>
      <td>-0.018008</td>
      <td>-0.040524</td>
      <td>-0.040460</td>
    </tr>
    <tr>
      <th>2006-12-10 10:10:10</th>
      <td>-0.029503</td>
      <td>0.007912</td>
      <td>0.019185</td>
      <td>-0.012394</td>
    </tr>
    <tr>
      <th>2007-01-10 10:10:10</th>
      <td>-0.021747</td>
      <td>0.021343</td>
      <td>-0.010596</td>
      <td>-0.010150</td>
    </tr>
    <tr>
      <th>2007-02-10 10:10:10</th>
      <td>-0.100102</td>
      <td>-0.004281</td>
      <td>-0.054437</td>
      <td>-0.078498</td>
    </tr>
    <tr>
      <th>2007-03-10 10:10:10</th>
      <td>0.020816</td>
      <td>0.042798</td>
      <td>0.067679</td>
      <td>0.027650</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>2015-10-10 10:10:10</th>
      <td>0.049518</td>
      <td>0.002704</td>
      <td>0.149391</td>
      <td>0.009233</td>
    </tr>
    <tr>
      <th>2015-11-10 10:10:10</th>
      <td>0.051396</td>
      <td>-0.019316</td>
      <td>-0.043443</td>
      <td>-0.023382</td>
    </tr>
    <tr>
      <th>2015-12-10 10:10:10</th>
      <td>0.186521</td>
      <td>0.014430</td>
      <td>0.022265</td>
      <td>0.002597</td>
    </tr>
    <tr>
      <th>2016-01-10 10:10:10</th>
      <td>0.272163</td>
      <td>0.045146</td>
      <td>0.184826</td>
      <td>0.120864</td>
    </tr>
    <tr>
      <th>2016-02-10 10:10:10</th>
      <td>0.188164</td>
      <td>0.033947</td>
      <td>0.142778</td>
      <td>0.042605</td>
    </tr>
    <tr>
      <th>2016-03-10 10:10:10</th>
      <td>-0.259750</td>
      <td>-0.042696</td>
      <td>-0.047555</td>
      <td>-0.056975</td>
    </tr>
    <tr>
      <th>2016-04-10 10:10:10</th>
      <td>-0.141077</td>
      <td>-0.011704</td>
      <td>-0.100264</td>
      <td>-0.039645</td>
    </tr>
    <tr>
      <th>2016-05-10 10:10:10</th>
      <td>-0.052477</td>
      <td>-0.002389</td>
      <td>0.071397</td>
      <td>0.026448</td>
    </tr>
    <tr>
      <th>2016-06-10 10:10:10</th>
      <td>0.022510</td>
      <td>0.006800</td>
      <td>-0.000141</td>
      <td>-0.032379</td>
    </tr>
    <tr>
      <th>2016-07-10 10:10:10</th>
      <td>-0.041249</td>
      <td>-0.020425</td>
      <td>0.013583</td>
      <td>-0.031263</td>
    </tr>
    <tr>
      <th>2016-08-10 10:10:10</th>
      <td>-0.155142</td>
      <td>-0.031638</td>
      <td>-0.029522</td>
      <td>-0.020205</td>
    </tr>
    <tr>
      <th>2016-09-10 10:10:10</th>
      <td>-0.163973</td>
      <td>-0.022106</td>
      <td>-0.045245</td>
      <td>-0.022420</td>
    </tr>
    <tr>
      <th>2016-10-10 10:10:10</th>
      <td>-0.068873</td>
      <td>-0.000642</td>
      <td>0.042778</td>
      <td>-0.023036</td>
    </tr>
    <tr>
      <th>2016-11-10 10:10:10</th>
      <td>0.010856</td>
      <td>-0.070580</td>
      <td>0.046707</td>
      <td>-0.022329</td>
    </tr>
    <tr>
      <th>2016-12-10 10:10:10</th>
      <td>-0.068674</td>
      <td>-0.105806</td>
      <td>0.034932</td>
      <td>-0.030212</td>
    </tr>
  </tbody>
</table>
<p>132 rows × 4 columns</p>
</div>




```python
plot_cols = list(delta_avg)

# 2 axes for 2 subplots
fig, axes = plt.subplots(4,1, figsize=(10,7), sharex=True)
#delta_df[plot_cols].plot(subplots=True, ax=axes)
delta_avg[plot_cols].plot(subplots=True, ax=axes)
#plt.ylim([-0.20,0.150])
plt.show()
```


![png](output_36_0.png)



```python

```


```python
(abs(delta_avg).iloc[1:,:].quantile(q=0.90, axis=0))

```




    Energy Changes         0.163191
    Finance Changes        0.059207
    Health Care Changes    0.118481
    Technology Changes     0.084176
    dtype: float64




```python
peak = delta_avg[(abs(delta_avg) >= 0.163191).any(axis=1)]
```


```python
peak.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Energy Changes</th>
      <th>Finance Changes</th>
      <th>Health Care Changes</th>
      <th>Technology Changes</th>
    </tr>
    <tr>
      <th>Timestamp</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2008-08-10 10:10:10</th>
      <td>0.271934</td>
      <td>-0.048996</td>
      <td>-0.069831</td>
      <td>-0.033595</td>
    </tr>
    <tr>
      <th>2008-10-10 10:10:10</th>
      <td>0.483709</td>
      <td>0.131039</td>
      <td>0.320536</td>
      <td>0.340418</td>
    </tr>
    <tr>
      <th>2008-11-10 10:10:10</th>
      <td>0.192015</td>
      <td>0.068335</td>
      <td>0.228251</td>
      <td>0.217602</td>
    </tr>
    <tr>
      <th>2009-03-10 10:10:10</th>
      <td>0.176872</td>
      <td>0.069709</td>
      <td>0.133102</td>
      <td>0.045064</td>
    </tr>
    <tr>
      <th>2009-04-10 10:10:10</th>
      <td>-0.167232</td>
      <td>-0.116181</td>
      <td>-0.137381</td>
      <td>-0.224170</td>
    </tr>
  </tbody>
</table>
</div>




```python
plot_cols = list(delta_avg)

fig, axes = plt.subplots(4,1, figsize=(10,7), sharex=True)
delta_avg[plot_cols].plot(subplots=True, ax=axes)

for shade in range(len(peak)):
    peak_bgn = peak.index[shade]
    if peak.index[shade].month == 12:
        year = peak.index[shade].year+1
        end = (year, 1, 10, 10, 10, 10)
        dt_obj = datetime(*end[0:6])
        peak_end = pd.to_datetime(dt_obj)
        
    else:
        mo = peak.index[shade].month + 1
        end = (peak.index[shade].year, mo, 10, 10, 10, 10)
        dt_obj = datetime(*end[0:6])
        peak_end = pd.to_datetime(dt_obj)
        
    for ax in axes:
        ax.axvspan(peak_bgn, peak_end, color=sns.xkcd_rgb['grey'], alpha=.5)
    
        ax.axhline(0, color='k', linestyle='-', linewidth=1)
```


![png](output_41_0.png)



```python
plot_cols = list(avg_T)

fig, axes = plt.subplots(4,1, figsize=(10,7), sharex=True)
avg_T[plot_cols].plot(subplots=True, ax=axes)

for shade in range(len(peak)):
    peak_bgn = peak.index[shade]
    if peak.index[shade].month == 12:
        year = peak.index[shade].year+1
        end = (year, 1, 10, 10, 10, 10)
        dt_obj = datetime(*end[0:6])
        peak_end = pd.to_datetime(dt_obj)
        
    else:
        mo = peak.index[shade].month + 1
        end = (peak.index[shade].year, mo, 10, 10, 10, 10)
        dt_obj = datetime(*end[0:6])
        peak_end = pd.to_datetime(dt_obj)
        
    for ax in axes:
        ax.axvspan(peak_bgn, peak_end, color=sns.xkcd_rgb['grey'], alpha=.5)
    
        ax.axhline(0, color='k', linestyle='-', linewidth=1)
```


![png](output_42_0.png)



```python
peak.index[0]
```




    Timestamp('2008-08-10 10:10:10')




```python
peak.index[1].month - peak.index[0].month == 0
```




    False




```python
peak_shade = []
for l in range(len(peak)-1):
    if peak.index[l+1].year == peak.index[1].year:
        if peak.index[l+1].month - peak.index[1].month == 1:
            start = peak.index[l]
        else:
            start = peak.index[l-1]
            peak_shade.append(start)
    else:
        start = peak.index[l-1]
        peak_shade.append(start)
```


```python
peak_shade
```




    [Timestamp('2016-09-10 10:10:10'),
     Timestamp('2008-10-10 10:10:10'),
     Timestamp('2008-11-10 10:10:10'),
     Timestamp('2009-03-10 10:10:10'),
     Timestamp('2009-04-10 10:10:10'),
     Timestamp('2009-12-10 10:10:10'),
     Timestamp('2011-08-10 10:10:10'),
     Timestamp('2014-12-10 10:10:10'),
     Timestamp('2015-12-10 10:10:10'),
     Timestamp('2016-01-10 10:10:10'),
     Timestamp('2016-02-10 10:10:10')]




```python
peak
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Energy Changes</th>
      <th>Finance Changes</th>
      <th>Health Care Changes</th>
      <th>Technology Changes</th>
    </tr>
    <tr>
      <th>Timestamp</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2008-08-10 10:10:10</th>
      <td>0.271934</td>
      <td>-0.048996</td>
      <td>-0.069831</td>
      <td>-0.033595</td>
    </tr>
    <tr>
      <th>2008-10-10 10:10:10</th>
      <td>0.483709</td>
      <td>0.131039</td>
      <td>0.320536</td>
      <td>0.340418</td>
    </tr>
    <tr>
      <th>2008-11-10 10:10:10</th>
      <td>0.192015</td>
      <td>0.068335</td>
      <td>0.228251</td>
      <td>0.217602</td>
    </tr>
    <tr>
      <th>2009-03-10 10:10:10</th>
      <td>0.176872</td>
      <td>0.069709</td>
      <td>0.133102</td>
      <td>0.045064</td>
    </tr>
    <tr>
      <th>2009-04-10 10:10:10</th>
      <td>-0.167232</td>
      <td>-0.116181</td>
      <td>-0.137381</td>
      <td>-0.224170</td>
    </tr>
    <tr>
      <th>2009-12-10 10:10:10</th>
      <td>-0.164439</td>
      <td>-0.008523</td>
      <td>-0.037643</td>
      <td>-0.058359</td>
    </tr>
    <tr>
      <th>2011-08-10 10:10:10</th>
      <td>0.388879</td>
      <td>0.103690</td>
      <td>0.199786</td>
      <td>0.153049</td>
    </tr>
    <tr>
      <th>2014-12-10 10:10:10</th>
      <td>0.316577</td>
      <td>-0.002436</td>
      <td>-0.008372</td>
      <td>-0.014644</td>
    </tr>
    <tr>
      <th>2015-12-10 10:10:10</th>
      <td>0.186521</td>
      <td>0.014430</td>
      <td>0.022265</td>
      <td>0.002597</td>
    </tr>
    <tr>
      <th>2016-01-10 10:10:10</th>
      <td>0.272163</td>
      <td>0.045146</td>
      <td>0.184826</td>
      <td>0.120864</td>
    </tr>
    <tr>
      <th>2016-02-10 10:10:10</th>
      <td>0.188164</td>
      <td>0.033947</td>
      <td>0.142778</td>
      <td>0.042605</td>
    </tr>
    <tr>
      <th>2016-03-10 10:10:10</th>
      <td>-0.259750</td>
      <td>-0.042696</td>
      <td>-0.047555</td>
      <td>-0.056975</td>
    </tr>
    <tr>
      <th>2016-09-10 10:10:10</th>
      <td>-0.163973</td>
      <td>-0.022106</td>
      <td>-0.045245</td>
      <td>-0.022420</td>
    </tr>
  </tbody>
</table>
</div>



## Test: Which month has the highest volatility?


```python
# double check, would it make more sense to look at sum of absolute changes?
# This wouldn't give us direction though.
abs_delta_df = abs(delta_df)
months = abs_delta_df.groupby(delta_df.index.month).sum()
months
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Energy Changes</th>
      <th>Finance Changes</th>
      <th>Health Care Changes</th>
      <th>Technology Changes</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>5.579697</td>
      <td>2.292274</td>
      <td>3.983668</td>
      <td>3.575874</td>
    </tr>
    <tr>
      <th>2</th>
      <td>5.153948</td>
      <td>1.725488</td>
      <td>3.177925</td>
      <td>2.995240</td>
    </tr>
    <tr>
      <th>3</th>
      <td>5.517229</td>
      <td>2.258931</td>
      <td>4.191325</td>
      <td>3.491156</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4.583842</td>
      <td>1.896602</td>
      <td>3.660335</td>
      <td>3.011856</td>
    </tr>
    <tr>
      <th>5</th>
      <td>5.179666</td>
      <td>1.909797</td>
      <td>3.654547</td>
      <td>3.306427</td>
    </tr>
    <tr>
      <th>6</th>
      <td>4.531683</td>
      <td>2.211246</td>
      <td>3.878905</td>
      <td>3.338665</td>
    </tr>
    <tr>
      <th>7</th>
      <td>4.570591</td>
      <td>1.928904</td>
      <td>3.694508</td>
      <td>3.224373</td>
    </tr>
    <tr>
      <th>8</th>
      <td>5.905949</td>
      <td>2.439180</td>
      <td>3.975516</td>
      <td>3.614457</td>
    </tr>
    <tr>
      <th>9</th>
      <td>5.347203</td>
      <td>2.091582</td>
      <td>3.754380</td>
      <td>3.259025</td>
    </tr>
    <tr>
      <th>10</th>
      <td>6.048092</td>
      <td>2.669991</td>
      <td>4.596439</td>
      <td>4.065775</td>
    </tr>
    <tr>
      <th>11</th>
      <td>5.547477</td>
      <td>2.488493</td>
      <td>3.521998</td>
      <td>4.112071</td>
    </tr>
    <tr>
      <th>12</th>
      <td>4.527409</td>
      <td>2.301069</td>
      <td>3.779710</td>
      <td>3.537954</td>
    </tr>
  </tbody>
</table>
</div>




```python
months["total"] = months.sum(axis=1)
months.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Energy Changes</th>
      <th>Finance Changes</th>
      <th>Health Care Changes</th>
      <th>Technology Changes</th>
      <th>total</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>5.579697</td>
      <td>2.292274</td>
      <td>3.983668</td>
      <td>3.575874</td>
      <td>15.431513</td>
    </tr>
    <tr>
      <th>2</th>
      <td>5.153948</td>
      <td>1.725488</td>
      <td>3.177925</td>
      <td>2.995240</td>
      <td>13.052601</td>
    </tr>
    <tr>
      <th>3</th>
      <td>5.517229</td>
      <td>2.258931</td>
      <td>4.191325</td>
      <td>3.491156</td>
      <td>15.458640</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4.583842</td>
      <td>1.896602</td>
      <td>3.660335</td>
      <td>3.011856</td>
      <td>13.152635</td>
    </tr>
    <tr>
      <th>5</th>
      <td>5.179666</td>
      <td>1.909797</td>
      <td>3.654547</td>
      <td>3.306427</td>
      <td>14.050437</td>
    </tr>
  </tbody>
</table>
</div>




```python
sns.set_style("white")
sns.set_context({"figure.figsize": (20, 10)})
```


```python
sns.barplot(x = months.index, y = months.total, color = "red")
health_plot = sns.barplot(x = months.index, y = months['Health Care Changes']+months['Energy Changes']+months['Finance Changes'], color = "yellow")
fin_plot = sns.barplot(x = months.index, y = months['Finance Changes']+months['Energy Changes'], color = "blue")
eng_plot = sns.barplot(x = months.index, y = months['Energy Changes'], color = "green")

topbar = plt.Rectangle((0,0),1,1,fc="red", edgecolor = 'none')
bottombar = plt.Rectangle((0,0),1,1,fc='#0000A3',  edgecolor = 'none')
l = plt.legend([bottombar, topbar], ['Energy Changes', 'Finance Changes'], loc=1, ncol = 2, prop={'size':16})
l.draw_frame(False)

#Optional code - Make plot look nicer
sns.despine(left=True)
eng_plot.set_ylabel("Total Absolute Closing Price Changs")
eng_plot.set_xlabel("Months")

#Set fonts to consistent 16pt size
for item in ([bottom_plot.xaxis.label, bottom_plot.yaxis.label] +
             bottom_plot.get_xticklabels() + bottom_plot.get_yticklabels()):
    item.set_fontsize(16)
```


    ---------------------------------------------------------------------------

    NameError                                 Traceback (most recent call last)

    <ipython-input-51-8d91579b026d> in <module>()
         16 #Set fonts to consistent 16pt size
         17 for item in ([bottom_plot.xaxis.label, bottom_plot.yaxis.label] +
    ---> 18              bottom_plot.get_xticklabels() + bottom_plot.get_yticklabels()):
         19     item.set_fontsize(16)


    NameError: name 'bottom_plot' is not defined



![png](output_52_1.png)



```python

```

## Subset Data of Dataframe


```python
# 261 not right number, why is our last day 12-30-2016? -261 maybe?
year = delta_df.iloc[-100:,:]

plot_cols = list(year)

# 2 axes for 2 subplots
fig, axes = plt.subplots(4,1, figsize=(10,7), sharex=True)
#delta_df[plot_cols].plot(subplots=True, ax=axes)
year[plot_cols].plot(subplots=True, ax=axes)
#plt.ylim([-0.20,0.150])
plt.show()
```


![png](output_55_0.png)


#### Mega-function with everything related to the NYT API inside (work in progress)


```python
from nytimesarticle import articleAPI
import time
# OLD KEY (hit my limit): api = articleAPI('2679a66fe8df4740b754f98e52ad068c')
api = articleAPI('e031fcaf03da4b3c949e505c4aa69a5b')
def news_articles(sector,start,end,pages):
    sector_df = pd.DataFrame()
    for i in range(pages):
        try:
            if sector == 'Health Care':
                sector_articles = api.search( 
                    q = 'Health',
                    fq = {
                        'news_desk':'Business',
                        'section_name':'Business',
                        'subject.contains':['Health','Drugs'],
                        'type_of_material':'News'
                    },
                    begin_date = start,
                    end_date = end,
                    sort = 'oldest',
                    page = i
                )
            if sector == 'Technology':
                sector_articles = api.search(
                    fq = {
                        'news_desk.contains':'Business',
                        'section_name':'Technology',
                        'subject.contains':['Acquisitions'],
                        'type_of_material':'News'
                    },
                    begin_date = start,
                    end_date = end,
                    sort = 'oldest',
                    page = i
                )
            if sector == 'Energy':
                sector_articles = api.search( 
                    q = 'Energy & Environment',
                    fq = {
                        'news_desk':'Business',
                        'subject.contains':['Energy','Renewable','Gas','Acquisitions'],
                        'section_name':'Business',
                        'type_of_material':'News'
                    }, 
                    begin_date = start,
                    end_date = end,
                    sort = 'oldest',
                    page = i
                )
            if sector == 'Finance':
                sector_articles = api.search( 
                    q = 'Finance',
                    fq = {
                        'news_desk':'Business',
                        'subject.contains':['Banking','Financial'],
                        'section_name':'Business',
                        'type_of_material':'News'
                    }, 
                    begin_date = start,
                    end_date = end,
                    sort = 'oldest',
                    page = i
                )
            df_i = sector_articles['response']['docs']
            sector_df = sector_df.append(df_i) 
            time.sleep(0.5)   # API only allows 5 calls per second. This slows it down!
        except KeyError:
            break
        except IndexError:
            break
    return sector_df.reset_index()
```

###### Important Notes:
**Asking for over 100 pages at once (necessary for a 10-year span) leads to unpredictable results.**
* Ideally I would do something like: ```news_articles('Health Care',20060101,20170101,500)```, but the function simply stops running around 110~120 pages.
* My solution: Split the desired time frame, make separate calls, concatenate the results, save locally
* Even with this work-around, it takes multiple tries to obtain the correct result.


```python
'''
The following code locally stores the news data. Please do not run this block!

healthcare_news1 = news_articles('Health Care',20060101,20091231,100)
healthcare_news2 = news_articles('Health Care',20100101,20131231,100)
healthcare_news3 = news_articles('Health Care',20140101,20161231,100)
healthcare_news = pd.concat([healthcare_news1,healthcare_news2,healthcare_news3],ignore_index=True)
healthcare_news['Sector'] = 'Health Care'
tech_news = news_articles('Technology',100)
tech_news['Sector'] = 'Technology'
energy_news1 = news_articles('Energy',20060101,20091231,100)
energy_news2 = news_articles('Energy',20100101,20131231,100)
energy_news3 = news_articles('Energy',20140101,20161231,100)
energy_news = pd.concat([energy_news1,energy_news2,energy_news3])
energy_news['Sector']='Energy'
finance_news1 = news_articles('Finance',20060101,20091231,100)
finance_news2 = news_articles('Finance',20100101,20131231,100)
finance_news3 = news_articles('Finance',20140101,20161231,100)
finance_news = pd.concat([finance_news1,finance_news2,finance_news3])
finance_news['Sector']='Finance'
all_news = pd.concat([healthcare_news,tech_news,energy_news,finance_news],ignore_index=True)
# all_news.to_pickle('news_df')
'''
```




    "\nThe following code locally stores the news data. Please do not run this block!\n\nhealthcare_news1 = news_articles('Health Care',20060101,20091231,100)\nhealthcare_news2 = news_articles('Health Care',20100101,20131231,100)\nhealthcare_news3 = news_articles('Health Care',20140101,20161231,100)\nhealthcare_news = pd.concat([healthcare_news1,healthcare_news2,healthcare_news3],ignore_index=True)\nhealthcare_news['Sector'] = 'Health Care'\ntech_news = news_articles('Technology',100)\ntech_news['Sector'] = 'Technology'\nenergy_news1 = news_articles('Energy',20060101,20091231,100)\nenergy_news2 = news_articles('Energy',20100101,20131231,100)\nenergy_news3 = news_articles('Energy',20140101,20161231,100)\nenergy_news = pd.concat([energy_news1,energy_news2,energy_news3])\nenergy_news['Sector']='Energy'\nfinance_news1 = news_articles('Finance',20060101,20091231,100)\nfinance_news2 = news_articles('Finance',20100101,20131231,100)\nfinance_news3 = news_articles('Finance',20140101,20161231,100)\nfinance_news = pd.concat([finance_news1,finance_news2,finance_news3])\nfinance_news['Sector']='Finance'\nall_news = pd.concat([healthcare_news,tech_news,energy_news,finance_news],ignore_index=True)\n# all_news.to_pickle('news_df')\n"




```python
all_news = pd.read_pickle('news_df')
# Convert dates to index
all_news['pub_date'] = pd.to_datetime(all_news.pub_date)
all_news = all_news.set_index(all_news['pub_date'])
```


```python
headlines = [d.get('main') for d in all_news.headline]
dates = list(pd.to_datetime(all_news['pub_date']))
# Pandas categorical objects have integer values mapped to them:
sector_levels = all_news['Sector'].astype('category').cat.codes

# Data Frame for plot.ly Timeline:
pltdf = pd.DataFrame(
    {'Date':dates,
     'Title':headlines,
     'Sector':all_news.Sector,  
     'Level':sector_levels
    })
```

### Timeline:


```python
import plotly.plotly as py
import plotly.graph_objs as go

fig = {
    'data': [
        {
            'x': pltdf[pltdf['Sector']==sector]['Date'],
            'y': pltdf[pltdf['Sector']==sector]['Level'],
            'text': pltdf[pltdf['Sector']==sector]['Title'],
            'name': sector, 'mode': 'markers',
        } for sector in reversed(all_news['Sector'].astype('category').cat.categories)
        ]
    }
py.iplot(fig, filename='plotly_test')
```




<iframe id="igraph" scrolling="no" style="border:none;" seamless="seamless" src="https://plot.ly/~audchu/38.embed" height="525px" width="100%"></iframe>



# Analysis of Vocabulary in News Headlines
Using the peak changes as the periods of interest, we analyzed the words contained in the news articles headlines for those months.


```python
import string
import nltk
import regex as re
from collections import Counter
from nltk.corpus import stopwords
from nltk.stem.porter import PorterStemmer
import wordcloud
```


```python
tokenize = nltk.word_tokenize
def stem(tokens,stemmer = PorterStemmer().stem):
    return [stemmer(w.lower()) for w in tokens] 
# http://stackoverflow.com/questions/11066400/remove-punctuation-from-unicode-formatted-strings
def remove_punctuation(text):
    return re.sub(ur"\p{P}+", "", text)
```


```python
def CloudPlots(data):
    text = data.lead_paragraph.str.cat(sep = ' ')
    text = text.lower()
    text = remove_punctuation(text)
    tokens = tokenize(text)
    filtered = [w for w in tokens if not w in stopwords.words('english')]
    filtered = stem(filtered)
    count = Counter(filtered)
    # top 100 words:
    top_words = dict(count.most_common(100))
    cloud = wordcloud.WordCloud(background_color="white")
    wc = cloud.generate_from_frequencies(top_words)
    return(wc)
```


```python
peak
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Energy Changes</th>
      <th>Finance Changes</th>
      <th>Health Care Changes</th>
      <th>Technology Changes</th>
    </tr>
    <tr>
      <th>Timestamp</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2008-08-10 10:10:10</th>
      <td>0.271934</td>
      <td>-0.048996</td>
      <td>-0.069831</td>
      <td>-0.033595</td>
    </tr>
    <tr>
      <th>2008-10-10 10:10:10</th>
      <td>0.483709</td>
      <td>0.131039</td>
      <td>0.320536</td>
      <td>0.340418</td>
    </tr>
    <tr>
      <th>2008-11-10 10:10:10</th>
      <td>0.192015</td>
      <td>0.068335</td>
      <td>0.228251</td>
      <td>0.217602</td>
    </tr>
    <tr>
      <th>2009-03-10 10:10:10</th>
      <td>0.176872</td>
      <td>0.069709</td>
      <td>0.133102</td>
      <td>0.045064</td>
    </tr>
    <tr>
      <th>2009-04-10 10:10:10</th>
      <td>-0.167232</td>
      <td>-0.116181</td>
      <td>-0.137381</td>
      <td>-0.224170</td>
    </tr>
    <tr>
      <th>2009-12-10 10:10:10</th>
      <td>-0.164439</td>
      <td>-0.008523</td>
      <td>-0.037643</td>
      <td>-0.058359</td>
    </tr>
    <tr>
      <th>2011-08-10 10:10:10</th>
      <td>0.388879</td>
      <td>0.103690</td>
      <td>0.199786</td>
      <td>0.153049</td>
    </tr>
    <tr>
      <th>2014-12-10 10:10:10</th>
      <td>0.316577</td>
      <td>-0.002436</td>
      <td>-0.008372</td>
      <td>-0.014644</td>
    </tr>
    <tr>
      <th>2015-12-10 10:10:10</th>
      <td>0.186521</td>
      <td>0.014430</td>
      <td>0.022265</td>
      <td>0.002597</td>
    </tr>
    <tr>
      <th>2016-01-10 10:10:10</th>
      <td>0.272163</td>
      <td>0.045146</td>
      <td>0.184826</td>
      <td>0.120864</td>
    </tr>
    <tr>
      <th>2016-02-10 10:10:10</th>
      <td>0.188164</td>
      <td>0.033947</td>
      <td>0.142778</td>
      <td>0.042605</td>
    </tr>
    <tr>
      <th>2016-03-10 10:10:10</th>
      <td>-0.259750</td>
      <td>-0.042696</td>
      <td>-0.047555</td>
      <td>-0.056975</td>
    </tr>
    <tr>
      <th>2016-09-10 10:10:10</th>
      <td>-0.163973</td>
      <td>-0.022106</td>
      <td>-0.045245</td>
      <td>-0.022420</td>
    </tr>
  </tbody>
</table>
</div>



**We have visualized two periods where the volatility persisted for an exceptional length:**
* 8/2008 to 11/2008
* 12/2015 to 3/2016


```python
# (1) Subset the data:
sample1 = all_news['2008-8-10 10:10:10':'2008-11-10 10:10:10']
sample1_sector = sample1.groupby("Sector")  # a groupby object
```


```python
fig = plt.figure()
fig.suptitle("NYT Most Used Terms: Aug.2008 to Nov.2008", fontsize=30)
for counter, sect in enumerate(all_news.Sector.unique()):
    wc = CloudPlots(sample1_sector.get_group(sect))
    ax = fig.add_subplot(2,2,counter+1)
    ax.set_title("%s Sector" %sect,fontsize=23)
    plt.imshow(wc)
    plt.axis("off")
```


![png](output_71_0.png)



```python
# (2) Subset the data:
sample2 = all_news['2015-12-10 10:10:10':'2016-03-10 10:10:10']
sample2_sector = sample2.groupby("Sector")
```


```python
fig = plt.figure()
fig.suptitle("NYT Most Used Terms: Dec.2015 to Mar.2016", fontsize=30)
for counter, sect in enumerate(all_news.Sector.unique()):
    wc = CloudPlots(sample2_sector.get_group(sect))
    ax = fig.add_subplot(2,2,counter+1)
    ax.set_title("%s Sector" %sect,fontsize=23)
    plt.imshow(wc)
    plt.axis("off")
```


![png](output_73_0.png)


# Time Series Analysis


```python
ts_eng = delta_df['Energy Changes']
# Why do we get an NA for Nov 1 2016?
# Need to change following date range
# ts_eng['2016-11-02':'2017-03-01']
```

## Check Stationarity

Dickey-Fuller Test: tests the null hypothesis of whether a u nit root is present in an autoregressive model.  


```python
from statsmodels.tsa.stattools import adfuller
def test_stationarity(timeseries):
    
    #Determing rolling statistics
    rolmean = pd.rolling_mean(timeseries, window=12)
    rolstd = pd.rolling_std(timeseries, window=12)

    #Plot rolling statistics:
    orig = plt.plot(timeseries, color='blue',label='Original')
    mean = plt.plot(rolmean, color='red', label='Rolling Mean')
    std = plt.plot(rolstd, color='black', label = 'Rolling Std')
    plt.legend(loc='best')
    plt.title('Rolling Mean & Standard Deviation')
    plt.show(block=False)
    
    #Perform Dickey-Fuller test:
    print('Results of Dickey-Fuller Test:')
    dftest = adfuller(timeseries, autolag='AIC')
    dfoutput = pd.Series(dftest[0:4], index=['Test Statistic','p-value','#Lags Used','Number of Observations Used'])
    for key,value in dftest[4].items():
        dfoutput['Critical Value (%s)'%key] = value
    print(dfoutput)

```

test_stationarity(ts_eng)


```python
testE = delta_df["Energy Changes"]
```

# Energy Sector Time Series


```python
E_log = np.log(avg_T.iloc[-365:,:]['Health Care'])
```


```python
E_log_diff = E_log - E_log.shift()
plt.plot(E_log_diff)
```


```python
fig = plt.figure(figsize=(8,8))
ax1 = fig.add_subplot(211) 
fig = sm.graphics.tsa.plot_acf(E_log_diff[1:].values.squeeze(),lags = 40,ax=ax1)
plt.ylim([-0.15,0.15])
ax2 = fig.add_subplot(212)
fig = sm.graphics.tsa.plot_pacf(E_log_diff[1:],lags =40, ax= ax2)
plt.ylim([-0.15,0.15])
```


```python
E_log_diff.dropna(inplace=True)
test_stationarity(E_log_diff)
```


```python

```
