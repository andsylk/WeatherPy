
### WeatherPy Analysis
#### Observed Trends

#### 1) Cloudiness has the least correlation with Latitude compared with other variables.  The slope is basically flat.
#### 2) Max Temperature is the highest when Latitude is 0 (or near the equator).  Cities that are further out from the equator have lower max temperatures.
#### 3) The majority of cities in the sample tended to have high humidity percentages and low wind speeds.


```python
# Dependencies
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import requests as req
import random
import json
from citipy import citipy
plt.style.use("seaborn")
import time

# API Key
api_key = "43f0caa6aca8465b6f439da6bda4e8ca"

# Build query URL
units = "imperial"
url = "http://api.openweathermap.org/data/2.5/weather?"

# Build partial query URL
query_url = url + "appid=" + api_key + "&units=" + units + "&q=" # + city
```


```python
# get random sample of latitude and longitude points and pass through citipy to get a list of cities
lat = []
lng = []
city_list = []

for latitude in np.random.uniform(-90,90,3000):
    lat.append(latitude)
for longitude in np.random.uniform(-180,180,3000):
    lng.append(longitude)
lat_lng = zip(lat,lng)
for lat, lng in lat_lng:
    city = citipy.nearest_city(lat, lng)
    city_list.append(city.city_name)

# From the list, get rid of duplicates and get a random sample of 600 cities 
city_list_no_dupes = random.sample(set(city_list),600)
clean_city_list = [city.replace(" ", "+") for city in city_list_no_dupes]

clean_city_df = pd.DataFrame(clean_city_list, columns = ["City"]).reset_index()
```


```python
# Call OpenWeatherMap API and get data points for 500+ cities
for index,row in clean_city_df.iterrows():
    try:
        city = row["City"]
        response = req.get(query_url + city).json()
        city1 = response["name"]
        clean_city_df.set_value(index, "City Name", city1)
        cloudiness = response["clouds"]["all"]
        clean_city_df.set_value(index,"Cloudiness",cloudiness)
        country = response["sys"]["country"]
        clean_city_df.set_value(index, "Country", country)
        date = response["dt"]
        clean_city_df.set_value(index, "Date", date)
        humidity = response["main"]["humidity"]
        clean_city_df.set_value(index, "Humidity", humidity)
        lats = response["coord"]["lat"]
        clean_city_df.set_value(index, "Lat", lats)
        lons = response["coord"]["lon"]
        clean_city_df.set_value(index, "Lng", lons)
        max_temp = response["main"]["temp_max"]
        clean_city_df.set_value(index, "Max Temp", max_temp)
        wind_speed = response["wind"]["speed"]
        clean_city_df.set_value(index, "Wind Speed", wind_speed)
    except:
        print("Error with city data. Skipping")
        time.sleep(60)
        continue

# Clean up dataframe 
cleancity = clean_city_df.dropna(axis=0, how='any')
cleancity["Date"]= cleancity["Date"].astype(int)
cleancity = cleancity.drop('City', 1)

# Counts of clean version if cities dataframe
cleancity.count()
```

    index         538
    City Name     538
    Cloudiness    538
    Country       538
    Date          538
    Humidity      538
    Lat           538
    Lng           538
    Max Temp      538
    Wind Speed    538
    dtype: int64

```python
cleancity.head()
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
      <th>index</th>
      <th>City Name</th>
      <th>Cloudiness</th>
      <th>Country</th>
      <th>Date</th>
      <th>Humidity</th>
      <th>Lat</th>
      <th>Lng</th>
      <th>Max Temp</th>
      <th>Wind Speed</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>Villa Carlos Paz</td>
      <td>0.0</td>
      <td>AR</td>
      <td>1514059200</td>
      <td>47.0</td>
      <td>-31.42</td>
      <td>-64.50</td>
      <td>77.00</td>
      <td>14.99</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>Marsa Matruh</td>
      <td>40.0</td>
      <td>EG</td>
      <td>1514059200</td>
      <td>67.0</td>
      <td>31.35</td>
      <td>27.25</td>
      <td>60.80</td>
      <td>11.41</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>Muravlenko</td>
      <td>80.0</td>
      <td>RU</td>
      <td>1514061496</td>
      <td>85.0</td>
      <td>63.79</td>
      <td>74.50</td>
      <td>15.33</td>
      <td>14.97</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>Ola</td>
      <td>12.0</td>
      <td>RU</td>
      <td>1514055600</td>
      <td>64.0</td>
      <td>59.58</td>
      <td>151.30</td>
      <td>-41.81</td>
      <td>11.27</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>Vredendal</td>
      <td>0.0</td>
      <td>ZA</td>
      <td>1514061560</td>
      <td>95.0</td>
      <td>-31.68</td>
      <td>18.49</td>
      <td>55.16</td>
      <td>2.77</td>
    </tr>
    <tr>
      <th>5</th>
      <td>5</td>
      <td>Teguldet</td>
      <td>68.0</td>
      <td>RU</td>
      <td>1514061561</td>
      <td>87.0</td>
      <td>57.31</td>
      <td>88.17</td>
      <td>8.72</td>
      <td>2.59</td>
    </tr>
    <tr>
      <th>6</th>
      <td>6</td>
      <td>Karratha</td>
      <td>0.0</td>
      <td>AU</td>
      <td>1514061561</td>
      <td>94.0</td>
      <td>-20.74</td>
      <td>116.85</td>
      <td>74.51</td>
      <td>5.57</td>
    </tr>
  </tbody>
</table>
<p>6 rows × 10 columns</p>
</div>



### Latitude vs Temperature Plot


```python
# Scatter Plot Number 1
cleancity.plot(kind="scatter", x="Lat", y="Max Temp", grid =True, edgecolor = "black",color = "darkblue", figsize =(10,8))
plt.title("City Latitude vs Max Temperature (12/22/2017)", fontsize = 18, fontweight='bold')
plt.xlabel("Latitude",fontsize = 14)
plt.ylabel("Max Temperature (F)",fontsize = 14)
plt.xlim(-80,100)
plt.ylim(-100, 150)
plt.tick_params(labelsize=13)
plt.show()
```


![png](output_9_0.png)


### Latitude vs Humidity Plot


```python
# Scatter Plot Number 2
cleancity.plot(kind="scatter", x="Lat", y="Humidity", grid =True, edgecolor = "black",color = "darkblue", figsize =(10,8))
plt.title("City Latitude vs Humidity (12/22/2017)", fontsize = 18, fontweight='bold')
plt.xlabel("Latitude",fontsize = 14)
plt.ylabel("Humidity (%)",fontsize = 14)
plt.xlim(-80,100)
plt.ylim(-20, 120)
plt.tick_params(labelsize=13)
plt.show()
```


![png](output_11_0.png)


### Latitude vs Cloudiness Plot


```python
# Scatter Plot Number 3
cleancity.plot(kind="scatter", x="Lat", y="Cloudiness", grid =True, edgecolor = "black",color = "darkblue", figsize =(10,8))
plt.title("City Latitude vs Cloudiness (12/22/2017)", fontsize = 18, fontweight='bold')
plt.xlabel("Latitude",fontsize = 14)
plt.ylabel("Cloudiness (%)",fontsize = 14)
plt.xlim(-80,100)
plt.ylim(-20, 120)
plt.tick_params(labelsize=13)
plt.show()
```


![png](output_13_0.png)


### Latitude vs Wind Speed Plot


```python
# Scatter Plot Number 4
cleancity.plot(kind="scatter", x="Lat", y="Wind Speed", grid =True, edgecolor = "black",color = "darkblue", figsize =(10,8))
plt.title("City Latitude vs Wind Speed (12/22/2017)", fontsize = 18, fontweight='bold')
plt.xlabel("Latitude",fontsize = 14)
plt.ylabel("Wind Speed (mph)",fontsize = 14)
plt.xlim(-80,100)
plt.ylim(-5, 40)
plt.tick_params(labelsize=13)
plt.show()
```


![png](output_15_0.png)

