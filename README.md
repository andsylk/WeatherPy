
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


```python
# Clean up dataframe 
cleancity = clean_city_df.dropna(axis=0, how='any')
cleancity["Date"]= cleancity["Date"].astype(int)
cleancity = cleancity.drop('City', 1)

```

```python
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
cleancity
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
    <tr>
      <th>7</th>
      <td>7</td>
      <td>Kapaa</td>
      <td>90.0</td>
      <td>US</td>
      <td>1514059200</td>
      <td>100.0</td>
      <td>22.08</td>
      <td>-159.32</td>
      <td>73.40</td>
      <td>10.29</td>
    </tr>
    <tr>
      <th>8</th>
      <td>8</td>
      <td>Kamarion</td>
      <td>76.0</td>
      <td>GR</td>
      <td>1514060400</td>
      <td>100.0</td>
      <td>36.80</td>
      <td>25.82</td>
      <td>50.00</td>
      <td>26.40</td>
    </tr>
    <tr>
      <th>9</th>
      <td>9</td>
      <td>Karpokhorion</td>
      <td>20.0</td>
      <td>GR</td>
      <td>1514058600</td>
      <td>64.0</td>
      <td>39.33</td>
      <td>22.02</td>
      <td>37.40</td>
      <td>11.41</td>
    </tr>
    <tr>
      <th>10</th>
      <td>10</td>
      <td>Thinadhoo</td>
      <td>76.0</td>
      <td>MV</td>
      <td>1514061563</td>
      <td>100.0</td>
      <td>0.53</td>
      <td>72.93</td>
      <td>80.99</td>
      <td>2.21</td>
    </tr>
    <tr>
      <th>11</th>
      <td>11</td>
      <td>Barrow</td>
      <td>0.0</td>
      <td>AR</td>
      <td>1514061297</td>
      <td>40.0</td>
      <td>-38.31</td>
      <td>-60.23</td>
      <td>65.64</td>
      <td>13.40</td>
    </tr>
    <tr>
      <th>12</th>
      <td>12</td>
      <td>Kearney</td>
      <td>90.0</td>
      <td>US</td>
      <td>1514060100</td>
      <td>73.0</td>
      <td>40.70</td>
      <td>-99.08</td>
      <td>23.00</td>
      <td>6.93</td>
    </tr>
    <tr>
      <th>13</th>
      <td>13</td>
      <td>Sola</td>
      <td>90.0</td>
      <td>FI</td>
      <td>1514060400</td>
      <td>100.0</td>
      <td>62.78</td>
      <td>29.36</td>
      <td>30.20</td>
      <td>3.36</td>
    </tr>
    <tr>
      <th>14</th>
      <td>14</td>
      <td>Maceio</td>
      <td>75.0</td>
      <td>BR</td>
      <td>1514059200</td>
      <td>94.0</td>
      <td>-9.67</td>
      <td>-35.74</td>
      <td>75.20</td>
      <td>8.05</td>
    </tr>
    <tr>
      <th>15</th>
      <td>15</td>
      <td>Puerto Escondido</td>
      <td>75.0</td>
      <td>MX</td>
      <td>1514058000</td>
      <td>78.0</td>
      <td>15.86</td>
      <td>-97.07</td>
      <td>82.40</td>
      <td>8.05</td>
    </tr>
    <tr>
      <th>16</th>
      <td>16</td>
      <td>Hays</td>
      <td>90.0</td>
      <td>US</td>
      <td>1514058960</td>
      <td>73.0</td>
      <td>38.88</td>
      <td>-99.33</td>
      <td>24.80</td>
      <td>6.93</td>
    </tr>
    <tr>
      <th>17</th>
      <td>17</td>
      <td>Tuktoyaktuk</td>
      <td>20.0</td>
      <td>CA</td>
      <td>1514059200</td>
      <td>91.0</td>
      <td>69.44</td>
      <td>-133.03</td>
      <td>8.60</td>
      <td>13.87</td>
    </tr>
    <tr>
      <th>19</th>
      <td>19</td>
      <td>San Juan</td>
      <td>40.0</td>
      <td>PR</td>
      <td>1514058960</td>
      <td>69.0</td>
      <td>18.47</td>
      <td>-66.12</td>
      <td>82.40</td>
      <td>17.22</td>
    </tr>
    <tr>
      <th>20</th>
      <td>20</td>
      <td>Horsham</td>
      <td>0.0</td>
      <td>AU</td>
      <td>1514061520</td>
      <td>73.0</td>
      <td>-36.71</td>
      <td>142.20</td>
      <td>59.12</td>
      <td>12.66</td>
    </tr>
    <tr>
      <th>21</th>
      <td>21</td>
      <td>Vanimo</td>
      <td>56.0</td>
      <td>PG</td>
      <td>1514059929</td>
      <td>100.0</td>
      <td>-2.67</td>
      <td>141.30</td>
      <td>83.55</td>
      <td>8.86</td>
    </tr>
    <tr>
      <th>22</th>
      <td>22</td>
      <td>Pundaguitan</td>
      <td>44.0</td>
      <td>PH</td>
      <td>1514061693</td>
      <td>100.0</td>
      <td>6.37</td>
      <td>126.17</td>
      <td>79.68</td>
      <td>1.99</td>
    </tr>
    <tr>
      <th>23</th>
      <td>23</td>
      <td>Erzin</td>
      <td>75.0</td>
      <td>TR</td>
      <td>1514060400</td>
      <td>82.0</td>
      <td>36.95</td>
      <td>36.20</td>
      <td>60.80</td>
      <td>8.05</td>
    </tr>
    <tr>
      <th>24</th>
      <td>24</td>
      <td>Aleksandrovskoye</td>
      <td>88.0</td>
      <td>RU</td>
      <td>1514061694</td>
      <td>88.0</td>
      <td>56.74</td>
      <td>85.39</td>
      <td>13.35</td>
      <td>3.78</td>
    </tr>
    <tr>
      <th>25</th>
      <td>25</td>
      <td>Vagur</td>
      <td>88.0</td>
      <td>FO</td>
      <td>1514061694</td>
      <td>100.0</td>
      <td>61.47</td>
      <td>-6.81</td>
      <td>45.30</td>
      <td>18.10</td>
    </tr>
    <tr>
      <th>26</th>
      <td>26</td>
      <td>Luganville</td>
      <td>75.0</td>
      <td>VU</td>
      <td>1514059200</td>
      <td>94.0</td>
      <td>-15.51</td>
      <td>167.18</td>
      <td>75.20</td>
      <td>2.24</td>
    </tr>
    <tr>
      <th>27</th>
      <td>27</td>
      <td>Tarumovka</td>
      <td>92.0</td>
      <td>RU</td>
      <td>1514061695</td>
      <td>100.0</td>
      <td>44.07</td>
      <td>46.54</td>
      <td>38.28</td>
      <td>5.23</td>
    </tr>
    <tr>
      <th>28</th>
      <td>28</td>
      <td>Polewali</td>
      <td>12.0</td>
      <td>ID</td>
      <td>1514061695</td>
      <td>95.0</td>
      <td>-4.03</td>
      <td>119.80</td>
      <td>73.61</td>
      <td>5.73</td>
    </tr>
    <tr>
      <th>29</th>
      <td>29</td>
      <td>Panguna</td>
      <td>100.0</td>
      <td>PG</td>
      <td>1514061696</td>
      <td>98.0</td>
      <td>-6.32</td>
      <td>155.48</td>
      <td>73.11</td>
      <td>0.47</td>
    </tr>
    <tr>
      <th>30</th>
      <td>30</td>
      <td>Bayir</td>
      <td>75.0</td>
      <td>TR</td>
      <td>1514060400</td>
      <td>95.0</td>
      <td>37.27</td>
      <td>28.22</td>
      <td>46.40</td>
      <td>16.11</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>567</th>
      <td>567</td>
      <td>Kulykiv</td>
      <td>90.0</td>
      <td>UA</td>
      <td>1514062800</td>
      <td>100.0</td>
      <td>49.98</td>
      <td>24.08</td>
      <td>35.60</td>
      <td>15.66</td>
    </tr>
    <tr>
      <th>568</th>
      <td>568</td>
      <td>Badarpur</td>
      <td>0.0</td>
      <td>IN</td>
      <td>1514065630</td>
      <td>88.0</td>
      <td>24.87</td>
      <td>92.56</td>
      <td>55.47</td>
      <td>3.71</td>
    </tr>
    <tr>
      <th>569</th>
      <td>569</td>
      <td>Aktau</td>
      <td>90.0</td>
      <td>KZ</td>
      <td>1514062800</td>
      <td>100.0</td>
      <td>43.65</td>
      <td>51.16</td>
      <td>32.00</td>
      <td>13.42</td>
    </tr>
    <tr>
      <th>570</th>
      <td>570</td>
      <td>Salalah</td>
      <td>0.0</td>
      <td>OM</td>
      <td>1514062200</td>
      <td>23.0</td>
      <td>17.01</td>
      <td>54.10</td>
      <td>73.40</td>
      <td>6.93</td>
    </tr>
    <tr>
      <th>571</th>
      <td>571</td>
      <td>Morant Bay</td>
      <td>20.0</td>
      <td>JM</td>
      <td>1514062800</td>
      <td>58.0</td>
      <td>17.88</td>
      <td>-76.41</td>
      <td>87.80</td>
      <td>4.70</td>
    </tr>
    <tr>
      <th>572</th>
      <td>572</td>
      <td>Moron</td>
      <td>40.0</td>
      <td>VE</td>
      <td>1514062800</td>
      <td>55.0</td>
      <td>10.49</td>
      <td>-68.20</td>
      <td>87.80</td>
      <td>4.27</td>
    </tr>
    <tr>
      <th>573</th>
      <td>573</td>
      <td>Estevan</td>
      <td>90.0</td>
      <td>CA</td>
      <td>1514064900</td>
      <td>78.0</td>
      <td>49.14</td>
      <td>-102.99</td>
      <td>14.00</td>
      <td>13.87</td>
    </tr>
    <tr>
      <th>574</th>
      <td>574</td>
      <td>Uvalde</td>
      <td>1.0</td>
      <td>US</td>
      <td>1514063700</td>
      <td>27.0</td>
      <td>29.21</td>
      <td>-99.79</td>
      <td>62.60</td>
      <td>4.61</td>
    </tr>
    <tr>
      <th>575</th>
      <td>575</td>
      <td>Kahului</td>
      <td>20.0</td>
      <td>US</td>
      <td>1514062560</td>
      <td>50.0</td>
      <td>20.89</td>
      <td>-156.47</td>
      <td>78.80</td>
      <td>13.87</td>
    </tr>
    <tr>
      <th>576</th>
      <td>576</td>
      <td>Itupiranga</td>
      <td>40.0</td>
      <td>BR</td>
      <td>1514062800</td>
      <td>100.0</td>
      <td>-5.13</td>
      <td>-49.33</td>
      <td>77.00</td>
      <td>3.36</td>
    </tr>
    <tr>
      <th>577</th>
      <td>577</td>
      <td>Maragogi</td>
      <td>92.0</td>
      <td>BR</td>
      <td>1514065633</td>
      <td>100.0</td>
      <td>-9.01</td>
      <td>-35.22</td>
      <td>76.22</td>
      <td>6.85</td>
    </tr>
    <tr>
      <th>578</th>
      <td>578</td>
      <td>Cururupu</td>
      <td>56.0</td>
      <td>BR</td>
      <td>1514065634</td>
      <td>64.0</td>
      <td>-1.82</td>
      <td>-44.87</td>
      <td>84.77</td>
      <td>10.27</td>
    </tr>
    <tr>
      <th>579</th>
      <td>579</td>
      <td>Crisan</td>
      <td>0.0</td>
      <td>RO</td>
      <td>1514064600</td>
      <td>100.0</td>
      <td>44.97</td>
      <td>29.47</td>
      <td>32.00</td>
      <td>10.29</td>
    </tr>
    <tr>
      <th>580</th>
      <td>580</td>
      <td>Acapulco</td>
      <td>75.0</td>
      <td>MX</td>
      <td>1514062080</td>
      <td>74.0</td>
      <td>16.86</td>
      <td>-99.88</td>
      <td>84.20</td>
      <td>16.11</td>
    </tr>
    <tr>
      <th>581</th>
      <td>581</td>
      <td>Tromso</td>
      <td>75.0</td>
      <td>NO</td>
      <td>1514064000</td>
      <td>92.0</td>
      <td>69.65</td>
      <td>18.96</td>
      <td>28.40</td>
      <td>17.22</td>
    </tr>
    <tr>
      <th>582</th>
      <td>582</td>
      <td>Mittagong</td>
      <td>0.0</td>
      <td>AU</td>
      <td>1514062800</td>
      <td>47.0</td>
      <td>-34.45</td>
      <td>150.45</td>
      <td>78.80</td>
      <td>10.29</td>
    </tr>
    <tr>
      <th>583</th>
      <td>583</td>
      <td>Tulun</td>
      <td>80.0</td>
      <td>RU</td>
      <td>1514065650</td>
      <td>82.0</td>
      <td>54.56</td>
      <td>100.58</td>
      <td>10.79</td>
      <td>2.48</td>
    </tr>
    <tr>
      <th>584</th>
      <td>584</td>
      <td>Onguday</td>
      <td>64.0</td>
      <td>RU</td>
      <td>1514065650</td>
      <td>78.0</td>
      <td>50.75</td>
      <td>86.14</td>
      <td>3.72</td>
      <td>2.48</td>
    </tr>
    <tr>
      <th>586</th>
      <td>586</td>
      <td>Maunabo</td>
      <td>40.0</td>
      <td>PR</td>
      <td>1514063100</td>
      <td>61.0</td>
      <td>18.01</td>
      <td>-65.90</td>
      <td>80.60</td>
      <td>17.22</td>
    </tr>
    <tr>
      <th>587</th>
      <td>587</td>
      <td>Prestea</td>
      <td>0.0</td>
      <td>GH</td>
      <td>1514065712</td>
      <td>76.0</td>
      <td>5.43</td>
      <td>-2.14</td>
      <td>74.06</td>
      <td>2.55</td>
    </tr>
    <tr>
      <th>588</th>
      <td>588</td>
      <td>Ixtapa</td>
      <td>75.0</td>
      <td>MX</td>
      <td>1514062140</td>
      <td>69.0</td>
      <td>20.71</td>
      <td>-105.21</td>
      <td>80.60</td>
      <td>5.82</td>
    </tr>
    <tr>
      <th>589</th>
      <td>589</td>
      <td>Ekibastuz</td>
      <td>0.0</td>
      <td>KZ</td>
      <td>1514065713</td>
      <td>61.0</td>
      <td>51.72</td>
      <td>75.32</td>
      <td>4.31</td>
      <td>9.15</td>
    </tr>
    <tr>
      <th>591</th>
      <td>591</td>
      <td>Lhokseumawe</td>
      <td>92.0</td>
      <td>ID</td>
      <td>1514065774</td>
      <td>100.0</td>
      <td>5.18</td>
      <td>97.15</td>
      <td>75.72</td>
      <td>3.60</td>
    </tr>
    <tr>
      <th>592</th>
      <td>592</td>
      <td>Santa Vitoria do Palmar</td>
      <td>92.0</td>
      <td>BR</td>
      <td>1514065774</td>
      <td>94.0</td>
      <td>-33.52</td>
      <td>-53.37</td>
      <td>69.24</td>
      <td>21.90</td>
    </tr>
    <tr>
      <th>594</th>
      <td>594</td>
      <td>Saldanha</td>
      <td>0.0</td>
      <td>PT</td>
      <td>1514065835</td>
      <td>90.0</td>
      <td>41.42</td>
      <td>-6.55</td>
      <td>31.62</td>
      <td>5.06</td>
    </tr>
    <tr>
      <th>595</th>
      <td>595</td>
      <td>Banda Aceh</td>
      <td>64.0</td>
      <td>ID</td>
      <td>1514065615</td>
      <td>94.0</td>
      <td>5.56</td>
      <td>95.32</td>
      <td>75.72</td>
      <td>4.23</td>
    </tr>
    <tr>
      <th>596</th>
      <td>596</td>
      <td>Kysyl-Syr</td>
      <td>56.0</td>
      <td>RU</td>
      <td>1514065836</td>
      <td>64.0</td>
      <td>63.90</td>
      <td>122.77</td>
      <td>-34.71</td>
      <td>4.34</td>
    </tr>
    <tr>
      <th>597</th>
      <td>597</td>
      <td>Road Town</td>
      <td>40.0</td>
      <td>VG</td>
      <td>1514062800</td>
      <td>74.0</td>
      <td>18.42</td>
      <td>-64.62</td>
      <td>82.40</td>
      <td>18.34</td>
    </tr>
    <tr>
      <th>598</th>
      <td>598</td>
      <td>Port Hedland</td>
      <td>0.0</td>
      <td>AU</td>
      <td>1514062800</td>
      <td>88.0</td>
      <td>-20.31</td>
      <td>118.58</td>
      <td>80.60</td>
      <td>3.36</td>
    </tr>
    <tr>
      <th>599</th>
      <td>599</td>
      <td>Asyut</td>
      <td>0.0</td>
      <td>EG</td>
      <td>1514062800</td>
      <td>62.0</td>
      <td>27.18</td>
      <td>31.19</td>
      <td>59.00</td>
      <td>11.41</td>
    </tr>
  </tbody>
</table>
<p>538 rows × 10 columns</p>
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

