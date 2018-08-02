

```python
import pandas as pd
import numpy as np
from datetime import datetime
import time
from sklearn import preprocessing
```


```python
## 倒入数据
```


```python
trad_flow = pd.read_csv(r'RFM_TRAD_FLOW.csv',encoding='gbk')
trad_flow.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>transID</th>
      <th>cumid</th>
      <th>time</th>
      <th>amount</th>
      <th>type_label</th>
      <th>type</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>9407</td>
      <td>10001</td>
      <td>14JUN09:17:58:34</td>
      <td>199.0</td>
      <td>正常</td>
      <td>Normal</td>
    </tr>
    <tr>
      <th>1</th>
      <td>9625</td>
      <td>10001</td>
      <td>16JUN09:15:09:13</td>
      <td>369.0</td>
      <td>正常</td>
      <td>Normal</td>
    </tr>
    <tr>
      <th>2</th>
      <td>11837</td>
      <td>10001</td>
      <td>01JUL09:14:50:36</td>
      <td>369.0</td>
      <td>正常</td>
      <td>Normal</td>
    </tr>
    <tr>
      <th>3</th>
      <td>26629</td>
      <td>10001</td>
      <td>14DEC09:18:05:32</td>
      <td>359.0</td>
      <td>正常</td>
      <td>Normal</td>
    </tr>
    <tr>
      <th>4</th>
      <td>30850</td>
      <td>10001</td>
      <td>12APR10:13:02:20</td>
      <td>399.0</td>
      <td>正常</td>
      <td>Normal</td>
    </tr>
  </tbody>
</table>
</div>




```python
trad_flow.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 26662 entries, 0 to 26661
    Data columns (total 6 columns):
    transID       26662 non-null int64
    cumid         26662 non-null int64
    time          26662 non-null object
    amount        26662 non-null float64
    type_label    26662 non-null object
    type          26662 non-null object
    dtypes: float64(1), int64(2), object(3)
    memory usage: 1.2+ MB



```python
#RFM模型
#R 最近一次交易时间，值越大，表示不活跃，值越小，表示活跃
#F 最近的交易频率，表示有没有兴趣
#M 最近的交易金额，表示有没有价值
```


```python
## 通过计算F反应客户对打折产品的偏好
```


```python
F = trad_flow.groupby(['cumid','type'])[['transID']].count()
F.head(10)
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th>transID</th>
    </tr>
    <tr>
      <th>cumid</th>
      <th>type</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th rowspan="4" valign="top">10001</th>
      <th>Normal</th>
      <td>15</td>
    </tr>
    <tr>
      <th>Presented</th>
      <td>8</td>
    </tr>
    <tr>
      <th>Special_offer</th>
      <td>2</td>
    </tr>
    <tr>
      <th>returned_goods</th>
      <td>2</td>
    </tr>
    <tr>
      <th rowspan="3" valign="top">10002</th>
      <th>Normal</th>
      <td>12</td>
    </tr>
    <tr>
      <th>Presented</th>
      <td>5</td>
    </tr>
    <tr>
      <th>returned_goods</th>
      <td>1</td>
    </tr>
    <tr>
      <th rowspan="3" valign="top">10003</th>
      <th>Normal</th>
      <td>15</td>
    </tr>
    <tr>
      <th>Presented</th>
      <td>8</td>
    </tr>
    <tr>
      <th>Special_offer</th>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>




```python
#数据透视
F_trans = pd.pivot_table(F,index='cumid',columns='type',values='transID')
F_trans.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>type</th>
      <th>Normal</th>
      <th>Presented</th>
      <th>Special_offer</th>
      <th>returned_goods</th>
    </tr>
    <tr>
      <th>cumid</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>10001</th>
      <td>15.0</td>
      <td>8.0</td>
      <td>2.0</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>10002</th>
      <td>12.0</td>
      <td>5.0</td>
      <td>NaN</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>10003</th>
      <td>15.0</td>
      <td>8.0</td>
      <td>1.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>10004</th>
      <td>15.0</td>
      <td>12.0</td>
      <td>2.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>10005</th>
      <td>8.0</td>
      <td>5.0</td>
      <td>NaN</td>
      <td>1.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
F_trans['Special_offer']=F_trans['Special_offer'].fillna(0)
F_trans.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>type</th>
      <th>Normal</th>
      <th>Presented</th>
      <th>Special_offer</th>
      <th>returned_goods</th>
    </tr>
    <tr>
      <th>cumid</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>10001</th>
      <td>15.0</td>
      <td>8.0</td>
      <td>2.0</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>10002</th>
      <td>12.0</td>
      <td>5.0</td>
      <td>0.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>10003</th>
      <td>15.0</td>
      <td>8.0</td>
      <td>1.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>10004</th>
      <td>15.0</td>
      <td>12.0</td>
      <td>2.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>10005</th>
      <td>8.0</td>
      <td>5.0</td>
      <td>0.0</td>
      <td>1.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
F_trans['interest']=F_trans['Special_offer']/(F_trans['Special_offer']+F_trans['Normal'])
F_trans.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>type</th>
      <th>Normal</th>
      <th>Presented</th>
      <th>Special_offer</th>
      <th>returned_goods</th>
      <th>interest</th>
    </tr>
    <tr>
      <th>cumid</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>10001</th>
      <td>15.0</td>
      <td>8.0</td>
      <td>2.0</td>
      <td>2.0</td>
      <td>0.117647</td>
    </tr>
    <tr>
      <th>10002</th>
      <td>12.0</td>
      <td>5.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>10003</th>
      <td>15.0</td>
      <td>8.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.062500</td>
    </tr>
    <tr>
      <th>10004</th>
      <td>15.0</td>
      <td>12.0</td>
      <td>2.0</td>
      <td>1.0</td>
      <td>0.117647</td>
    </tr>
    <tr>
      <th>10005</th>
      <td>8.0</td>
      <td>5.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.000000</td>
    </tr>
  </tbody>
</table>
</div>




```python
## 通过计算M反应客户的价值信息
```


```python
M=trad_flow.groupby(['cumid','type','type_label'])[['amount']].sum()
M.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th></th>
      <th>amount</th>
    </tr>
    <tr>
      <th>cumid</th>
      <th>type</th>
      <th>type_label</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th rowspan="4" valign="top">10001</th>
      <th>Normal</th>
      <th>正常</th>
      <td>3608.0</td>
    </tr>
    <tr>
      <th>Presented</th>
      <th>赠送</th>
      <td>0.0</td>
    </tr>
    <tr>
      <th>Special_offer</th>
      <th>特价</th>
      <td>420.0</td>
    </tr>
    <tr>
      <th>returned_goods</th>
      <th>退货</th>
      <td>-694.0</td>
    </tr>
    <tr>
      <th>10002</th>
      <th>Normal</th>
      <th>正常</th>
      <td>1894.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
M_trans=pd.pivot_table(M,index='cumid',columns='type',values='amount')
M_trans['Special_offer']=M_trans['Special_offer'].fillna(0)
M_trans['returned_goods']=M_trans['returned_goods'].fillna(0)
M_trans['value']=M_trans['Normal']+M_trans['Special_offer']+M_trans['returned_goods']
M_trans.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>type</th>
      <th>Normal</th>
      <th>Presented</th>
      <th>Special_offer</th>
      <th>returned_goods</th>
      <th>value</th>
    </tr>
    <tr>
      <th>cumid</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>10001</th>
      <td>3608.0</td>
      <td>0.0</td>
      <td>420.0</td>
      <td>-694.0</td>
      <td>3334.0</td>
    </tr>
    <tr>
      <th>10002</th>
      <td>1894.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-242.0</td>
      <td>1652.0</td>
    </tr>
    <tr>
      <th>10003</th>
      <td>3503.0</td>
      <td>0.0</td>
      <td>156.0</td>
      <td>-224.0</td>
      <td>3435.0</td>
    </tr>
    <tr>
      <th>10004</th>
      <td>2979.0</td>
      <td>0.0</td>
      <td>373.0</td>
      <td>-40.0</td>
      <td>3312.0</td>
    </tr>
    <tr>
      <th>10005</th>
      <td>2368.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-249.0</td>
      <td>2119.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
## 通过计算R反应客户是否为沉默客户
```


```python
def to_time(t):
    out_t = time.mktime(time.strptime(t,'%d%b%y:%H:%M:%S'))
    return out_t
```


```python
trad_flow['time_new']=trad_flow.time.apply(to_time)
trad_flow.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>transID</th>
      <th>cumid</th>
      <th>time</th>
      <th>amount</th>
      <th>type_label</th>
      <th>type</th>
      <th>time_new</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>9407</td>
      <td>10001</td>
      <td>14JUN09:17:58:34</td>
      <td>199.0</td>
      <td>正常</td>
      <td>Normal</td>
      <td>1.244974e+09</td>
    </tr>
    <tr>
      <th>1</th>
      <td>9625</td>
      <td>10001</td>
      <td>16JUN09:15:09:13</td>
      <td>369.0</td>
      <td>正常</td>
      <td>Normal</td>
      <td>1.245136e+09</td>
    </tr>
    <tr>
      <th>2</th>
      <td>11837</td>
      <td>10001</td>
      <td>01JUL09:14:50:36</td>
      <td>369.0</td>
      <td>正常</td>
      <td>Normal</td>
      <td>1.246431e+09</td>
    </tr>
    <tr>
      <th>3</th>
      <td>26629</td>
      <td>10001</td>
      <td>14DEC09:18:05:32</td>
      <td>359.0</td>
      <td>正常</td>
      <td>Normal</td>
      <td>1.260785e+09</td>
    </tr>
    <tr>
      <th>4</th>
      <td>30850</td>
      <td>10001</td>
      <td>12APR10:13:02:20</td>
      <td>399.0</td>
      <td>正常</td>
      <td>Normal</td>
      <td>1.271049e+09</td>
    </tr>
  </tbody>
</table>
</div>




```python
R = trad_flow.groupby(['cumid'])[['time_new']].max()
R.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>time_new</th>
    </tr>
    <tr>
      <th>cumid</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>10001</th>
      <td>1.284699e+09</td>
    </tr>
    <tr>
      <th>10002</th>
      <td>1.278129e+09</td>
    </tr>
    <tr>
      <th>10003</th>
      <td>1.282983e+09</td>
    </tr>
    <tr>
      <th>10004</th>
      <td>1.283057e+09</td>
    </tr>
    <tr>
      <th>10005</th>
      <td>1.282127e+09</td>
    </tr>
  </tbody>
</table>
</div>




```python
## 构建模型，筛选目标客户
```


```python
threshold = pd.qcut(F_trans['interest'], 2, retbins=True)[1][1]
binarizer = preprocessing.Binarizer(threshold=threshold)
interest_q = pd.DataFrame(binarizer.transform(F_trans['interest'].values.reshape(-1, 1)))
interest_q.index=F_trans.index
interest_q.columns=["interest"]
```


```python
threshold = pd.qcut(M_trans['value'], 2, retbins=True)[1][1]
binarizer = preprocessing.Binarizer(threshold=threshold)
value_q = pd.DataFrame(binarizer.transform(M_trans['value'].reshape(-1, 1)))
value_q.index=M_trans.index
value_q.columns=["value"]
```

    /Users/wangdinghao/anaconda3/lib/python3.6/site-packages/ipykernel_launcher.py:3: FutureWarning: reshape is deprecated and will raise in a subsequent release. Please use .values.reshape(...) instead
      This is separate from the ipykernel package so we can avoid doing imports until



```python
threshold = pd.qcut(R["time_new"], 2, retbins=True)[1][1]
binarizer = preprocessing.Binarizer(threshold=threshold)
time_new_q = pd.DataFrame(binarizer.transform(R["time_new"].reshape(-1, 1)))
time_new_q.index=R.index
time_new_q.columns=["time"]
```

    /Users/wangdinghao/anaconda3/lib/python3.6/site-packages/ipykernel_launcher.py:3: FutureWarning: reshape is deprecated and will raise in a subsequent release. Please use .values.reshape(...) instead
      This is separate from the ipykernel package so we can avoid doing imports until



```python
analysis = pd.concat([interest_q,value_q,time_new_q],axis=1)
```


```python
analysis = analysis[['interest','value','time']]
```


```python
analysis.head(10)
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>interest</th>
      <th>value</th>
      <th>time</th>
    </tr>
    <tr>
      <th>cumid</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>10001</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>10002</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>10003</th>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>10004</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>10005</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>10006</th>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>10007</th>
      <td>0.0</td>
      <td>1.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>10008</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>10009</th>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>10010</th>
      <td>1.0</td>
      <td>0.0</td>
      <td>1.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
label = {
        (0,0,0):'无兴趣-低价值-沉默',
        (1,0,0):'有兴趣-低价值-沉默',
        (1,0,1):'有兴趣-低价值-活跃',
        (0,0,1):'无兴趣-低价值-活跃',
        (0,1,0):'无兴趣-高价值-沉默',
        (1,1,0):'有兴趣-高价值-沉默',
        (1,1,1):'有兴趣-高价值-活跃',
        (0,1,1):'无兴趣-高价值-活跃'
}
```


```python
analysis['label'] = analysis[['interest','value','time']].apply(lambda x: label[(x[0],x[1],x[2])], axis = 1)
analysis.head(10)
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>interest</th>
      <th>value</th>
      <th>time</th>
      <th>label</th>
    </tr>
    <tr>
      <th>cumid</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>10001</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>有兴趣-高价值-活跃</td>
    </tr>
    <tr>
      <th>10002</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>无兴趣-低价值-沉默</td>
    </tr>
    <tr>
      <th>10003</th>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>无兴趣-高价值-沉默</td>
    </tr>
    <tr>
      <th>10004</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>有兴趣-高价值-沉默</td>
    </tr>
    <tr>
      <th>10005</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>无兴趣-低价值-沉默</td>
    </tr>
    <tr>
      <th>10006</th>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>有兴趣-低价值-沉默</td>
    </tr>
    <tr>
      <th>10007</th>
      <td>0.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>无兴趣-高价值-活跃</td>
    </tr>
    <tr>
      <th>10008</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>有兴趣-高价值-活跃</td>
    </tr>
    <tr>
      <th>10009</th>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>无兴趣-高价值-沉默</td>
    </tr>
    <tr>
      <th>10010</th>
      <td>1.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>有兴趣-低价值-活跃</td>
    </tr>
  </tbody>
</table>
</div>


