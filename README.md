

```python
# This is the main file for our team project
# the purpose of this project is to analyze the big data files and answer few questions regarding:
#1. The relationship of the houe price and the weather in the US
#2. The relationship of the houe price and the House Income in the US
#3. The relationship of the houe price and the population in the US
```


```python
# All dependency
import os
import pandas as pd
import numpy as np
import json
import matplotlib.pyplot as plt
import requests as req
import random
from uszipcode import ZipcodeSearchEngine

```


```python
#Read the date files
csv_path = os.path.join('..', 'data', 'Zip_Zhvi_2bedroom.csv')
df_2bed = pd.read_csv(csv_path)
csv_path2 = os.path.join('..', 'data', 'Zip_Zhvi_3bedroom.csv')
df_3bed = pd.read_csv(csv_path2)
csv_path3 = os.path.join('..', 'data', 'census_data.csv')
census_df = pd.read_csv(csv_path3)
census_df["Zipcode"] = census_df["Zipcode"].astype(str).str.zfill(5)
census_df.head()
len(census_df)
```




    33120




```python
#Extract unique zipcodes 
unique_zip=census_df["Zipcode"].unique()
len(unique_zip)
```




    33120




```python
df_2a = df_2bed[["RegionName", 'City', 'State'
, 'CountyName'
,'2017-11']]
df2 = df_2a[np.isfinite(df_2a['2017-11'])]
df2['2017-11'] = df2['2017-11'].apply(np.int32)
df2 = df2.rename(columns={'RegionName': 'Zipcode', '2017-11' : 'Price'})
df_3a = df_3bed[["RegionName", 'City', 'State'
, 'CountyName'
,'2017-11']]
df3 = df_3a[np.isfinite(df_3a['2017-11'])]
df3['2017-11'] = df3['2017-11'].apply(np.int32)
df3 = df3.rename(columns={'RegionName': 'Zipcode', '2017-11' : 'Price'})
df2['Zipcode'] = df2['Zipcode'].astype(str).str.zfill(5)
df3['Zipcode'] = df3['Zipcode'].astype(str).str.zfill(5)
```

    C:\Users\Beheshteh\Anaconda3\envs\phytonData\lib\site-packages\ipykernel_launcher.py:5: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/indexing.html#indexing-view-versus-copy
      """
    


```python
df2.head()
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
      <th>Zipcode</th>
      <th>City</th>
      <th>State</th>
      <th>CountyName</th>
      <th>Price</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>10025</td>
      <td>New York</td>
      <td>NY</td>
      <td>New York</td>
      <td>1469200</td>
    </tr>
    <tr>
      <th>1</th>
      <td>60657</td>
      <td>Chicago</td>
      <td>IL</td>
      <td>Cook</td>
      <td>378200</td>
    </tr>
    <tr>
      <th>2</th>
      <td>10023</td>
      <td>New York</td>
      <td>NY</td>
      <td>New York</td>
      <td>1968600</td>
    </tr>
    <tr>
      <th>3</th>
      <td>60614</td>
      <td>Chicago</td>
      <td>IL</td>
      <td>Cook</td>
      <td>405900</td>
    </tr>
    <tr>
      <th>4</th>
      <td>79936</td>
      <td>El Paso</td>
      <td>TX</td>
      <td>El Paso</td>
      <td>84300</td>
    </tr>
  </tbody>
</table>
</div>




```python
inner_merge_df = pd.merge(df2, census_df, on="Zipcode")
inner_merge_df2 = pd.merge(df3, census_df, on="Zipcode")
```


```python
inner_merge_df.head()
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
      <th>Zipcode</th>
      <th>City</th>
      <th>State</th>
      <th>CountyName</th>
      <th>Price</th>
      <th>Population</th>
      <th>Median Age</th>
      <th>Household Income</th>
      <th>Per Capita Income</th>
      <th>Poverty Count</th>
      <th>Poverty Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>10025</td>
      <td>New York</td>
      <td>NY</td>
      <td>New York</td>
      <td>1469200</td>
      <td>94717</td>
      <td>40.6</td>
      <td>80,025</td>
      <td>63711.0</td>
      <td>14515</td>
      <td>15.324599</td>
    </tr>
    <tr>
      <th>1</th>
      <td>60657</td>
      <td>Chicago</td>
      <td>IL</td>
      <td>Cook</td>
      <td>378200</td>
      <td>70105</td>
      <td>30.9</td>
      <td>84,319</td>
      <td>67680.0</td>
      <td>6455</td>
      <td>9.207617</td>
    </tr>
    <tr>
      <th>2</th>
      <td>10023</td>
      <td>New York</td>
      <td>NY</td>
      <td>New York</td>
      <td>1968600</td>
      <td>60307</td>
      <td>42.5</td>
      <td>115,080</td>
      <td>111473.0</td>
      <td>4419</td>
      <td>7.327508</td>
    </tr>
    <tr>
      <th>3</th>
      <td>60614</td>
      <td>Chicago</td>
      <td>IL</td>
      <td>Cook</td>
      <td>405900</td>
      <td>69817</td>
      <td>30.1</td>
      <td>94,640</td>
      <td>71659.0</td>
      <td>6822</td>
      <td>9.771259</td>
    </tr>
    <tr>
      <th>4</th>
      <td>79936</td>
      <td>El Paso</td>
      <td>TX</td>
      <td>El Paso</td>
      <td>84300</td>
      <td>113255</td>
      <td>31.2</td>
      <td>52,422</td>
      <td>22053.0</td>
      <td>15663</td>
      <td>13.829853</td>
    </tr>
  </tbody>
</table>
</div>




```python
inner_merge_df2.head()
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
      <th>Zipcode</th>
      <th>City</th>
      <th>State</th>
      <th>CountyName</th>
      <th>Price</th>
      <th>Population</th>
      <th>Median Age</th>
      <th>Household Income</th>
      <th>Per Capita Income</th>
      <th>Poverty Count</th>
      <th>Poverty Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>60657</td>
      <td>Chicago</td>
      <td>IL</td>
      <td>Cook</td>
      <td>567300</td>
      <td>70105</td>
      <td>30.9</td>
      <td>84,319</td>
      <td>67680.0</td>
      <td>6455</td>
      <td>9.207617</td>
    </tr>
    <tr>
      <th>1</th>
      <td>60614</td>
      <td>Chicago</td>
      <td>IL</td>
      <td>Cook</td>
      <td>628700</td>
      <td>69817</td>
      <td>30.1</td>
      <td>94,640</td>
      <td>71659.0</td>
      <td>6822</td>
      <td>9.771259</td>
    </tr>
    <tr>
      <th>2</th>
      <td>79936</td>
      <td>El Paso</td>
      <td>TX</td>
      <td>El Paso</td>
      <td>111700</td>
      <td>113255</td>
      <td>31.2</td>
      <td>52,422</td>
      <td>22053.0</td>
      <td>15663</td>
      <td>13.829853</td>
    </tr>
    <tr>
      <th>3</th>
      <td>60640</td>
      <td>Chicago</td>
      <td>IL</td>
      <td>Cook</td>
      <td>375200</td>
      <td>67088</td>
      <td>37.5</td>
      <td>47,390</td>
      <td>38673.0</td>
      <td>14206</td>
      <td>21.175173</td>
    </tr>
    <tr>
      <th>4</th>
      <td>77084</td>
      <td>Houston</td>
      <td>TX</td>
      <td>Harris</td>
      <td>151400</td>
      <td>103635</td>
      <td>32.6</td>
      <td>66,914</td>
      <td>27973.0</td>
      <td>11839</td>
      <td>11.423747</td>
    </tr>
  </tbody>
</table>
</div>




```python
latlnglist = []

for zip in inner_merge_df['Zipcode']:
    search = ZipcodeSearchEngine()
    zipcode = search.by_zipcode(zip)
    lat = zipcode['Latitude']
    lng = zipcode['Longitude']
    zipcoord = {"Zipcode": zip, "Lat":lat, "Lng": lng}
    latlnglist.append(zipcoord)
```


```python
latlnglist2 = []

for zip in inner_merge_df2['Zipcode']:
    search = ZipcodeSearchEngine()
    zipcode = search.by_zipcode(zip)
    lat = zipcode['Latitude']
    lng = zipcode['Longitude']
    zipcoord = {"Zipcode": zip, "Lat":lat, "Lng": lng}
    latlnglist2.append(zipcoord)
```


```python
hdf = pd.DataFrame(latlnglist)
hdf.head()
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
      <th>Lat</th>
      <th>Lng</th>
      <th>Zipcode</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>40.799921</td>
      <td>-73.968310</td>
      <td>10025</td>
    </tr>
    <tr>
      <th>1</th>
      <td>41.940379</td>
      <td>-87.653180</td>
      <td>60657</td>
    </tr>
    <tr>
      <th>2</th>
      <td>40.776906</td>
      <td>-73.980064</td>
      <td>10023</td>
    </tr>
    <tr>
      <th>3</th>
      <td>41.920347</td>
      <td>-87.643314</td>
      <td>60614</td>
    </tr>
    <tr>
      <th>4</th>
      <td>31.759979</td>
      <td>-106.287521</td>
      <td>79936</td>
    </tr>
  </tbody>
</table>
</div>




```python
hdf2 = pd.DataFrame(latlnglist2)
hdf2.head()
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
      <th>Lat</th>
      <th>Lng</th>
      <th>Zipcode</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>41.940379</td>
      <td>-87.653180</td>
      <td>60657</td>
    </tr>
    <tr>
      <th>1</th>
      <td>41.920347</td>
      <td>-87.643314</td>
      <td>60614</td>
    </tr>
    <tr>
      <th>2</th>
      <td>31.759979</td>
      <td>-106.287521</td>
      <td>79936</td>
    </tr>
    <tr>
      <th>3</th>
      <td>41.970313</td>
      <td>-87.663045</td>
      <td>60640</td>
    </tr>
    <tr>
      <th>4</th>
      <td>29.829651</td>
      <td>-95.666831</td>
      <td>77084</td>
    </tr>
  </tbody>
</table>
</div>




```python
latlngmerge = pd.merge(inner_merge_df, hdf, on="Zipcode")
latlngmerge.head()
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
      <th>Zipcode</th>
      <th>City</th>
      <th>State</th>
      <th>CountyName</th>
      <th>Price</th>
      <th>Population</th>
      <th>Median Age</th>
      <th>Household Income</th>
      <th>Per Capita Income</th>
      <th>Poverty Count</th>
      <th>Poverty Rate</th>
      <th>Lat</th>
      <th>Lng</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>10025</td>
      <td>New York</td>
      <td>NY</td>
      <td>New York</td>
      <td>1469200</td>
      <td>94717</td>
      <td>40.6</td>
      <td>80,025</td>
      <td>63711.0</td>
      <td>14515</td>
      <td>15.324599</td>
      <td>40.799921</td>
      <td>-73.968310</td>
    </tr>
    <tr>
      <th>1</th>
      <td>60657</td>
      <td>Chicago</td>
      <td>IL</td>
      <td>Cook</td>
      <td>378200</td>
      <td>70105</td>
      <td>30.9</td>
      <td>84,319</td>
      <td>67680.0</td>
      <td>6455</td>
      <td>9.207617</td>
      <td>41.940379</td>
      <td>-87.653180</td>
    </tr>
    <tr>
      <th>2</th>
      <td>10023</td>
      <td>New York</td>
      <td>NY</td>
      <td>New York</td>
      <td>1968600</td>
      <td>60307</td>
      <td>42.5</td>
      <td>115,080</td>
      <td>111473.0</td>
      <td>4419</td>
      <td>7.327508</td>
      <td>40.776906</td>
      <td>-73.980064</td>
    </tr>
    <tr>
      <th>3</th>
      <td>60614</td>
      <td>Chicago</td>
      <td>IL</td>
      <td>Cook</td>
      <td>405900</td>
      <td>69817</td>
      <td>30.1</td>
      <td>94,640</td>
      <td>71659.0</td>
      <td>6822</td>
      <td>9.771259</td>
      <td>41.920347</td>
      <td>-87.643314</td>
    </tr>
    <tr>
      <th>4</th>
      <td>79936</td>
      <td>El Paso</td>
      <td>TX</td>
      <td>El Paso</td>
      <td>84300</td>
      <td>113255</td>
      <td>31.2</td>
      <td>52,422</td>
      <td>22053.0</td>
      <td>15663</td>
      <td>13.829853</td>
      <td>31.759979</td>
      <td>-106.287521</td>
    </tr>
  </tbody>
</table>
</div>




```python
latlngmerge2 = pd.merge(inner_merge_df2, hdf2, on="Zipcode")
latlngmerge2.head()
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
      <th>Zipcode</th>
      <th>City</th>
      <th>State</th>
      <th>CountyName</th>
      <th>Price</th>
      <th>Population</th>
      <th>Median Age</th>
      <th>Household Income</th>
      <th>Per Capita Income</th>
      <th>Poverty Count</th>
      <th>Poverty Rate</th>
      <th>Lat</th>
      <th>Lng</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>60657</td>
      <td>Chicago</td>
      <td>IL</td>
      <td>Cook</td>
      <td>567300</td>
      <td>70105</td>
      <td>30.9</td>
      <td>84,319</td>
      <td>67680.0</td>
      <td>6455</td>
      <td>9.207617</td>
      <td>41.940379</td>
      <td>-87.653180</td>
    </tr>
    <tr>
      <th>1</th>
      <td>60614</td>
      <td>Chicago</td>
      <td>IL</td>
      <td>Cook</td>
      <td>628700</td>
      <td>69817</td>
      <td>30.1</td>
      <td>94,640</td>
      <td>71659.0</td>
      <td>6822</td>
      <td>9.771259</td>
      <td>41.920347</td>
      <td>-87.643314</td>
    </tr>
    <tr>
      <th>2</th>
      <td>79936</td>
      <td>El Paso</td>
      <td>TX</td>
      <td>El Paso</td>
      <td>111700</td>
      <td>113255</td>
      <td>31.2</td>
      <td>52,422</td>
      <td>22053.0</td>
      <td>15663</td>
      <td>13.829853</td>
      <td>31.759979</td>
      <td>-106.287521</td>
    </tr>
    <tr>
      <th>3</th>
      <td>60640</td>
      <td>Chicago</td>
      <td>IL</td>
      <td>Cook</td>
      <td>375200</td>
      <td>67088</td>
      <td>37.5</td>
      <td>47,390</td>
      <td>38673.0</td>
      <td>14206</td>
      <td>21.175173</td>
      <td>41.970313</td>
      <td>-87.663045</td>
    </tr>
    <tr>
      <th>4</th>
      <td>77084</td>
      <td>Houston</td>
      <td>TX</td>
      <td>Harris</td>
      <td>151400</td>
      <td>103635</td>
      <td>32.6</td>
      <td>66,914</td>
      <td>27973.0</td>
      <td>11839</td>
      <td>11.423747</td>
      <td>29.829651</td>
      <td>-95.666831</td>
    </tr>
  </tbody>
</table>
</div>




```python
latlngmerge = latlngmerge.rename(columns = {'Per Capita Income' : 'Income'})
latlngmerge2 = latlngmerge2.rename(columns = {'Per Capita Income' : 'Income'})

```


```python
path_d ='C:\\Users\\Beheshteh\\Documents'
latlngmerge.to_csv(os.path.join(path_d, 'twobed.csv'), index = False)
latlngmerge2.to_csv(os.path.join(path_d, 'threebed.csv'), index = False)

```


```python
# In this section , We need to defind all the function needed to find the effect of weather on price
# of the house.


# Build partial query URL

url = "http://api.openweathermap.org/data/2.5/weather?"
units = "metric"
api_key =  #get_file_contents(filename)
query_url = url + "appid=" + api_key + "&units=" + units + "&zip=" 

# Get 3000 random zipcode from the dataframe and get the avarage price for all with the same zipcode
def get_random3000(df):
    random3000_zipcodes = []
    random3000_price = []
    Zipcodes = df["Zipcode"].unique() 
    random3000_zipcodes = [ Zipcodes[i] for i in sorted(random.sample(range(len(Zipcodes)), 3000)) ]
 
    for code in random3000_zipcodes:
        
        prices = df[df.Zipcode == code ]['Price']
        avarage_price = float(sum(p for p in prices)) / len(prices)
        random3000_price.append(avarage_price)
        next
        
    return(random3000_zipcodes, random3000_price )
    

# this function for Loop through the list of Zipcodes and perform a request 
# for tempture on the specified zipcodes and return the time of query, lattitude,
# longttitude and tempature

def get_tempture_for_zipcode(Zipcodes):
    lat_list = []
    lon_list = []
    temp_list = []
    #weather_data = []
    errordatacount = 0
    zipcount = 0
    
    for zipcode in Zipcodes:
        
        response = req.get(query_url + str(zipcode) ).json()
        try:
            lat_list.append(response["coord"]["lat"])
            lon_list.append(response["coord"]["lon"])
            temp_list.append(response["main"]["temp"])
            #time_data = response["dt"]
            zipcount +=1 
            #print(zipcount)
        except:
            print("%s Error with Zipcode data. Skipping", errordatacount)
            errordatacount +=1
            
    return(lat_list, lon_list, temp_list) 

```


```python
# Call the file for getting the tempture for three bedrooms house

three_lat_list = []
three_lon_list = []
three_temp_list = []
random3_zipcodes = []
random3_price = []


#random3_zipcodes, random3_price = get_random3000(df3)
#three_lat_list, three_lon_list, three_temp_list = get_tempture_for_zipcode(random3_zipcodes)

```


```python
#Zipcodeweather3_data = { "ZipCode": random3_zipcodes, "Lng": three_lon_list,
                        # "Lat": three_lat_list, "Tempature (C)": three_temp_list,  
                        # "Price": random3_price}

#Zipcodeweather3_data = pd.DataFrame(Zipcodeweather3_data)

#write all the info in the csv file
#Zipcodeweather3_data.to_csv('Zipcodeweather_3bed_file.csv', index=True, header=True)
csv_path = os.path.join('..', 'data', 'Zipcodeweather3file.csv')
Zipcodeweather3_data = pd.read_csv(csv_path)       
Zipcodeweather3_data.head()

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
      <th>Unnamed: 0</th>
      <th>Lat</th>
      <th>Lng</th>
      <th>Price</th>
      <th>Tempature (C)</th>
      <th>ZipCode</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>41.92</td>
      <td>-87.65</td>
      <td>628700.0</td>
      <td>2.50</td>
      <td>60614</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>37.79</td>
      <td>-122.42</td>
      <td>1928200.0</td>
      <td>11.23</td>
      <td>94109</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>28.93</td>
      <td>-81.96</td>
      <td>235100.0</td>
      <td>13.77</td>
      <td>32162</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>41.95</td>
      <td>-87.70</td>
      <td>455800.0</td>
      <td>2.44</td>
      <td>60618</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>41.95</td>
      <td>-87.66</td>
      <td>472700.0</td>
      <td>2.50</td>
      <td>60613</td>
    </tr>
  </tbody>
</table>
</div>




```python
bins = [-10, 10, 30, 55, 75]
def do_bin_tempature(weather_data):
    group_names = ['freezing', 'cold', 'warm', 'hot']
    pd.cut(weather_data["Tempature (C)"], bins, labels=group_names)
    weather_data["Tempature (C)"] = pd.cut(weather_data["Tempature (C)"], bins, labels=group_names)
    return(weather_data)
```


```python
def Create_plot(weather_data):
    
    #Create the bin tempature based on ['freezing, -32_-10', 'cold, -10_45', 'warm, 45_75', 'hot, 75_100']
    do_bin_tempature(weather_data)
    
    #Based on the tempature 
    Freezingtemp = weather_data[weather_data["Tempature (C)"] == "freezing"]
    Coldtemp = weather_data[weather_data["Tempature (C)"] == "cold"]
    Warmtemp = weather_data[weather_data["Tempature (C)"] == "warm"]
    Hottemp = weather_data[weather_data["Tempature (C)"] == "hot"]

    Freezinglat = Freezingtemp.groupby(["ZipCode"]).max()["Lat"]
    Freezinglng = Freezingtemp.groupby(["ZipCode"]).max()["Lng"]
    Freezingprice = Freezingtemp.groupby(["ZipCode"]).mean()["Price"]

    coldlat = Coldtemp.groupby(["ZipCode"]).max()["Lat"]
    coldlng = Coldtemp.groupby(["ZipCode"]).max()["Lng"]
    coldprice = Coldtemp.groupby(["ZipCode"]).mean()["Price"]

    warmlat = Warmtemp.groupby(["ZipCode"]).max()["Lat"]
    warmlng = Warmtemp.groupby(["ZipCode"]).max()["Lng"]
    warmprice = Warmtemp.groupby(["ZipCode"]).mean()["Price"]

    hotlat = Hottemp.groupby(["ZipCode"]).max()["Lat"]
    hotlng = Hottemp.groupby(["ZipCode"]).max()["Lng"]
    hotprice = Hottemp.groupby(["ZipCode"]).mean()["Price"]
    
    
    
    plt.figure(figsize = (20,20))
    plt.scatter(Freezinglng, 
                Freezinglat, 
                s=Freezingprice / 10000, c="yellow", 
                edgecolor="black", linewidths=1, marker="o", 
                alpha=0.8, label="Freezing Temp")

    plt.scatter(coldlng, 
                coldlat, 
                s=coldprice / 10000, c="skyblue", 
                edgecolor="black", linewidths=1, marker="o", 
                alpha=0.8, label="cold Temp")

    plt.scatter(warmlng, 
                warmlat, 
                s=warmprice / 10000, c="orange", 
                edgecolor="black", linewidths=1, marker="o", 
                alpha=0.8, label="warm Temp")

    plt.scatter(hotlng, 
                hotlat, 
                s=hotprice / 10000, c="red", 
                edgecolor="black", linewidths=1, marker="o", 
                alpha=0.8, label="Hot Temp")

    plt.title("Effect of Tempture on house price in the US ")
    plt.ylabel("Lattitude")
    plt.xlabel("Longitude")
    plt.grid(True)

# Create a legend
    lgnd = plt.legend(fontsize="small", mode="Expanded", 
                      numpoints=1, scatterpoints=1, 
                      loc="best", title="Weather Type", 
                      labelspacing=0.5)
    lgnd.legendHandles[0]._sizes = [30]
    lgnd.legendHandles[1]._sizes = [30]
    lgnd.legendHandles[2]._sizes = [30]
    lgnd.legendHandles[3]._sizes = [30]

    plt.xlim((-130,-65))
# Incorporate a text label regarding circle size
        
    plt.savefig("output_temp_price.png")
    plt.show()
   
```


```python

Create_plot(Zipcodeweather3_data)
```


![png](combined_code_files/combined_code_22_0.png)



![png](combined_code_files/combined_code_22_1.png)



![png](combined_code_files/combined_code_22_2.png)



![png](combined_code_files/combined_code_22_3.png)



```python
# Call the file for getting the tempture for two bedrooms houses

two_lat_list = []
two_lon_list = []
two_temp_list = []
random2_zipcodes = []
random2_price = []


#random2_zipcodes, random2_price = get_random3000(df2)
#two_lat_list, two_lon_list, two_temp_list = get_tempture_for_zipcode(random2_zipcodes)

```


```python
#Zipcodeweather2_data = { "ZipCode": random2_zipcodes, "Lng": two_lon_list,
                         #"Lat": two_lat_list, "Tempature (C)": two_temp_list,  
                         #"Price": random2_price}

#Zipcodeweather2_data = pd.DataFrame(Zipcodeweather2_data)

#write all the info in the csv file
#Zipcodeweather2_data.to_csv('Zipcodeweather2file_file.csv', index=True, header=True)  
csv_path = os.path.join('..', 'data', 'Zipcodeweather2file_file.csv')
Zipcodeweather2_data = pd.read_csv(csv_path)

Zipcodeweather2_data.head()
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
      <th>Unnamed: 0</th>
      <th>Lat</th>
      <th>Lng</th>
      <th>Price</th>
      <th>Tempature (C)</th>
      <th>ZipCode</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>40.80</td>
      <td>-73.97</td>
      <td>1469200.0</td>
      <td>1.15</td>
      <td>10025</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>41.97</td>
      <td>-87.66</td>
      <td>254300.0</td>
      <td>3.12</td>
      <td>60640</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>41.92</td>
      <td>-87.70</td>
      <td>330000.0</td>
      <td>3.11</td>
      <td>60647</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>41.95</td>
      <td>-87.70</td>
      <td>275700.0</td>
      <td>3.11</td>
      <td>60618</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>29.54</td>
      <td>-95.32</td>
      <td>162400.0</td>
      <td>11.51</td>
      <td>77584</td>
    </tr>
  </tbody>
</table>
</div>




```python
Create_plot(Zipcodeweather2_data)
```


![png](combined_code_files/combined_code_25_0.png)



```python
#Weather in California
california_df = latlngmerge.loc[latlngmerge['State'] == "CA"]
california_df.head()
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
      <th>Zipcode</th>
      <th>City</th>
      <th>State</th>
      <th>CountyName</th>
      <th>Price</th>
      <th>Population</th>
      <th>Median Age</th>
      <th>Household Income</th>
      <th>Income</th>
      <th>Poverty Count</th>
      <th>Poverty Rate</th>
      <th>Lat</th>
      <th>Lng</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>6</th>
      <td>94109</td>
      <td>San Francisco</td>
      <td>CA</td>
      <td>San Francisco</td>
      <td>1303500</td>
      <td>Middle</td>
      <td>36.1</td>
      <td>71,043</td>
      <td>Middle</td>
      <td>7465</td>
      <td>13.325836</td>
      <td>37.792979</td>
      <td>-122.421242</td>
    </tr>
    <tr>
      <th>11</th>
      <td>90250</td>
      <td>Hawthorne</td>
      <td>CA</td>
      <td>Los Angeles</td>
      <td>536400</td>
      <td>High</td>
      <td>32.5</td>
      <td>47,076</td>
      <td>Low</td>
      <td>18007</td>
      <td>18.566406</td>
      <td>33.912781</td>
      <td>-118.352039</td>
    </tr>
    <tr>
      <th>18</th>
      <td>90046</td>
      <td>Los Angeles</td>
      <td>CA</td>
      <td>Los Angeles</td>
      <td>1033800</td>
      <td>Middle</td>
      <td>39.2</td>
      <td>61,157</td>
      <td>Middle</td>
      <td>7274</td>
      <td>14.284312</td>
      <td>34.114731</td>
      <td>-118.363726</td>
    </tr>
    <tr>
      <th>39</th>
      <td>94501</td>
      <td>Alameda</td>
      <td>CA</td>
      <td>Alameda</td>
      <td>813600</td>
      <td>High</td>
      <td>40.2</td>
      <td>76,787</td>
      <td>Low</td>
      <td>6896</td>
      <td>10.906560</td>
      <td>37.771217</td>
      <td>-122.282402</td>
    </tr>
    <tr>
      <th>43</th>
      <td>94110</td>
      <td>San Francisco</td>
      <td>CA</td>
      <td>San Francisco</td>
      <td>1358900</td>
      <td>High</td>
      <td>36.7</td>
      <td>97,563</td>
      <td>Middle</td>
      <td>8792</td>
      <td>11.960115</td>
      <td>37.748582</td>
      <td>-122.418411</td>
    </tr>
  </tbody>
</table>
</div>




```python
#
three_lat_list_CA = []
three_lon_list_CA = []
three_temp_list_CA = []

#three_lat_list_CA, three_lon_list_CA, three_temp_list_CA = get_tempture_for_zipcode(california_df['Zipcode'])

```


```python
#CAweather3_data = { "ZipCode": california_df['Zipcode'], "Lng": three_lon_list_CA,
                      #  "Lat": three_lat_list_CA, "Tempature (C)": three_temp_list_CA,  
                      #   "Price": california_df['Price']}

#CAweather3_data = pd.DataFrame(CAweather3_data)
#CAweather3_data.to_csv('CAweather3file.csv', index=True, header=True)

csv_path = os.path.join('..', 'data', 'CAweather3file.csv')
CAweather3_data = pd.read_csv(csv_path)

CAweather3_data.head()
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
      <th>Unnamed: 0</th>
      <th>Lat</th>
      <th>Lng</th>
      <th>Price</th>
      <th>Tempature (C)</th>
      <th>ZipCode</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>6</td>
      <td>37.79</td>
      <td>-122.42</td>
      <td>1303500</td>
      <td>12.15</td>
      <td>94109</td>
    </tr>
    <tr>
      <th>1</th>
      <td>11</td>
      <td>33.91</td>
      <td>-118.35</td>
      <td>536400</td>
      <td>15.87</td>
      <td>90250</td>
    </tr>
    <tr>
      <th>2</th>
      <td>18</td>
      <td>34.11</td>
      <td>-118.37</td>
      <td>1033800</td>
      <td>15.80</td>
      <td>90046</td>
    </tr>
    <tr>
      <th>3</th>
      <td>39</td>
      <td>37.77</td>
      <td>-122.26</td>
      <td>813600</td>
      <td>12.14</td>
      <td>94501</td>
    </tr>
    <tr>
      <th>4</th>
      <td>43</td>
      <td>37.75</td>
      <td>-122.42</td>
      <td>1358900</td>
      <td>12.08</td>
      <td>94110</td>
    </tr>
  </tbody>
</table>
</div>




```python
bins = [30, 55, 70, 85]
def do_bin_tempature_CA(weather_data):
    group_names = ['cold', 'warm', 'Vwarm']
    pd.cut(weather_data["Tempature (C)"], bins, labels=group_names)
    weather_data["Tempature (C)"] = pd.cut(weather_data["Tempature (C)"], bins, labels=group_names)
    return(weather_data)
```


```python
def Create_plot_CA(weather_data):
    
    #Create the bin tempature based on ['freezing, -32_-10', 'cold, -10_45', 'warm, 45_75', 'hot, 75_100']
    do_bin_tempature_CA(weather_data)
    
    #Based on the tempature 
    Coldtemp = weather_data[weather_data["Tempature (C)"] == "cold"]
    Warmtemp = weather_data[weather_data["Tempature (C)"] == "warm"]
    Vwarmtemp = weather_data[weather_data["Tempature (C)"] == "Vwarm"]

    coldlat = Coldtemp["Lat"]
    coldlng = Coldtemp["Lng"]
    coldprice = Coldtemp["Price"]

    warmlat = Warmtemp["Lat"]
    warmlng = Warmtemp["Lng"]
    warmprice = Warmtemp["Price"]

    Vwarmlat = Vwarmtemp["Lat"]
    Vwarmlng = Vwarmtemp["Lng"]
    Vwarmprice = Vwarmtemp["Price"]
    
        
    plt.figure(figsize = (10,10))
    plt.scatter(coldlng, 
                coldlat, 
                s=coldprice / 10000, c="skyblue", 
                edgecolor="black", linewidths=1, marker="o", 
                alpha=0.8, label="cold Temp")

    plt.scatter(warmlng, 
                warmlat, 
                s=warmprice / 10000, c="orange", 
                edgecolor="black", linewidths=1, marker="o", 
                alpha=0.8, label="warm Temp")

    plt.scatter(Vwarmlng, 
                Vwarmlat, 
                s=Vwarmprice / 10000, c="red", 
                edgecolor="black", linewidths=1, marker="o", 
                alpha=0.8, label="Very warm Temp")

    plt.title("Effect of Tempture on house price in California ")
    plt.ylabel("Lattitude")
    plt.xlabel("Longitude")
    plt.grid(True)

# Create a legend
    lgnd = plt.legend(fontsize="small", mode="Expanded", 
                      numpoints=1, scatterpoints=1, 
                      loc="best", title="Weather Type", 
                      labelspacing=0.5)
    lgnd.legendHandles[0]._sizes = [30]
    lgnd.legendHandles[1]._sizes = [30]
    lgnd.legendHandles[2]._sizes = [30]
  

    plt.xlim((-125,-115))
# Incorporate a text label regarding circle size
        
    plt.savefig("output_temp_price_california.png")
    plt.show()

```


```python
Create_plot_CA(CAweather3_data)
```


![png](combined_code_files/combined_code_31_0.png)



```python
pbins = [0, 30000, 60000, 120000]

pgroup_names = ['Low', 'Middle', 'High']
pd.cut(latlngmerge["Population"], pbins, labels=pgroup_names)
latlngmerge["Population"] = pd.cut(latlngmerge["Population"],pbins, labels=pgroup_names)
```


```python
ibins = [0, 50000, 120000, 999999]

igroup_names = ['Low', 'Middle', 'High']
pd.cut(latlngmerge["Income"], ibins, labels=igroup_names)
latlngmerge["Income"] = pd.cut(latlngmerge["Income"],ibins, labels=igroup_names)

igroup_names2 = ['Low', 'Middle', 'High']
pd.cut(latlngmerge2["Income"], ibins, labels=igroup_names)
latlngmerge2["Income"] = pd.cut(latlngmerge2["Income"],ibins, labels=igroup_names)
```


```python
Lowpop = latlngmerge[latlngmerge["Population"] == "Low"]
Mediumpop = latlngmerge[latlngmerge["Population"] == "Middle"]
Highpop = latlngmerge[latlngmerge["Population"] == "High"]

lowlat = Lowpop.groupby(["Zipcode"]).max()["Lat"]
lowlng = Lowpop.groupby(["Zipcode"]).max()["Lng"]
lowprice = Lowpop.groupby(["Zipcode"]).mean()["Price"]

Mediumlat = Mediumpop.groupby(["Zipcode"]).max()["Lat"]
Mediumlng = Mediumpop.groupby(["Zipcode"]).max()["Lng"]
Mediumprice = Mediumpop.groupby(["Zipcode"]).mean()["Price"]

Highlat = Highpop.groupby(["Zipcode"]).max()["Lat"]
Highlng = Highpop.groupby(["Zipcode"]).max()["Lng"]
Highprice = Highpop.groupby(["Zipcode"]).mean()["Price"]

```


```python
plt.figure(figsize = (20,20))
plt.scatter(lowlng, 
            lowlat, 
            s=lowprice / 10000, c="red", 
            edgecolor="black", linewidths=1, marker="o", 
            alpha=0.8, label="Low Population (Under 30k)")

plt.scatter(Mediumlng, 
            Mediumlat, 
            s=Mediumprice / 10000, c="orange", 
            edgecolor="black", linewidths=1, marker="o", 
            alpha=0.8, label="Medium Population (Between 30 and 60k)")

plt.scatter(Highlng, 
            Highlat, 
            s=Highprice / 10000, c="blue", 
            edgecolor="black", linewidths=1, marker="o", 
            alpha=0.8, label="High Population (Greater than 60k)")

plt.title("Population vs Price for 2 bedroom homes in the US")
plt.ylabel("Latitude")
plt.xlabel("Longitude")
plt.grid(True)

# Create a legend
lgnd = plt.legend(fontsize="small", mode="Expanded", 
                  numpoints=1, scatterpoints=1, 
                  loc="best", title="Population Density", 
                  labelspacing=0.5)
lgnd.legendHandles[0]._sizes = [30]
lgnd.legendHandles[1]._sizes = [30]
lgnd.legendHandles[2]._sizes = [30]

plt.xlim((-140,-60))
# Incorporate a text label regarding circle size
# plt.text(42, 35, "Note:\nCircle size correlates with driver count per city.")
plt.savefig("output_Population_price_2bedroom.png")
plt.show()
```


![png](combined_code_files/combined_code_35_0.png)



```python
pgroup_names = ['Low', 'Middle', 'High']
pd.cut(latlngmerge2["Population"], pbins, labels=pgroup_names)
latlngmerge2["Population"] = pd.cut(latlngmerge2["Population"],pbins, labels=pgroup_names)

Lowpop2 = latlngmerge2[latlngmerge2["Population"] == "Low"]
Mediumpop2 = latlngmerge2[latlngmerge2["Population"] == "Middle"]
Highpop2 = latlngmerge2[latlngmerge2["Population"] == "High"]

lowlat2 = Lowpop2.groupby(["Zipcode"]).max()["Lat"]
lowlng2 = Lowpop2.groupby(["Zipcode"]).max()["Lng"]
lowprice2 = Lowpop2.groupby(["Zipcode"]).mean()["Price"]

Mediumlat2 = Mediumpop2.groupby(["Zipcode"]).max()["Lat"]
Mediumlng2 = Mediumpop2.groupby(["Zipcode"]).max()["Lng"]
Mediumprice2 = Mediumpop2.groupby(["Zipcode"]).mean()["Price"]

Highlat2 = Highpop2.groupby(["Zipcode"]).max()["Lat"]
Highlng2 = Highpop2.groupby(["Zipcode"]).max()["Lng"]
Highprice2 = Highpop2.groupby(["Zipcode"]).mean()["Price"]

```


```python
plt.figure(figsize = (20,20))
plt.scatter(lowlng2, 
            lowlat2, 
            s=lowprice2 / 10000, c="red", 
            edgecolor="black", linewidths=1, marker="o", 
            alpha=0.8, label="Low Population (Under 30k)")

plt.scatter(Mediumlng2, 
            Mediumlat2, 
            s=Mediumprice2 / 10000, c="orange", 
            edgecolor="black", linewidths=1, marker="o", 
            alpha=0.8, label="Medium Population (Between 30k and 60k)")

plt.scatter(Highlng2, 
            Highlat2, 
            s=Highprice2 / 10000, c="blue", 
            edgecolor="black", linewidths=1, marker="o", 
            alpha=0.8, label="High Population (Greater than 60k)")

plt.title("Population vs Price for 3 bedroom homes in the US")
plt.ylabel("Latitude")
plt.xlabel("Longitude")
plt.grid(True)

# Create a legend
lgnd = plt.legend(fontsize="small", mode="Expanded", 
                  numpoints=1, scatterpoints=1, 
                  loc="best", title="Population Density", 
                  labelspacing=0.5)
lgnd.legendHandles[0]._sizes = [30]
lgnd.legendHandles[1]._sizes = [30]
lgnd.legendHandles[2]._sizes = [30]

plt.xlim((-140,-60))
# Incorporate a text label regarding circle size
# plt.text(42, 35, "Note:\nCircle size correlates with driver count per city.")
plt.savefig("output_Population_price_3bedroom.png")
plt.show()
```


![png](combined_code_files/combined_code_37_0.png)



```python
# Population in california and three bedroom
pbins = [0, 30000, 60000, 120000]

pgroup_names = ['Low', 'Middle', 'High']
pd.cut(california_df["Population"], pbins, labels=pgroup_names)
california_df["Population"] = pd.cut(california_df["Population"],pbins, labels=pgroup_names)
```


    ---------------------------------------------------------------------------

    TypeError                                 Traceback (most recent call last)

    <ipython-input-76-49973d819d46> in <module>()
          3 
          4 pgroup_names = ['Low', 'Middle', 'High']
    ----> 5 pd.cut(california_df["Population"], pbins, labels=pgroup_names)
          6 california_df["Population"] = pd.cut(california_df["Population"],pbins, labels=pgroup_names)
    

    ~\Anaconda3\envs\phytonData\lib\site-packages\pandas\core\reshape\tile.py in cut(x, bins, right, labels, retbins, precision, include_lowest)
        134                               precision=precision,
        135                               include_lowest=include_lowest,
    --> 136                               dtype=dtype)
        137 
        138     return _postprocess_for_cut(fac, bins, retbins, x_is_series,
    

    ~\Anaconda3\envs\phytonData\lib\site-packages\pandas\core\reshape\tile.py in _bins_to_cuts(x, bins, right, labels, precision, include_lowest, dtype, duplicates)
        235 
        236     side = 'left' if right else 'right'
    --> 237     ids = _ensure_int64(bins.searchsorted(x, side=side))
        238 
        239     if include_lowest:
    

    TypeError: '<' not supported between instances of 'int' and 'str'



```python
Lowpop = california_df[california_df["Population"] == "Low"]
Mediumpop = california_df[california_df["Population"] == "Middle"]
Highpop = california_df[california_df["Population"] == "High"]

lowlat = Lowpop["Lat"]
lowlng = Lowpop["Lng"]
lowprice = Lowpop["Price"]

Mediumlat = Mediumpop["Lat"]
Mediumlng = Mediumpop["Lng"]
Mediumprice = Mediumpop["Price"]

Highlat = Highpop["Lat"]
Highlng = Highpop["Lng"]
Highprice = Highpop["Price"]
```


```python
plt.figure(figsize = (10,10))
plt.scatter(lowlng, 
            lowlat, 
            s=lowprice / 10000, c="red", 
            edgecolor="black", linewidths=1, marker="o", 
            alpha=0.8, label="Low Population (Under 30k)")

plt.scatter(Mediumlng, 
            Mediumlat, 
            s=Mediumprice / 10000, c="orange", 
            edgecolor="black", linewidths=1, marker="o", 
            alpha=0.8, label="Medium Population (Between 30 and 60k)")

plt.scatter(Highlng, 
            Highlat, 
            s=Highprice / 10000, c="blue", 
            edgecolor="black", linewidths=1, marker="o", 
            alpha=0.8, label="High Population (Greater than 60k)")

plt.title("Population vs Price for 2 bedroom homes in the California")
plt.ylabel("Latitude")
plt.xlabel("Longitude")
plt.grid(True)

# Create a legend
lgnd = plt.legend(fontsize="small", mode="Expanded", 
                  numpoints=1, scatterpoints=1, 
                  loc="best", title="Population Density", 
                  labelspacing=0.5)
lgnd.legendHandles[0]._sizes = [30]
lgnd.legendHandles[1]._sizes = [30]
lgnd.legendHandles[2]._sizes = [30]

plt.xlim((-125,-115))
# Incorporate a text label regarding circle size
# plt.text(42, 35, "Note:\nCircle size correlates with driver count per city.")
plt.savefig("output_Population_price_CA.png")
plt.show()
```


![png](combined_code_files/combined_code_40_0.png)



```python
Lowi = latlngmerge[latlngmerge["Income"] == "Low"]
Mediumi = latlngmerge[latlngmerge["Income"] == "Middle"]
Highi = latlngmerge[latlngmerge["Income"] == "High"]

lowlati = Lowi.groupby(["Zipcode"]).max()["Lat"]
lowlngi = Lowi.groupby(["Zipcode"]).max()["Lng"]
lowpricei = Lowi.groupby(["Zipcode"]).mean()["Price"]

Mediumlati = Mediumi.groupby(["Zipcode"]).max()["Lat"]
Mediumlngi = Mediumi.groupby(["Zipcode"]).max()["Lng"]
Mediumpricei = Mediumi.groupby(["Zipcode"]).mean()["Price"]

Highlati = Highi.groupby(["Zipcode"]).max()["Lat"]
Highlngi = Highi.groupby(["Zipcode"]).max()["Lng"]
Highpricei = Highi.groupby(["Zipcode"]).mean()["Price"]
```


```python
plt.figure(figsize = (20,20))
plt.scatter(lowlngi, 
            lowlati, 
            s=lowpricei / 10000, c="red", 
            edgecolor="black", linewidths=1, marker="o", 
            alpha=0.8, label="Low Income (Under 50k)")

plt.scatter(Mediumlngi, 
            Mediumlati, 
            s=Mediumpricei / 10000, c="orange", 
            edgecolor="black", linewidths=1, marker="o", 
            alpha=0.8, label="Medium Income (Between 50k and 120k)")

plt.scatter(Highlngi, 
            Highlati, 
            s=Highpricei / 10000, c="blue", 
            edgecolor="black", linewidths=1, marker="o", 
            alpha=0.8, label="High Population (Greater than 120k)")

plt.title("Income vs Price for 2 bedroom homes in the US")
plt.ylabel("Latitude")
plt.xlabel("Longitude")
plt.grid(True)

# Create a legend
lgnd = plt.legend(fontsize="small", mode="Expanded", 
                  numpoints=1, scatterpoints=1, 
                  loc="best", title="Median Income", 
                  labelspacing=0.5)
lgnd.legendHandles[0]._sizes = [30]
lgnd.legendHandles[1]._sizes = [30]
lgnd.legendHandles[2]._sizes = [30]

plt.xlim((-140,-60))
# Incorporate a text label regarding circle size
# plt.text(42, 35, "Note:\nCircle size correlates with driver count per city.")
plt.savefig("output_Income_price_2bedroom.png")
plt.show()
```


![png](combined_code_files/combined_code_42_0.png)



```python
Lowi2 = latlngmerge2[latlngmerge2["Income"] == "Low"]
Mediumi2 = latlngmerge2[latlngmerge2["Income"] == "Middle"]
Highi2 = latlngmerge2[latlngmerge2["Income"] == "High"]

lowlati2 = Lowi2.groupby(["Zipcode"]).max()["Lat"]
lowlngi2 = Lowi2.groupby(["Zipcode"]).max()["Lng"]
lowpricei2 = Lowi2.groupby(["Zipcode"]).mean()["Price"]

Mediumlati2 = Mediumi2.groupby(["Zipcode"]).max()["Lat"]
Mediumlngi2 = Mediumi2.groupby(["Zipcode"]).max()["Lng"]
Mediumpricei2 = Mediumi2.groupby(["Zipcode"]).mean()["Price"]

Highlati2 = Highi2.groupby(["Zipcode"]).max()["Lat"]
Highlngi2 = Highi2.groupby(["Zipcode"]).max()["Lng"]
Highpricei2 = Highi2.groupby(["Zipcode"]).mean()["Price"]
```


```python
plt.figure(figsize = (20,20))
plt.scatter(lowlngi2, 
            lowlati2, 
            s=lowpricei2 / 10000, c="red", 
            edgecolor="black", linewidths=1, marker="o", 
            alpha=0.8, label="Low Income (Under 50k)")

plt.scatter(Mediumlngi2, 
            Mediumlati2, 
            s=Mediumpricei2 / 10000, c="orange", 
            edgecolor="black", linewidths=1, marker="o", 
            alpha=0.8, label="Medium Income (Between 50k and 120k)")

plt.scatter(Highlngi2, 
            Highlati2, 
            s=Highpricei2 / 10000, c="blue", 
            edgecolor="black", linewidths=1, marker="o", 
            alpha=0.8, label="High Population (Greater than 120k)")

plt.title("Income vs Price for 3 bedroom homes in the US")
plt.ylabel("Latitude")
plt.xlabel("Longitude")
plt.grid(True)

# Create a legend
lgnd = plt.legend(fontsize="small", mode="Expanded", 
                  numpoints=1, scatterpoints=1, 
                  loc="best", title="Median Income", 
                  labelspacing=0.5)
lgnd.legendHandles[0]._sizes = [30]
lgnd.legendHandles[1]._sizes = [30]
lgnd.legendHandles[2]._sizes = [30]

plt.xlim((-140,-60))
# Incorporate a text label regarding circle size
# plt.text(42, 35, "Note:\nCircle size correlates with driver count per city.")

plt.savefig("output_Income_price_3bedroom.png")
plt.show()
```


![png](combined_code_files/combined_code_44_0.png)



```python
#Income Vs 3 bedroom house in california
ibins = [0, 50000, 120000, 999999]

igroup_names = ['Low', 'Middle', 'High']
pd.cut(california_df["Income"], ibins, labels=igroup_names)
california_df["Income"] = pd.cut(california_df["Income"],ibins, labels=igroup_names)

```


    ---------------------------------------------------------------------------

    TypeError                                 Traceback (most recent call last)

    <ipython-input-51-8d93eb389e68> in <module>()
          3 
          4 igroup_names = ['Low', 'Middle', 'High']
    ----> 5 pd.cut(california_df["Income"], ibins, labels=igroup_names)
          6 california_df["Income"] = pd.cut(california_df["Income"],ibins, labels=igroup_names)
    

    ~\Anaconda3\envs\phytonData\lib\site-packages\pandas\core\reshape\tile.py in cut(x, bins, right, labels, retbins, precision, include_lowest)
        134                               precision=precision,
        135                               include_lowest=include_lowest,
    --> 136                               dtype=dtype)
        137 
        138     return _postprocess_for_cut(fac, bins, retbins, x_is_series,
    

    ~\Anaconda3\envs\phytonData\lib\site-packages\pandas\core\reshape\tile.py in _bins_to_cuts(x, bins, right, labels, precision, include_lowest, dtype, duplicates)
        235 
        236     side = 'left' if right else 'right'
    --> 237     ids = _ensure_int64(bins.searchsorted(x, side=side))
        238 
        239     if include_lowest:
    

    TypeError: '<' not supported between instances of 'int' and 'str'



```python
Lowpop = california_df[california_df["Income"] == "Low"]
Mediumpop = california_df[california_df["Income"] == "Middle"]
Highpop = california_df[california_df["Income"] == "High"]

lowlat = Lowpop["Lat"]
lowlng = Lowpop["Lng"]
lowprice = Lowpop["Price"]

Mediumlat = Mediumpop["Lat"]
Mediumlng = Mediumpop["Lng"]
Mediumprice = Mediumpop["Price"]

Highlat = Highpop["Lat"]
Highlng = Highpop["Lng"]
Highprice = Highpop["Price"]
```


```python
plt.figure(figsize = (10,10))
plt.scatter(lowlng, 
            lowlat, 
            s=lowprice / 10000, c="red", 
            edgecolor="black", linewidths=1, marker="o", 
            alpha=0.8, label="Low Income (Under 50k)")

plt.scatter(Mediumlng, 
            Mediumlat, 
            s=Mediumprice / 10000, c="orange", 
            edgecolor="black", linewidths=1, marker="o", 
            alpha=0.8, label="Medium Income (Between 50k and 120k)")

plt.scatter(Highlng, 
            Highlat, 
            s=Highprice / 10000, c="blue", 
            edgecolor="black", linewidths=1, marker="o", 
            alpha=0.8, label="High Income (Greater than 120k)")

plt.title("Income vs Price for 2 bedroom homes in the California")
plt.ylabel("Latitude")
plt.xlabel("Longitude")
plt.grid(True)

# Create a legend
lgnd = plt.legend(fontsize="small", mode="Expanded", 
                  numpoints=1, scatterpoints=1, 
                  loc="best", title="Income Density", 
                  labelspacing=0.5)
lgnd.legendHandles[0]._sizes = [30]
lgnd.legendHandles[1]._sizes = [30]
lgnd.legendHandles[2]._sizes = [30]

plt.xlim((-125,-115))
# Incorporate a text label regarding circle size
# plt.text(42, 35, "Note:\nCircle size correlates with driver count per city.")
plt.savefig("output_Income_price_CA.png")
plt.show()
```


![png](combined_code_files/combined_code_47_0.png)

