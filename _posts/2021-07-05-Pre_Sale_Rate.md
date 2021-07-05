## 전국 지역별 민간아파트의 평균 분양률 데이터 분석
분양률(미분양)은 공동주택 매매가격 상승 및 하락의 바로미터입니다. \
오늘은 분양분석을 통해 지역별 부동산 전망예측 및 분양 유망지역을 분석하고자 합니다.

#### 분석대상 데이터
데이터기간 : 2015년 3분기 ~ 분기별 2021년 1분기\
조사기관 : KOSIS 국가통계포털\
데이터형태 : CSV


```python
import warnings
warnings.filterwarnings('ignore')
```


```python
import pandas as pd
import numpy as np
import re
import matplotlib.pyplot as plt

%matplotlib inline
# IPython 에서 제공하는 Rich output 에 대한 표현 방식
# Rich output : 도표와 같은 그림, 소리, 애니메이션 과 같은 결과물
# Jupyter notebook을 실행한 브라우저에서 그림 등 Rich output을 볼 수 있게 해줌
```


```python
# Window 의 한글 폰트 설정
plt.rc('font', family='NanumGothic') 
```


```python
pre_sale_rate = pd.read_csv('data/지역별_민간아파트_평균_초기분양률_20210705104023.csv', 
                       encoding='euc-kr', engine='python',  skiprows=1)
pre_sale_rate.shape
```




    (23, 19)




```python
pre_sale_rate.head()
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
      <th>시점</th>
      <th>소계</th>
      <th>서울</th>
      <th>인천</th>
      <th>경기</th>
      <th>부산</th>
      <th>대구</th>
      <th>광주</th>
      <th>대전</th>
      <th>울산</th>
      <th>세종</th>
      <th>강원</th>
      <th>충북</th>
      <th>충남</th>
      <th>전북</th>
      <th>전남</th>
      <th>경북</th>
      <th>경남</th>
      <th>제주</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2021 1/4</td>
      <td>94.8</td>
      <td>100.0</td>
      <td>-</td>
      <td>96.2</td>
      <td>-</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>-</td>
      <td>100.0</td>
      <td>41.2</td>
      <td>75.0</td>
      <td>100.0</td>
      <td>99.9</td>
      <td>79.0</td>
      <td>98.9</td>
      <td>91.0</td>
      <td>-</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2020 4/4</td>
      <td>96.6</td>
      <td>100.0</td>
      <td>94.3</td>
      <td>98.3</td>
      <td>100.0</td>
      <td>99.8</td>
      <td>100.0</td>
      <td>-</td>
      <td>88.7</td>
      <td>-</td>
      <td>76.4</td>
      <td>99.1</td>
      <td>98.8</td>
      <td>82.9</td>
      <td>74.8</td>
      <td>73.7</td>
      <td>99.8</td>
      <td>1.3</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2020 3/4</td>
      <td>96.4</td>
      <td>100.0</td>
      <td>97.6</td>
      <td>100.0</td>
      <td>94.5</td>
      <td>99.9</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>-</td>
      <td>93.1</td>
      <td>-</td>
      <td>63.3</td>
      <td>100.0</td>
      <td>99.2</td>
      <td>-</td>
      <td>69.9</td>
      <td>10.3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2020 2/4</td>
      <td>97.0</td>
      <td>100.0</td>
      <td>99.9</td>
      <td>100.0</td>
      <td>92.5</td>
      <td>100.0</td>
      <td>99.4</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>39.3</td>
      <td>-</td>
      <td>100.0</td>
      <td>-</td>
      <td>93.1</td>
      <td>-</td>
      <td>-</td>
      <td>22.2</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2020 1/4</td>
      <td>92.4</td>
      <td>100.0</td>
      <td>98.7</td>
      <td>99.6</td>
      <td>99.8</td>
      <td>86.5</td>
      <td>99.3</td>
      <td>100.0</td>
      <td>76.1</td>
      <td>-</td>
      <td>76.0</td>
      <td>76.5</td>
      <td>77.7</td>
      <td>94.6</td>
      <td>50.0</td>
      <td>-</td>
      <td>98.4</td>
      <td>-</td>
    </tr>
  </tbody>
</table>
</div>




```python
pre_sale_rate.tail()
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
      <th>시점</th>
      <th>소계</th>
      <th>서울</th>
      <th>인천</th>
      <th>경기</th>
      <th>부산</th>
      <th>대구</th>
      <th>광주</th>
      <th>대전</th>
      <th>울산</th>
      <th>세종</th>
      <th>강원</th>
      <th>충북</th>
      <th>충남</th>
      <th>전북</th>
      <th>전남</th>
      <th>경북</th>
      <th>경남</th>
      <th>제주</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>18</th>
      <td>2016 3/4</td>
      <td>72.0</td>
      <td>100.0</td>
      <td>64.4</td>
      <td>76.8</td>
      <td>90.7</td>
      <td>100.0</td>
      <td>83.9</td>
      <td>91.4</td>
      <td>73.9</td>
      <td>99.0</td>
      <td>78.5</td>
      <td>57.8</td>
      <td>31.1</td>
      <td>55.8</td>
      <td>73.5</td>
      <td>33.7</td>
      <td>57.0</td>
      <td>89.2</td>
    </tr>
    <tr>
      <th>19</th>
      <td>2016 2/4</td>
      <td>70.5</td>
      <td>99.9</td>
      <td>74.2</td>
      <td>67.8</td>
      <td>76.9</td>
      <td>93.5</td>
      <td>67.7</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>91.1</td>
      <td>69.7</td>
      <td>44.3</td>
      <td>82.5</td>
      <td>83.3</td>
      <td>47.7</td>
      <td>68.1</td>
      <td>-</td>
    </tr>
    <tr>
      <th>20</th>
      <td>2016 1/4</td>
      <td>78.6</td>
      <td>95.7</td>
      <td>89.4</td>
      <td>76.1</td>
      <td>88.5</td>
      <td>46.1</td>
      <td>46.3</td>
      <td>96.8</td>
      <td>89.0</td>
      <td>99.7</td>
      <td>92.4</td>
      <td>60.2</td>
      <td>55.5</td>
      <td>96.6</td>
      <td>91.1</td>
      <td>78.5</td>
      <td>75.4</td>
      <td>-</td>
    </tr>
    <tr>
      <th>21</th>
      <td>2015 4/4</td>
      <td>87.1</td>
      <td>98.8</td>
      <td>84.4</td>
      <td>86.4</td>
      <td>97.1</td>
      <td>92.8</td>
      <td>92.4</td>
      <td>-</td>
      <td>100.0</td>
      <td>96.9</td>
      <td>100.0</td>
      <td>89.3</td>
      <td>70.1</td>
      <td>78.7</td>
      <td>77.0</td>
      <td>90.0</td>
      <td>79.0</td>
      <td>100.0</td>
    </tr>
    <tr>
      <th>22</th>
      <td>2015 3/4</td>
      <td>87.7</td>
      <td>95.7</td>
      <td>83.3</td>
      <td>92.4</td>
      <td>90.8</td>
      <td>100.0</td>
      <td>97.9</td>
      <td>87.1</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>58.8</td>
      <td>49.3</td>
      <td>76.6</td>
      <td>83.7</td>
      <td>79.6</td>
      <td>92.4</td>
      <td>79.7</td>
      <td>100.0</td>
    </tr>
  </tbody>
</table>
</div>



## 결측치 대체하기 ffill,  bfill
###  뒤에 있는 값으로 대체 df.fillna(method='bfill') # Fill values backward
### 평균으로 채우기 df.fillna(df.mean())


```python
pre_sale_rate.iloc[:,2:] = pre_sale_rate.iloc[:,2:].apply(pd.to_numeric, errors='coerce').fillna(method='ffill')
pre_sale_rate.iloc[:,2:] = pre_sale_rate.iloc[:,2:].apply(pd.to_numeric, errors='coerce').fillna(method='bfill')
pre_sale_rate
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
      <th>시점</th>
      <th>소계</th>
      <th>서울</th>
      <th>인천</th>
      <th>경기</th>
      <th>부산</th>
      <th>대구</th>
      <th>광주</th>
      <th>대전</th>
      <th>울산</th>
      <th>세종</th>
      <th>강원</th>
      <th>충북</th>
      <th>충남</th>
      <th>전북</th>
      <th>전남</th>
      <th>경북</th>
      <th>경남</th>
      <th>제주</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2021 1/4</td>
      <td>94.8</td>
      <td>100.0</td>
      <td>94.3</td>
      <td>96.2</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>88.7</td>
      <td>100.0</td>
      <td>41.2</td>
      <td>75.0</td>
      <td>100.0</td>
      <td>99.9</td>
      <td>79.0</td>
      <td>98.9</td>
      <td>91.0</td>
      <td>1.3</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2020 4/4</td>
      <td>96.6</td>
      <td>100.0</td>
      <td>94.3</td>
      <td>98.3</td>
      <td>100.0</td>
      <td>99.8</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>88.7</td>
      <td>100.0</td>
      <td>76.4</td>
      <td>99.1</td>
      <td>98.8</td>
      <td>82.9</td>
      <td>74.8</td>
      <td>73.7</td>
      <td>99.8</td>
      <td>1.3</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2020 3/4</td>
      <td>96.4</td>
      <td>100.0</td>
      <td>97.6</td>
      <td>100.0</td>
      <td>94.5</td>
      <td>99.9</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>93.1</td>
      <td>99.1</td>
      <td>63.3</td>
      <td>100.0</td>
      <td>99.2</td>
      <td>73.7</td>
      <td>69.9</td>
      <td>10.3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2020 2/4</td>
      <td>97.0</td>
      <td>100.0</td>
      <td>99.9</td>
      <td>100.0</td>
      <td>92.5</td>
      <td>100.0</td>
      <td>99.4</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>39.3</td>
      <td>99.1</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>93.1</td>
      <td>73.7</td>
      <td>69.9</td>
      <td>22.2</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2020 1/4</td>
      <td>92.4</td>
      <td>100.0</td>
      <td>98.7</td>
      <td>99.6</td>
      <td>99.8</td>
      <td>86.5</td>
      <td>99.3</td>
      <td>100.0</td>
      <td>76.1</td>
      <td>100.0</td>
      <td>76.0</td>
      <td>76.5</td>
      <td>77.7</td>
      <td>94.6</td>
      <td>50.0</td>
      <td>73.7</td>
      <td>98.4</td>
      <td>22.2</td>
    </tr>
    <tr>
      <th>5</th>
      <td>2019 4/4</td>
      <td>91.7</td>
      <td>99.6</td>
      <td>99.7</td>
      <td>92.7</td>
      <td>100.0</td>
      <td>93.4</td>
      <td>94.4</td>
      <td>100.0</td>
      <td>76.1</td>
      <td>100.0</td>
      <td>44.1</td>
      <td>76.5</td>
      <td>80.8</td>
      <td>80.5</td>
      <td>90.8</td>
      <td>17.8</td>
      <td>98.4</td>
      <td>46.8</td>
    </tr>
    <tr>
      <th>6</th>
      <td>2019 3/4</td>
      <td>84.0</td>
      <td>99.6</td>
      <td>99.3</td>
      <td>89.2</td>
      <td>82.8</td>
      <td>86.6</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>76.1</td>
      <td>100.0</td>
      <td>31.5</td>
      <td>80.3</td>
      <td>63.2</td>
      <td>87.1</td>
      <td>87.0</td>
      <td>100.0</td>
      <td>53.3</td>
      <td>32.3</td>
    </tr>
    <tr>
      <th>7</th>
      <td>2019 2/4</td>
      <td>82.8</td>
      <td>91.3</td>
      <td>88.8</td>
      <td>82.7</td>
      <td>82.8</td>
      <td>81.5</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>76.1</td>
      <td>100.0</td>
      <td>79.3</td>
      <td>80.3</td>
      <td>95.2</td>
      <td>87.1</td>
      <td>70.5</td>
      <td>67.9</td>
      <td>23.8</td>
      <td>32.3</td>
    </tr>
    <tr>
      <th>8</th>
      <td>2019 1/4</td>
      <td>81.7</td>
      <td>97.4</td>
      <td>95.1</td>
      <td>78.7</td>
      <td>71.6</td>
      <td>99.9</td>
      <td>99.9</td>
      <td>94.5</td>
      <td>76.1</td>
      <td>100.0</td>
      <td>53.9</td>
      <td>37.0</td>
      <td>95.2</td>
      <td>78.4</td>
      <td>78.5</td>
      <td>65.9</td>
      <td>13.9</td>
      <td>41.3</td>
    </tr>
    <tr>
      <th>9</th>
      <td>2018 4/4</td>
      <td>85.6</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>95.3</td>
      <td>95.5</td>
      <td>97.2</td>
      <td>99.1</td>
      <td>94.5</td>
      <td>76.1</td>
      <td>100.0</td>
      <td>41.5</td>
      <td>93.3</td>
      <td>52.7</td>
      <td>89.4</td>
      <td>78.5</td>
      <td>56.1</td>
      <td>33.3</td>
      <td>41.3</td>
    </tr>
    <tr>
      <th>10</th>
      <td>2018 3/4</td>
      <td>84.1</td>
      <td>99.6</td>
      <td>95.3</td>
      <td>94.7</td>
      <td>59.7</td>
      <td>94.5</td>
      <td>99.1</td>
      <td>100.0</td>
      <td>76.1</td>
      <td>100.0</td>
      <td>92.9</td>
      <td>52.7</td>
      <td>45.6</td>
      <td>75.7</td>
      <td>89.8</td>
      <td>46.7</td>
      <td>23.3</td>
      <td>41.3</td>
    </tr>
    <tr>
      <th>11</th>
      <td>2018 2/4</td>
      <td>81.5</td>
      <td>99.7</td>
      <td>65.9</td>
      <td>87.3</td>
      <td>68.8</td>
      <td>100.0</td>
      <td>82.2</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>99.2</td>
      <td>49.4</td>
      <td>52.7</td>
      <td>12.0</td>
      <td>90.8</td>
      <td>85.5</td>
      <td>83.8</td>
      <td>20.0</td>
      <td>62.4</td>
    </tr>
    <tr>
      <th>12</th>
      <td>2018 1/4</td>
      <td>86.5</td>
      <td>100.0</td>
      <td>65.9</td>
      <td>87.6</td>
      <td>89.8</td>
      <td>95.5</td>
      <td>99.6</td>
      <td>100.0</td>
      <td>62.1</td>
      <td>99.2</td>
      <td>80.6</td>
      <td>70.2</td>
      <td>33.1</td>
      <td>91.9</td>
      <td>84.3</td>
      <td>49.5</td>
      <td>75.8</td>
      <td>62.4</td>
    </tr>
    <tr>
      <th>13</th>
      <td>2017 4/4</td>
      <td>81.2</td>
      <td>99.2</td>
      <td>100.0</td>
      <td>91.9</td>
      <td>79.8</td>
      <td>100.0</td>
      <td>99.4</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>81.0</td>
      <td>42.7</td>
      <td>33.0</td>
      <td>95.7</td>
      <td>14.3</td>
      <td>48.9</td>
      <td>50.2</td>
      <td>64.7</td>
    </tr>
    <tr>
      <th>14</th>
      <td>2017 3/4</td>
      <td>87.3</td>
      <td>100.0</td>
      <td>74.6</td>
      <td>99.1</td>
      <td>93.7</td>
      <td>100.0</td>
      <td>87.4</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>81.6</td>
      <td>73.1</td>
      <td>94.0</td>
      <td>75.0</td>
      <td>80.5</td>
      <td>37.0</td>
      <td>82.2</td>
      <td>48.5</td>
    </tr>
    <tr>
      <th>15</th>
      <td>2017 2/4</td>
      <td>74.9</td>
      <td>99.7</td>
      <td>60.1</td>
      <td>67.5</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>88.5</td>
      <td>100.0</td>
      <td>93.1</td>
      <td>63.9</td>
      <td>40.8</td>
      <td>67.4</td>
      <td>71.9</td>
      <td>37.0</td>
      <td>82.2</td>
      <td>34.5</td>
    </tr>
    <tr>
      <th>16</th>
      <td>2017 1/4</td>
      <td>88.2</td>
      <td>99.9</td>
      <td>71.2</td>
      <td>86.9</td>
      <td>95.9</td>
      <td>100.0</td>
      <td>87.1</td>
      <td>98.2</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>85.0</td>
      <td>91.2</td>
      <td>21.4</td>
      <td>99.6</td>
      <td>93.2</td>
      <td>59.7</td>
      <td>86.6</td>
      <td>82.5</td>
    </tr>
    <tr>
      <th>17</th>
      <td>2016 4/4</td>
      <td>85.8</td>
      <td>96.4</td>
      <td>96.6</td>
      <td>89.3</td>
      <td>100.0</td>
      <td>89.6</td>
      <td>98.8</td>
      <td>98.2</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>89.5</td>
      <td>24.4</td>
      <td>35.1</td>
      <td>82.6</td>
      <td>93.8</td>
      <td>37.4</td>
      <td>95.2</td>
      <td>72.1</td>
    </tr>
    <tr>
      <th>18</th>
      <td>2016 3/4</td>
      <td>72.0</td>
      <td>100.0</td>
      <td>64.4</td>
      <td>76.8</td>
      <td>90.7</td>
      <td>100.0</td>
      <td>83.9</td>
      <td>91.4</td>
      <td>73.9</td>
      <td>99.0</td>
      <td>78.5</td>
      <td>57.8</td>
      <td>31.1</td>
      <td>55.8</td>
      <td>73.5</td>
      <td>33.7</td>
      <td>57.0</td>
      <td>89.2</td>
    </tr>
    <tr>
      <th>19</th>
      <td>2016 2/4</td>
      <td>70.5</td>
      <td>99.9</td>
      <td>74.2</td>
      <td>67.8</td>
      <td>76.9</td>
      <td>93.5</td>
      <td>67.7</td>
      <td>91.4</td>
      <td>73.9</td>
      <td>99.0</td>
      <td>91.1</td>
      <td>69.7</td>
      <td>44.3</td>
      <td>82.5</td>
      <td>83.3</td>
      <td>47.7</td>
      <td>68.1</td>
      <td>89.2</td>
    </tr>
    <tr>
      <th>20</th>
      <td>2016 1/4</td>
      <td>78.6</td>
      <td>95.7</td>
      <td>89.4</td>
      <td>76.1</td>
      <td>88.5</td>
      <td>46.1</td>
      <td>46.3</td>
      <td>96.8</td>
      <td>89.0</td>
      <td>99.7</td>
      <td>92.4</td>
      <td>60.2</td>
      <td>55.5</td>
      <td>96.6</td>
      <td>91.1</td>
      <td>78.5</td>
      <td>75.4</td>
      <td>89.2</td>
    </tr>
    <tr>
      <th>21</th>
      <td>2015 4/4</td>
      <td>87.1</td>
      <td>98.8</td>
      <td>84.4</td>
      <td>86.4</td>
      <td>97.1</td>
      <td>92.8</td>
      <td>92.4</td>
      <td>96.8</td>
      <td>100.0</td>
      <td>96.9</td>
      <td>100.0</td>
      <td>89.3</td>
      <td>70.1</td>
      <td>78.7</td>
      <td>77.0</td>
      <td>90.0</td>
      <td>79.0</td>
      <td>100.0</td>
    </tr>
    <tr>
      <th>22</th>
      <td>2015 3/4</td>
      <td>87.7</td>
      <td>95.7</td>
      <td>83.3</td>
      <td>92.4</td>
      <td>90.8</td>
      <td>100.0</td>
      <td>97.9</td>
      <td>87.1</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>58.8</td>
      <td>49.3</td>
      <td>76.6</td>
      <td>83.7</td>
      <td>79.6</td>
      <td>92.4</td>
      <td>79.7</td>
      <td>100.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
print(pre_sale_rate.dtypes)
```

    시점     object
    소계    float64
    서울    float64
    인천    float64
    경기    float64
    부산    float64
    대구    float64
    광주    float64
    대전    float64
    울산    float64
    세종    float64
    강원    float64
    충북    float64
    충남    float64
    전북    float64
    전남    float64
    경북    float64
    경남    float64
    제주    float64
    dtype: object


## 잘못하는 흔한 실수
### pre_sale_rate['시점'] = pre_sale_rate['시점'].astype(datetime)
### pre_sale_rate['시점']  = pre_sale_rate['시점'].to_datetime()


```python
# convert the 'Date' column to datetime format
pre_sale_rate['시점'] = pd.to_datetime(pre_sale_rate['시점'])
print(pre_sale_rate.dtypes)
```

    시점    datetime64[ns]
    소계           float64
    서울           float64
    인천           float64
    경기           float64
    부산           float64
    대구           float64
    광주           float64
    대전           float64
    울산           float64
    세종           float64
    강원           float64
    충북           float64
    충남           float64
    전북           float64
    전남           float64
    경북           float64
    경남           float64
    제주           float64
    dtype: object



```python
# 일부로 다운로드를 내림차순으로 받았습니다. 오름차순으로 시계열로 정리해보겠습니다.

pre_sale_rate.sort_values(by=['시점'], ascending=True, inplace=True)
pre_sale_rate
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
      <th>시점</th>
      <th>소계</th>
      <th>서울</th>
      <th>인천</th>
      <th>경기</th>
      <th>부산</th>
      <th>대구</th>
      <th>광주</th>
      <th>대전</th>
      <th>울산</th>
      <th>세종</th>
      <th>강원</th>
      <th>충북</th>
      <th>충남</th>
      <th>전북</th>
      <th>전남</th>
      <th>경북</th>
      <th>경남</th>
      <th>제주</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>22</th>
      <td>2015-03-04</td>
      <td>87.7</td>
      <td>95.7</td>
      <td>83.3</td>
      <td>92.4</td>
      <td>90.8</td>
      <td>100.0</td>
      <td>97.9</td>
      <td>87.1</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>58.8</td>
      <td>49.3</td>
      <td>76.6</td>
      <td>83.7</td>
      <td>79.6</td>
      <td>92.4</td>
      <td>79.7</td>
      <td>100.0</td>
    </tr>
    <tr>
      <th>21</th>
      <td>2015-04-04</td>
      <td>87.1</td>
      <td>98.8</td>
      <td>84.4</td>
      <td>86.4</td>
      <td>97.1</td>
      <td>92.8</td>
      <td>92.4</td>
      <td>96.8</td>
      <td>100.0</td>
      <td>96.9</td>
      <td>100.0</td>
      <td>89.3</td>
      <td>70.1</td>
      <td>78.7</td>
      <td>77.0</td>
      <td>90.0</td>
      <td>79.0</td>
      <td>100.0</td>
    </tr>
    <tr>
      <th>20</th>
      <td>2016-01-04</td>
      <td>78.6</td>
      <td>95.7</td>
      <td>89.4</td>
      <td>76.1</td>
      <td>88.5</td>
      <td>46.1</td>
      <td>46.3</td>
      <td>96.8</td>
      <td>89.0</td>
      <td>99.7</td>
      <td>92.4</td>
      <td>60.2</td>
      <td>55.5</td>
      <td>96.6</td>
      <td>91.1</td>
      <td>78.5</td>
      <td>75.4</td>
      <td>89.2</td>
    </tr>
    <tr>
      <th>19</th>
      <td>2016-02-04</td>
      <td>70.5</td>
      <td>99.9</td>
      <td>74.2</td>
      <td>67.8</td>
      <td>76.9</td>
      <td>93.5</td>
      <td>67.7</td>
      <td>91.4</td>
      <td>73.9</td>
      <td>99.0</td>
      <td>91.1</td>
      <td>69.7</td>
      <td>44.3</td>
      <td>82.5</td>
      <td>83.3</td>
      <td>47.7</td>
      <td>68.1</td>
      <td>89.2</td>
    </tr>
    <tr>
      <th>18</th>
      <td>2016-03-04</td>
      <td>72.0</td>
      <td>100.0</td>
      <td>64.4</td>
      <td>76.8</td>
      <td>90.7</td>
      <td>100.0</td>
      <td>83.9</td>
      <td>91.4</td>
      <td>73.9</td>
      <td>99.0</td>
      <td>78.5</td>
      <td>57.8</td>
      <td>31.1</td>
      <td>55.8</td>
      <td>73.5</td>
      <td>33.7</td>
      <td>57.0</td>
      <td>89.2</td>
    </tr>
    <tr>
      <th>17</th>
      <td>2016-04-04</td>
      <td>85.8</td>
      <td>96.4</td>
      <td>96.6</td>
      <td>89.3</td>
      <td>100.0</td>
      <td>89.6</td>
      <td>98.8</td>
      <td>98.2</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>89.5</td>
      <td>24.4</td>
      <td>35.1</td>
      <td>82.6</td>
      <td>93.8</td>
      <td>37.4</td>
      <td>95.2</td>
      <td>72.1</td>
    </tr>
    <tr>
      <th>16</th>
      <td>2017-01-04</td>
      <td>88.2</td>
      <td>99.9</td>
      <td>71.2</td>
      <td>86.9</td>
      <td>95.9</td>
      <td>100.0</td>
      <td>87.1</td>
      <td>98.2</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>85.0</td>
      <td>91.2</td>
      <td>21.4</td>
      <td>99.6</td>
      <td>93.2</td>
      <td>59.7</td>
      <td>86.6</td>
      <td>82.5</td>
    </tr>
    <tr>
      <th>15</th>
      <td>2017-02-04</td>
      <td>74.9</td>
      <td>99.7</td>
      <td>60.1</td>
      <td>67.5</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>88.5</td>
      <td>100.0</td>
      <td>93.1</td>
      <td>63.9</td>
      <td>40.8</td>
      <td>67.4</td>
      <td>71.9</td>
      <td>37.0</td>
      <td>82.2</td>
      <td>34.5</td>
    </tr>
    <tr>
      <th>14</th>
      <td>2017-03-04</td>
      <td>87.3</td>
      <td>100.0</td>
      <td>74.6</td>
      <td>99.1</td>
      <td>93.7</td>
      <td>100.0</td>
      <td>87.4</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>81.6</td>
      <td>73.1</td>
      <td>94.0</td>
      <td>75.0</td>
      <td>80.5</td>
      <td>37.0</td>
      <td>82.2</td>
      <td>48.5</td>
    </tr>
    <tr>
      <th>13</th>
      <td>2017-04-04</td>
      <td>81.2</td>
      <td>99.2</td>
      <td>100.0</td>
      <td>91.9</td>
      <td>79.8</td>
      <td>100.0</td>
      <td>99.4</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>81.0</td>
      <td>42.7</td>
      <td>33.0</td>
      <td>95.7</td>
      <td>14.3</td>
      <td>48.9</td>
      <td>50.2</td>
      <td>64.7</td>
    </tr>
    <tr>
      <th>12</th>
      <td>2018-01-04</td>
      <td>86.5</td>
      <td>100.0</td>
      <td>65.9</td>
      <td>87.6</td>
      <td>89.8</td>
      <td>95.5</td>
      <td>99.6</td>
      <td>100.0</td>
      <td>62.1</td>
      <td>99.2</td>
      <td>80.6</td>
      <td>70.2</td>
      <td>33.1</td>
      <td>91.9</td>
      <td>84.3</td>
      <td>49.5</td>
      <td>75.8</td>
      <td>62.4</td>
    </tr>
    <tr>
      <th>11</th>
      <td>2018-02-04</td>
      <td>81.5</td>
      <td>99.7</td>
      <td>65.9</td>
      <td>87.3</td>
      <td>68.8</td>
      <td>100.0</td>
      <td>82.2</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>99.2</td>
      <td>49.4</td>
      <td>52.7</td>
      <td>12.0</td>
      <td>90.8</td>
      <td>85.5</td>
      <td>83.8</td>
      <td>20.0</td>
      <td>62.4</td>
    </tr>
    <tr>
      <th>10</th>
      <td>2018-03-04</td>
      <td>84.1</td>
      <td>99.6</td>
      <td>95.3</td>
      <td>94.7</td>
      <td>59.7</td>
      <td>94.5</td>
      <td>99.1</td>
      <td>100.0</td>
      <td>76.1</td>
      <td>100.0</td>
      <td>92.9</td>
      <td>52.7</td>
      <td>45.6</td>
      <td>75.7</td>
      <td>89.8</td>
      <td>46.7</td>
      <td>23.3</td>
      <td>41.3</td>
    </tr>
    <tr>
      <th>9</th>
      <td>2018-04-04</td>
      <td>85.6</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>95.3</td>
      <td>95.5</td>
      <td>97.2</td>
      <td>99.1</td>
      <td>94.5</td>
      <td>76.1</td>
      <td>100.0</td>
      <td>41.5</td>
      <td>93.3</td>
      <td>52.7</td>
      <td>89.4</td>
      <td>78.5</td>
      <td>56.1</td>
      <td>33.3</td>
      <td>41.3</td>
    </tr>
    <tr>
      <th>8</th>
      <td>2019-01-04</td>
      <td>81.7</td>
      <td>97.4</td>
      <td>95.1</td>
      <td>78.7</td>
      <td>71.6</td>
      <td>99.9</td>
      <td>99.9</td>
      <td>94.5</td>
      <td>76.1</td>
      <td>100.0</td>
      <td>53.9</td>
      <td>37.0</td>
      <td>95.2</td>
      <td>78.4</td>
      <td>78.5</td>
      <td>65.9</td>
      <td>13.9</td>
      <td>41.3</td>
    </tr>
    <tr>
      <th>7</th>
      <td>2019-02-04</td>
      <td>82.8</td>
      <td>91.3</td>
      <td>88.8</td>
      <td>82.7</td>
      <td>82.8</td>
      <td>81.5</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>76.1</td>
      <td>100.0</td>
      <td>79.3</td>
      <td>80.3</td>
      <td>95.2</td>
      <td>87.1</td>
      <td>70.5</td>
      <td>67.9</td>
      <td>23.8</td>
      <td>32.3</td>
    </tr>
    <tr>
      <th>6</th>
      <td>2019-03-04</td>
      <td>84.0</td>
      <td>99.6</td>
      <td>99.3</td>
      <td>89.2</td>
      <td>82.8</td>
      <td>86.6</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>76.1</td>
      <td>100.0</td>
      <td>31.5</td>
      <td>80.3</td>
      <td>63.2</td>
      <td>87.1</td>
      <td>87.0</td>
      <td>100.0</td>
      <td>53.3</td>
      <td>32.3</td>
    </tr>
    <tr>
      <th>5</th>
      <td>2019-04-04</td>
      <td>91.7</td>
      <td>99.6</td>
      <td>99.7</td>
      <td>92.7</td>
      <td>100.0</td>
      <td>93.4</td>
      <td>94.4</td>
      <td>100.0</td>
      <td>76.1</td>
      <td>100.0</td>
      <td>44.1</td>
      <td>76.5</td>
      <td>80.8</td>
      <td>80.5</td>
      <td>90.8</td>
      <td>17.8</td>
      <td>98.4</td>
      <td>46.8</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2020-01-04</td>
      <td>92.4</td>
      <td>100.0</td>
      <td>98.7</td>
      <td>99.6</td>
      <td>99.8</td>
      <td>86.5</td>
      <td>99.3</td>
      <td>100.0</td>
      <td>76.1</td>
      <td>100.0</td>
      <td>76.0</td>
      <td>76.5</td>
      <td>77.7</td>
      <td>94.6</td>
      <td>50.0</td>
      <td>73.7</td>
      <td>98.4</td>
      <td>22.2</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2020-02-04</td>
      <td>97.0</td>
      <td>100.0</td>
      <td>99.9</td>
      <td>100.0</td>
      <td>92.5</td>
      <td>100.0</td>
      <td>99.4</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>39.3</td>
      <td>99.1</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>93.1</td>
      <td>73.7</td>
      <td>69.9</td>
      <td>22.2</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2020-03-04</td>
      <td>96.4</td>
      <td>100.0</td>
      <td>97.6</td>
      <td>100.0</td>
      <td>94.5</td>
      <td>99.9</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>93.1</td>
      <td>99.1</td>
      <td>63.3</td>
      <td>100.0</td>
      <td>99.2</td>
      <td>73.7</td>
      <td>69.9</td>
      <td>10.3</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2020-04-04</td>
      <td>96.6</td>
      <td>100.0</td>
      <td>94.3</td>
      <td>98.3</td>
      <td>100.0</td>
      <td>99.8</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>88.7</td>
      <td>100.0</td>
      <td>76.4</td>
      <td>99.1</td>
      <td>98.8</td>
      <td>82.9</td>
      <td>74.8</td>
      <td>73.7</td>
      <td>99.8</td>
      <td>1.3</td>
    </tr>
    <tr>
      <th>0</th>
      <td>2021-01-04</td>
      <td>94.8</td>
      <td>100.0</td>
      <td>94.3</td>
      <td>96.2</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>100.0</td>
      <td>88.7</td>
      <td>100.0</td>
      <td>41.2</td>
      <td>75.0</td>
      <td>100.0</td>
      <td>99.9</td>
      <td>79.0</td>
      <td>98.9</td>
      <td>91.0</td>
      <td>1.3</td>
    </tr>
  </tbody>
</table>
</div>




```python
pre_sale_rate.drop('소계', axis=1, inplace=True)
```


```python
pre_sale_rate.columns
```




    Index(['시점', '서울', '인천', '경기', '부산', '대구', '광주', '대전', '울산', '세종', '강원', '충북',
           '충남', '전북', '전남', '경북', '경남', '제주'],
          dtype='object')



## 흔히하는 실수 pre_sale_rate.astype('float')
### pre_sale_rate['시점'] = pd.to_numeric(pre_sale_rate['시점'])
### pre_sale_rate['시점'].dtypes

## '시점' column을 실수형으로 변경
### re_sale_rate['시점'] = pre_sale_rate['시점'].values.astype(float)
### pre_sale_rate['시점'].dtypes


```python
plt.rc('figure', figsize=(20, 5))
pre_sale_rate.plot('시점',['서울','세종','제주'])
```




    <AxesSubplot:xlabel='시점'>




    
![png](output_16_1.png)
    



```python
# 결측치 보기
import missingno as msno
msno.matrix(pre_sale_rate, figsize=(18,6))
```




    <AxesSubplot:>




    
![png](output_17_1.png)
    

