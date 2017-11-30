

```python
# Dependencies
import json
import requests as req
import seaborn as sns
import pandas as pd
import numpy as np
import random
import matplotlib.pyplot as plt
from citipy import citipy
```


```python
# Latitude and longitude zones based on geographic coordinate system (in intervals of 10)
lat_zone = np.arange(-90, 90, 10)
lon_zone = np.arange(-180, 180, 10)

# Create dataframe for storing data
cities_df = pd.DataFrame()

# Make columns in dataframe
cities_df["Latitude"] = ""
cities_df["Longitude"] = ""
cities_df["City"] = ""
cities_df["Country"] = ""

# Create 'for' loop to get latitude and longitude values
for coord_lat in lat_zone:
    
    for coord_lon in lon_zone:
        # Get list of latitude and longitude values (0.01 for value to 2 decimal places)
        lat_values = list(np.arange(coord_lat, coord_lat + 15, 0.01))
        lon_values = list(np.arange(coord_lon, coord_lon + 15, 0.01))
        
        # Random latitude and longitude values 
        random_lats = random.sample(lat_values, 50)
        random_lons = random.sample(lon_values, 50)
        
        # Sample of latitude and longitude values
        lat_samples = [coord_lat + lat for lat in random_lats]
        lon_samples = [coord_lon + lon for lon in random_lons]
        
        # Store values in dataframe created
        cities_df = cities_df.append(pd.DataFrame.from_dict({
            "Latitude": lat_samples, 
            "Longitude": lon_samples}))

cities_df = cities_df.reset_index(drop=True)
cities_df.head()
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
      <th>City</th>
      <th>Country</th>
      <th>Latitude</th>
      <th>Longitude</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>-174.27</td>
      <td>-347.40</td>
    </tr>
    <tr>
      <th>1</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>-167.26</td>
      <td>-351.92</td>
    </tr>
    <tr>
      <th>2</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>-168.55</td>
      <td>-354.87</td>
    </tr>
    <tr>
      <th>3</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>-175.38</td>
      <td>-348.56</td>
    </tr>
    <tr>
      <th>4</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>-165.21</td>
      <td>-352.03</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Use 'for' loop to locate the neareast city based on latitude and longitude stored in dataframe
for column, row in cities_df.iterrows():
    city = citipy.nearest_city(row["Latitude"], row["Longitude"])
    cities_df.set_value(column, "City", city.city_name)
    cities_df.set_value(column, "Country", city.country_code)

# Remove Latitude and Longitude columns to get city names and countries only
new_cities_df = cities_df.drop(["Latitude", "Longitude"], axis=1)

# Remove duplicate cities and keep unique cities and countries only
new_cities_df = new_cities_df.drop_duplicates()
new_cities_df.head()
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
      <th>City</th>
      <th>Country</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>vaini</td>
      <td>to</td>
    </tr>
    <tr>
      <th>451</th>
      <td>mataura</td>
      <td>pf</td>
    </tr>
    <tr>
      <th>500</th>
      <td>punta arenas</td>
      <td>cl</td>
    </tr>
    <tr>
      <th>521</th>
      <td>ushuaia</td>
      <td>ar</td>
    </tr>
    <tr>
      <th>900</th>
      <td>bredasdorp</td>
      <td>za</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Choose 500 random cities based on longitude and latitude
random_cities_df = new_cities_df.sample(500)

# Reset index
random_cities_df = random_cities_df.reset_index(drop=True)
random_cities_df.head()
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
      <th>City</th>
      <th>Country</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>kyra</td>
      <td>ru</td>
    </tr>
    <tr>
      <th>1</th>
      <td>jarjis</td>
      <td>tn</td>
    </tr>
    <tr>
      <th>2</th>
      <td>inverell</td>
      <td>au</td>
    </tr>
    <tr>
      <th>3</th>
      <td>glens falls</td>
      <td>us</td>
    </tr>
    <tr>
      <th>4</th>
      <td>elko</td>
      <td>us</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Save config info
url = "http://api.openweathermap.org/data/2.5/weather"

params = {"appid": "2ec88724f3fad131ccffe1dfb2efab45",
          "units": "metric"}

# Use 'for' loop to retrieve weather info by rows in dataframe
for column, row in random_cities_df.iterrows():
    
    # Reference rows for 'q' param
    params["q"] = f'{row["City"]}, {row["Country"]}'
    
    # Get and print links for each city
    print(f'Weather information for {params["q"]}')
    weather_response = req.get(url, params)
    print(weather_response.url)
    weather_response  = weather_response.json()
    
    # Get weather data and input into dataframe
    random_cities_df.set_value(column, "Latitude", weather_response.get("coord", {}).get("lat"))
    random_cities_df.set_value(column, "Longitude", weather_response.get("coord", {}).get("lon"))
    random_cities_df.set_value(column, "Temperature", weather_response.get("main", {}).get("temp_max"))
    random_cities_df.set_value(column, "Wind Speed", weather_response.get("wind", {}).get("speed"))
    random_cities_df.set_value(column, "Humidity", weather_response.get("main", {}).get("humidity"))
    random_cities_df.set_value(column, "Cloudiness", weather_response.get("clouds", {}).get("all"))
```

    Weather information for kyra, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=kyra%2C+ru
    Weather information for jarjis, tn
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=jarjis%2C+tn
    Weather information for inverell, au
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=inverell%2C+au
    Weather information for glens falls, us
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=glens+falls%2C+us
    Weather information for elko, us
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=elko%2C+us
    Weather information for huazolotitlan, mx
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=huazolotitlan%2C+mx
    Weather information for tarata, pe
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=tarata%2C+pe
    Weather information for sisimiut, gl
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=sisimiut%2C+gl
    Weather information for abu zabad, sd
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=abu+zabad%2C+sd
    Weather information for yellowknife, ca
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=yellowknife%2C+ca
    Weather information for baton rouge, us
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=baton+rouge%2C+us
    Weather information for hovd, mn
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=hovd%2C+mn
    Weather information for batsfjord, no
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=batsfjord%2C+no
    Weather information for mafeteng, ls
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=mafeteng%2C+ls
    Weather information for atbasar, kz
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=atbasar%2C+kz
    Weather information for marystown, ca
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=marystown%2C+ca
    Weather information for salalah, om
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=salalah%2C+om
    Weather information for maryville, us
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=maryville%2C+us
    Weather information for pousat, kh
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=pousat%2C+kh
    Weather information for kermanshah, ir
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=kermanshah%2C+ir
    Weather information for leon, es
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=leon%2C+es
    Weather information for catamarca, ar
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=catamarca%2C+ar
    Weather information for nishihara, jp
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=nishihara%2C+jp
    Weather information for buariki, ki
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=buariki%2C+ki
    Weather information for tuatapere, nz
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=tuatapere%2C+nz
    Weather information for nkpor, ng
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=nkpor%2C+ng
    Weather information for ilyich, kz
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=ilyich%2C+kz
    Weather information for bojaya, co
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=bojaya%2C+co
    Weather information for rongcheng, cn
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=rongcheng%2C+cn
    Weather information for lasa, cn
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=lasa%2C+cn
    Weather information for penne, it
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=penne%2C+it
    Weather information for kumylzhenskaya, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=kumylzhenskaya%2C+ru
    Weather information for pangai, to
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=pangai%2C+to
    Weather information for tamworth, au
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=tamworth%2C+au
    Weather information for romny, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=romny%2C+ru
    Weather information for muskegon, us
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=muskegon%2C+us
    Weather information for new iberia, us
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=new+iberia%2C+us
    Weather information for shenjiamen, cn
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=shenjiamen%2C+cn
    Weather information for zermatt, ch
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=zermatt%2C+ch
    Weather information for pinega, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=pinega%2C+ru
    Weather information for haileybury, ca
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=haileybury%2C+ca
    Weather information for solnechnyy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=solnechnyy%2C+ru
    Weather information for tambura, sd
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=tambura%2C+sd
    Weather information for mitu, co
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=mitu%2C+co
    Weather information for jenjarom, my
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=jenjarom%2C+my
    Weather information for falam, mm
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=falam%2C+mm
    Weather information for sao geraldo do araguaia, br
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=sao+geraldo+do+araguaia%2C+br
    Weather information for lenki, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=lenki%2C+ru
    Weather information for sur, om
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=sur%2C+om
    Weather information for taltal, cl
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=taltal%2C+cl
    Weather information for shahreza, ir
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=shahreza%2C+ir
    Weather information for yablonovo, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=yablonovo%2C+ru
    Weather information for mandalgovi, mn
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=mandalgovi%2C+mn
    Weather information for florida, uy
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=florida%2C+uy
    Weather information for pokhara, np
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=pokhara%2C+np
    Weather information for sumbe, ao
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=sumbe%2C+ao
    Weather information for kilindoni, tz
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=kilindoni%2C+tz
    Weather information for manokwari, id
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=manokwari%2C+id
    Weather information for kramat, id
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=kramat%2C+id
    Weather information for amapa, br
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=amapa%2C+br
    Weather information for edd, er
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=edd%2C+er
    Weather information for masallatah, ly
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=masallatah%2C+ly
    Weather information for morondava, mg
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=morondava%2C+mg
    Weather information for sao filipe, cv
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=sao+filipe%2C+cv
    Weather information for woodstock, ca
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=woodstock%2C+ca
    Weather information for mantua, cu
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=mantua%2C+cu
    Weather information for ca mau, vn
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=ca+mau%2C+vn
    Weather information for angren, uz
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=angren%2C+uz
    Weather information for makat, kz
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=makat%2C+kz
    Weather information for katangli, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=katangli%2C+ru
    Weather information for albany, au
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=albany%2C+au
    Weather information for staryy nadym, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=staryy+nadym%2C+ru
    Weather information for ulety, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=ulety%2C+ru
    Weather information for kamina, cd
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=kamina%2C+cd
    Weather information for badarpur, in
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=badarpur%2C+in
    Weather information for tabira, br
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=tabira%2C+br
    Weather information for buqayq, sa
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=buqayq%2C+sa
    Weather information for riyadh, sa
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=riyadh%2C+sa
    Weather information for effium, ng
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=effium%2C+ng
    Weather information for baruun-urt, mn
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=baruun-urt%2C+mn
    Weather information for atambua, id
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=atambua%2C+id
    Weather information for cayenne, gf
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=cayenne%2C+gf
    Weather information for orlik, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=orlik%2C+ru
    Weather information for balagansk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=balagansk%2C+ru
    Weather information for alice springs, au
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=alice+springs%2C+au
    Weather information for tuburan, ph
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=tuburan%2C+ph
    Weather information for xunchang, cn
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=xunchang%2C+cn
    Weather information for cape town, za
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=cape+town%2C+za
    Weather information for mombetsu, jp
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=mombetsu%2C+jp
    Weather information for caibarien, cu
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=caibarien%2C+cu
    Weather information for outlook, ca
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=outlook%2C+ca
    Weather information for jega, ng
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=jega%2C+ng
    Weather information for cotonou, bj
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=cotonou%2C+bj
    Weather information for san luis, mx
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=san+luis%2C+mx
    Weather information for zhovti vody, ua
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=zhovti+vody%2C+ua
    Weather information for rockport, us
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=rockport%2C+us
    Weather information for mrirt, ma
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=mrirt%2C+ma
    Weather information for sabha, ly
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=sabha%2C+ly
    Weather information for jiwani, pk
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=jiwani%2C+pk
    Weather information for valdivia, cl
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=valdivia%2C+cl
    Weather information for okato, nz
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=okato%2C+nz
    Weather information for samusu, ws
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=samusu%2C+ws
    Weather information for kayerkan, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=kayerkan%2C+ru
    Weather information for palamos, es
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=palamos%2C+es
    Weather information for abu jubayhah, sd
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=abu+jubayhah%2C+sd
    Weather information for novovarshavka, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=novovarshavka%2C+ru
    Weather information for olafsvik, is
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=olafsvik%2C+is
    Weather information for townsville, au
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=townsville%2C+au
    Weather information for thompson, ca
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=thompson%2C+ca
    Weather information for muli, mv
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=muli%2C+mv
    Weather information for luebo, cd
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=luebo%2C+cd
    Weather information for talcahuano, cl
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=talcahuano%2C+cl
    Weather information for nizhniy kuranakh, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=nizhniy+kuranakh%2C+ru
    Weather information for belaya gora, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=belaya+gora%2C+ru
    Weather information for alghero, it
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=alghero%2C+it
    Weather information for igrim, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=igrim%2C+ru
    Weather information for cancun, mx
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=cancun%2C+mx
    Weather information for clocolan, za
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=clocolan%2C+za
    Weather information for khovu-aksy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=khovu-aksy%2C+ru
    Weather information for camopi, gf
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=camopi%2C+gf
    Weather information for cam ranh, vn
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=cam+ranh%2C+vn
    Weather information for araouane, ml
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=araouane%2C+ml
    Weather information for norton shores, us
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=norton+shores%2C+us
    Weather information for neepawa, ca
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=neepawa%2C+ca
    Weather information for boyolangu, id
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=boyolangu%2C+id
    Weather information for newport, us
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=newport%2C+us
    Weather information for dhidhdhoo, mv
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=dhidhdhoo%2C+mv
    Weather information for aswan, eg
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=aswan%2C+eg
    Weather information for tondano, id
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=tondano%2C+id
    Weather information for blagoyevo, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=blagoyevo%2C+ru
    Weather information for kazuno, jp
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=kazuno%2C+jp
    Weather information for jamestown, us
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=jamestown%2C+us
    Weather information for penzance, gb
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=penzance%2C+gb
    Weather information for datong, cn
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=datong%2C+cn
    Weather information for mount pleasant, us
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=mount+pleasant%2C+us
    Weather information for zimovniki, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=zimovniki%2C+ru
    Weather information for rochegda, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=rochegda%2C+ru
    Weather information for matamoros, mx
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=matamoros%2C+mx
    Weather information for vanimo, pg
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=vanimo%2C+pg
    Weather information for linxia, cn
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=linxia%2C+cn
    Weather information for ambam, cm
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=ambam%2C+cm
    Weather information for wattegama, lk
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=wattegama%2C+lk
    Weather information for altus, us
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=altus%2C+us
    Weather information for faya, td
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=faya%2C+td
    Weather information for katobu, id
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=katobu%2C+id
    Weather information for bolshaya martynovka, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=bolshaya+martynovka%2C+ru
    Weather information for cockburn town, bs
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=cockburn+town%2C+bs
    Weather information for nicoya, cr
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=nicoya%2C+cr
    Weather information for vzmorye, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=vzmorye%2C+ru
    Weather information for beloha, mg
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=beloha%2C+mg
    Weather information for oktyabrskoye, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=oktyabrskoye%2C+ru
    Weather information for portobelo, pa
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=portobelo%2C+pa
    Weather information for sangar, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=sangar%2C+ru
    Weather information for taoudenni, ml
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=taoudenni%2C+ml
    Weather information for tuktoyaktuk, ca
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=tuktoyaktuk%2C+ca
    Weather information for kushmurun, kz
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=kushmurun%2C+kz
    Weather information for iskateley, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=iskateley%2C+ru
    Weather information for nenjiang, cn
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=nenjiang%2C+cn
    Weather information for nhulunbuy, au
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=nhulunbuy%2C+au
    Weather information for port-de-bouc, fr
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=port-de-bouc%2C+fr
    Weather information for castleblayney, ie
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=castleblayney%2C+ie
    Weather information for sonoita, mx
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=sonoita%2C+mx
    Weather information for keokuk, us
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=keokuk%2C+us
    Weather information for cajamarca, pe
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=cajamarca%2C+pe
    Weather information for usogorsk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=usogorsk%2C+ru
    Weather information for san joaquin, bo
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=san+joaquin%2C+bo
    Weather information for luau, ao
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=luau%2C+ao
    Weather information for dakar, sn
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=dakar%2C+sn
    Weather information for tiznit, ma
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=tiznit%2C+ma
    Weather information for nivala, fi
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=nivala%2C+fi
    Weather information for hirara, jp
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=hirara%2C+jp
    Weather information for port elizabeth, za
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=port+elizabeth%2C+za
    Weather information for tanshui, tw
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=tanshui%2C+tw
    Weather information for urucara, br
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=urucara%2C+br
    Weather information for karasjok, no
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=karasjok%2C+no
    Weather information for kovdor, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=kovdor%2C+ru
    Weather information for weligama, lk
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=weligama%2C+lk
    Weather information for fasa, ir
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=fasa%2C+ir
    Weather information for bud, no
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=bud%2C+no
    Weather information for sann, pk
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=sann%2C+pk
    Weather information for almaznyy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=almaznyy%2C+ru
    Weather information for petrolina de goias, br
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=petrolina+de+goias%2C+br
    Weather information for puerto el triunfo, sv
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=puerto+el+triunfo%2C+sv
    Weather information for ixtapa, mx
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=ixtapa%2C+mx
    Weather information for aktau, kz
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=aktau%2C+kz
    Weather information for chuncheng, cn
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=chuncheng%2C+cn
    Weather information for lengshuitan, cn
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=lengshuitan%2C+cn
    Weather information for waris aliganj, in
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=waris+aliganj%2C+in
    Weather information for zhigansk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=zhigansk%2C+ru
    Weather information for estelle, us
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=estelle%2C+us
    Weather information for labranzagrande, co
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=labranzagrande%2C+co
    Weather information for puerto del rosario, es
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=puerto+del+rosario%2C+es
    Weather information for nsukka, ng
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=nsukka%2C+ng
    Weather information for pundaguitan, ph
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=pundaguitan%2C+ph
    Weather information for grenada, us
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=grenada%2C+us
    Weather information for ilo, pe
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=ilo%2C+pe
    Weather information for singapore, sg
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=singapore%2C+sg
    Weather information for dembi dolo, et
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=dembi+dolo%2C+et
    Weather information for tarudant, ma
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=tarudant%2C+ma
    Weather information for ordynskoye, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=ordynskoye%2C+ru
    Weather information for batticaloa, lk
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=batticaloa%2C+lk
    Weather information for malibu, us
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=malibu%2C+us
    Weather information for sinnamary, gf
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=sinnamary%2C+gf
    Weather information for mpanda, tz
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=mpanda%2C+tz
    Weather information for whitehorse, ca
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=whitehorse%2C+ca
    Weather information for cravo norte, co
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=cravo+norte%2C+co
    Weather information for juwana, id
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=juwana%2C+id
    Weather information for saint-georges, gf
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=saint-georges%2C+gf
    Weather information for adeje, es
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=adeje%2C+es
    Weather information for avarua, ck
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=avarua%2C+ck
    Weather information for karatuzskoye, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=karatuzskoye%2C+ru
    Weather information for oshkosh, us
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=oshkosh%2C+us
    Weather information for geraldton, au
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=geraldton%2C+au
    Weather information for cao bang, vn
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=cao+bang%2C+vn
    Weather information for prince albert, ca
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=prince+albert%2C+ca
    Weather information for chatellerault, fr
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=chatellerault%2C+fr
    Weather information for jutai, br
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=jutai%2C+br
    Weather information for sassandra, ci
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=sassandra%2C+ci
    Weather information for koslan, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=koslan%2C+ru
    Weather information for khatanga, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=khatanga%2C+ru
    Weather information for qaqortoq, gl
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=qaqortoq%2C+gl
    Weather information for sai buri, th
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=sai+buri%2C+th
    Weather information for constitucion, mx
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=constitucion%2C+mx
    Weather information for maamba, zm
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=maamba%2C+zm
    Weather information for gazni, af
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=gazni%2C+af
    Weather information for warwick, au
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=warwick%2C+au
    Weather information for cirie, it
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=cirie%2C+it
    Weather information for puebloviejo, co
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=puebloviejo%2C+co
    Weather information for namwala, zm
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=namwala%2C+zm
    Weather information for karacakoy, tr
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=karacakoy%2C+tr
    Weather information for asahikawa, jp
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=asahikawa%2C+jp
    Weather information for morgan city, us
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=morgan+city%2C+us
    Weather information for maceio, br
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=maceio%2C+br
    Weather information for ebebiyin, gq
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=ebebiyin%2C+gq
    Weather information for burgersdorp, za
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=burgersdorp%2C+za
    Weather information for teya, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=teya%2C+ru
    Weather information for huanan, cn
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=huanan%2C+cn
    Weather information for nsanje, mw
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=nsanje%2C+mw
    Weather information for college, us
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=college%2C+us
    Weather information for ongandjera, na
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=ongandjera%2C+na
    Weather information for saldanha, za
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=saldanha%2C+za
    Weather information for southbridge, us
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=southbridge%2C+us
    Weather information for harper, lr
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=harper%2C+lr
    Weather information for batemans bay, au
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=batemans+bay%2C+au
    Weather information for kota bahru, my
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=kota+bahru%2C+my
    Weather information for longlac, ca
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=longlac%2C+ca
    Weather information for dharchula, in
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=dharchula%2C+in
    Weather information for shingu, jp
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=shingu%2C+jp
    Weather information for chute-aux-outardes, ca
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=chute-aux-outardes%2C+ca
    Weather information for gatesville, us
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=gatesville%2C+us
    Weather information for khilok, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=khilok%2C+ru
    Weather information for zhanakorgan, kz
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=zhanakorgan%2C+kz
    Weather information for kosonsoy, uz
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=kosonsoy%2C+uz
    Weather information for gisborne, nz
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=gisborne%2C+nz
    Weather information for teahupoo, pf
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=teahupoo%2C+pf
    Weather information for kiunga, pg
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=kiunga%2C+pg
    Weather information for huarmey, pe
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=huarmey%2C+pe
    Weather information for iqaluit, ca
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=iqaluit%2C+ca
    Weather information for kargasok, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=kargasok%2C+ru
    Weather information for labuhan, id
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=labuhan%2C+id
    Weather information for saint anthony, ca
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=saint+anthony%2C+ca
    Weather information for hibbing, us
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=hibbing%2C+us
    Weather information for artyk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=artyk%2C+ru
    Weather information for bajo baudo, co
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=bajo+baudo%2C+co
    Weather information for dujuma, so
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=dujuma%2C+so
    Weather information for kalabo, zm
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=kalabo%2C+zm
    Weather information for vao, nc
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=vao%2C+nc
    Weather information for damaturu, ng
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=damaturu%2C+ng
    Weather information for bandarbeyla, so
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=bandarbeyla%2C+so
    Weather information for kahuta, pk
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=kahuta%2C+pk
    Weather information for tateyama, jp
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=tateyama%2C+jp
    Weather information for tukrah, ly
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=tukrah%2C+ly
    Weather information for tabuleiro do norte, br
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=tabuleiro+do+norte%2C+br
    Weather information for guasdualito, ve
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=guasdualito%2C+ve
    Weather information for jawhar, so
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=jawhar%2C+so
    Weather information for tula, mx
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=tula%2C+mx
    Weather information for yashalta, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=yashalta%2C+ru
    Weather information for victoria, sc
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=victoria%2C+sc
    Weather information for sa kaeo, th
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=sa+kaeo%2C+th
    Weather information for cockburn town, tc
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=cockburn+town%2C+tc
    Weather information for rincon, an
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=rincon%2C+an
    Weather information for acajutla, sv
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=acajutla%2C+sv
    Weather information for magadan, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=magadan%2C+ru
    Weather information for russkaya polyana, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=russkaya+polyana%2C+ru
    Weather information for santa rosa, ar
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=santa+rosa%2C+ar
    Weather information for birao, cf
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=birao%2C+cf
    Weather information for cauquenes, cl
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=cauquenes%2C+cl
    Weather information for george, za
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=george%2C+za
    Weather information for busselton, au
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=busselton%2C+au
    Weather information for poronaysk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=poronaysk%2C+ru
    Weather information for paredon, mx
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=paredon%2C+mx
    Weather information for ayr, au
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=ayr%2C+au
    Weather information for mehamn, no
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=mehamn%2C+no
    Weather information for mys shmidta, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=mys+shmidta%2C+ru
    Weather information for dokka, no
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=dokka%2C+no
    Weather information for laureles, py
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=laureles%2C+py
    Weather information for aksarka, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=aksarka%2C+ru
    Weather information for emerald, au
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=emerald%2C+au
    Weather information for nisia floresta, br
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=nisia+floresta%2C+br
    Weather information for mananara, mg
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=mananara%2C+mg
    Weather information for bandar-e lengeh, ir
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=bandar-e+lengeh%2C+ir
    Weather information for sirsa, in
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=sirsa%2C+in
    Weather information for plouzane, fr
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=plouzane%2C+fr
    Weather information for anadyr, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=anadyr%2C+ru
    Weather information for jardim, br
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=jardim%2C+br
    Weather information for attert, be
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=attert%2C+be
    Weather information for kenora, ca
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=kenora%2C+ca
    Weather information for gull lake, ca
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=gull+lake%2C+ca
    Weather information for riohacha, co
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=riohacha%2C+co
    Weather information for uberlandia, br
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=uberlandia%2C+br
    Weather information for wakkanai, jp
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=wakkanai%2C+jp
    Weather information for bergheim, de
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=bergheim%2C+de
    Weather information for porto novo, cv
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=porto+novo%2C+cv
    Weather information for palmas, br
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=palmas%2C+br
    Weather information for san cristobal, ec
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=san+cristobal%2C+ec
    Weather information for dobryanka, ua
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=dobryanka%2C+ua
    Weather information for saquena, pe
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=saquena%2C+pe
    Weather information for listvyanskiy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=listvyanskiy%2C+ru
    Weather information for bereznik, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=bereznik%2C+ru
    Weather information for lalomanu, ws
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=lalomanu%2C+ws
    Weather information for bentiu, sd
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=bentiu%2C+sd
    Weather information for cabo san lucas, mx
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=cabo+san+lucas%2C+mx
    Weather information for labutta, mm
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=labutta%2C+mm
    Weather information for babu, cn
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=babu%2C+cn
    Weather information for vila franca do campo, pt
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=vila+franca+do+campo%2C+pt
    Weather information for ambilobe, mg
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=ambilobe%2C+mg
    Weather information for tkvarcheli, ge
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=tkvarcheli%2C+ge
    Weather information for knysna, za
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=knysna%2C+za
    Weather information for kinablangan, ph
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=kinablangan%2C+ph
    Weather information for ust-koksa, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=ust-koksa%2C+ru
    Weather information for kapit, my
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=kapit%2C+my
    Weather information for chimbote, pe
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=chimbote%2C+pe
    Weather information for heilbad heiligenstadt, de
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=heilbad+heiligenstadt%2C+de
    Weather information for ardakan, ir
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=ardakan%2C+ir
    Weather information for vila velha, br
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=vila+velha%2C+br
    Weather information for manono, cd
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=manono%2C+cd
    Weather information for papara, pf
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=papara%2C+pf
    Weather information for itoman, jp
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=itoman%2C+jp
    Weather information for ismailia, eg
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=ismailia%2C+eg
    Weather information for zyryanka, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=zyryanka%2C+ru
    Weather information for kurilsk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=kurilsk%2C+ru
    Weather information for ilulissat, gl
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=ilulissat%2C+gl
    Weather information for shahr-e kord, ir
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=shahr-e+kord%2C+ir
    Weather information for corovode, al
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=corovode%2C+al
    Weather information for sosnovka, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=sosnovka%2C+ru
    Weather information for vendome, fr
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=vendome%2C+fr
    Weather information for canutama, br
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=canutama%2C+br
    Weather information for san lorenzo, ar
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=san+lorenzo%2C+ar
    Weather information for san fernando, mx
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=san+fernando%2C+mx
    Weather information for punta arenas, cl
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=punta+arenas%2C+cl
    Weather information for sennoy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=sennoy%2C+ru
    Weather information for clyde river, ca
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=clyde+river%2C+ca
    Weather information for evensk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=evensk%2C+ru
    Weather information for palmer, us
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=palmer%2C+us
    Weather information for erzin, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=erzin%2C+ru
    Weather information for celestun, mx
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=celestun%2C+mx
    Weather information for san rafael, ar
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=san+rafael%2C+ar
    Weather information for wittmund, de
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=wittmund%2C+de
    Weather information for bac lieu, vn
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=bac+lieu%2C+vn
    Weather information for ambikapur, in
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=ambikapur%2C+in
    Weather information for wyszkow, pl
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=wyszkow%2C+pl
    Weather information for talnakh, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=talnakh%2C+ru
    Weather information for supaul, in
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=supaul%2C+in
    Weather information for bagdarin, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=bagdarin%2C+ru
    Weather information for kota, in
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=kota%2C+in
    Weather information for maniwaki, ca
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=maniwaki%2C+ca
    Weather information for porosozero, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=porosozero%2C+ru
    Weather information for eirunepe, br
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=eirunepe%2C+br
    Weather information for kamenskoye, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=kamenskoye%2C+ru
    Weather information for ranot, th
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=ranot%2C+th
    Weather information for chapais, ca
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=chapais%2C+ca
    Weather information for chicama, pe
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=chicama%2C+pe
    Weather information for sungai udang, my
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=sungai+udang%2C+my
    Weather information for maldonado, uy
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=maldonado%2C+uy
    Weather information for zlatoustovsk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=zlatoustovsk%2C+ru
    Weather information for ostroleka, pl
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=ostroleka%2C+pl
    Weather information for cherskiy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=cherskiy%2C+ru
    Weather information for nohar, in
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=nohar%2C+in
    Weather information for kalemie, cd
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=kalemie%2C+cd
    Weather information for salta, ar
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=salta%2C+ar
    Weather information for hanzhong, cn
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=hanzhong%2C+cn
    Weather information for zima, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=zima%2C+ru
    Weather information for sao miguel do araguaia, br
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=sao+miguel+do+araguaia%2C+br
    Weather information for saint-augustin, ca
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=saint-augustin%2C+ca
    Weather information for bardiyah, ly
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=bardiyah%2C+ly
    Weather information for tautira, pf
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=tautira%2C+pf
    Weather information for janakpur road, in
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=janakpur+road%2C+in
    Weather information for hollola, fi
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=hollola%2C+fi
    Weather information for bira, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=bira%2C+ru
    Weather information for saint-michel-des-saints, ca
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=saint-michel-des-saints%2C+ca
    Weather information for mitsamiouli, km
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=mitsamiouli%2C+km
    Weather information for thano bula khan, pk
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=thano+bula+khan%2C+pk
    Weather information for half moon bay, us
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=half+moon+bay%2C+us
    Weather information for la ronge, ca
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=la+ronge%2C+ca
    Weather information for eureka, us
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=eureka%2C+us
    Weather information for correntina, br
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=correntina%2C+br
    Weather information for fort frances, ca
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=fort+frances%2C+ca
    Weather information for puerto baquerizo moreno, ec
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=puerto+baquerizo+moreno%2C+ec
    Weather information for sistranda, no
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=sistranda%2C+no
    Weather information for canmore, ca
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=canmore%2C+ca
    Weather information for belem de sao francisco, br
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=belem+de+sao+francisco%2C+br
    Weather information for kachikau, bw
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=kachikau%2C+bw
    Weather information for bolungarvik, is
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=bolungarvik%2C+is
    Weather information for xushan, cn
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=xushan%2C+cn
    Weather information for mapiripan, co
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=mapiripan%2C+co
    Weather information for berezanka, ua
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=berezanka%2C+ua
    Weather information for pala, td
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=pala%2C+td
    Weather information for bela palanka, rs
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=bela+palanka%2C+rs
    Weather information for el retorno, co
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=el+retorno%2C+co
    Weather information for shetpe, kz
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=shetpe%2C+kz
    Weather information for safwah, sa
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=safwah%2C+sa
    Weather information for san jose, gt
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=san+jose%2C+gt
    Weather information for mufumbwe, zm
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=mufumbwe%2C+zm
    Weather information for vreed en hoop, gy
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=vreed+en+hoop%2C+gy
    Weather information for beohari, in
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=beohari%2C+in
    Weather information for marabba, sd
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=marabba%2C+sd
    Weather information for inta, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=inta%2C+ru
    Weather information for padang, id
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=padang%2C+id
    Weather information for chernyshevskiy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=chernyshevskiy%2C+ru
    Weather information for taitung, tw
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=taitung%2C+tw
    Weather information for butaritari, ki
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=butaritari%2C+ki
    Weather information for muzhi, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=muzhi%2C+ru
    Weather information for piacenza, it
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=piacenza%2C+it
    Weather information for sao gabriel da cachoeira, br
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=sao+gabriel+da+cachoeira%2C+br
    Weather information for odweyne, so
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=odweyne%2C+so
    Weather information for alihe, cn
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=alihe%2C+cn
    Weather information for hasaki, jp
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=hasaki%2C+jp
    Weather information for piacabucu, br
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=piacabucu%2C+br
    Weather information for broken hill, au
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=broken+hill%2C+au
    Weather information for ponta do sol, cv
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=ponta+do+sol%2C+cv
    Weather information for kjollefjord, no
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=kjollefjord%2C+no
    Weather information for miandrivazo, mg
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=miandrivazo%2C+mg
    Weather information for umzimvubu, za
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=umzimvubu%2C+za
    Weather information for ambovombe, mg
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=ambovombe%2C+mg
    Weather information for wasilla, us
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=wasilla%2C+us
    Weather information for cheremukhovo, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=cheremukhovo%2C+ru
    Weather information for innisfail, au
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=innisfail%2C+au
    Weather information for santa margherita ligure, it
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=santa+margherita+ligure%2C+it
    Weather information for bredasdorp, za
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=bredasdorp%2C+za
    Weather information for new norfolk, au
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=new+norfolk%2C+au
    Weather information for kaputa, zm
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=kaputa%2C+zm
    Weather information for cuite, br
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=cuite%2C+br
    Weather information for pervomayskiy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=pervomayskiy%2C+ru
    Weather information for ingham, au
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=ingham%2C+au
    Weather information for leshukonskoye, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=leshukonskoye%2C+ru
    Weather information for tvoroyri, fo
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=tvoroyri%2C+fo
    Weather information for sola, vu
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=sola%2C+vu
    Weather information for krutikha, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=krutikha%2C+ru
    Weather information for chistogorskiy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=chistogorskiy%2C+ru
    Weather information for nemyriv, ua
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=nemyriv%2C+ua
    Weather information for panaba, mx
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=panaba%2C+mx
    Weather information for ipixuna, br
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=ipixuna%2C+br
    Weather information for jacareacanga, br
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=jacareacanga%2C+br
    Weather information for adre, td
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=adre%2C+td
    Weather information for taywarah, af
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=taywarah%2C+af
    Weather information for nikolayevka, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=nikolayevka%2C+ru
    Weather information for nchelenge, zm
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=nchelenge%2C+zm
    Weather information for robertson, za
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=robertson%2C+za
    Weather information for great yarmouth, gb
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=great+yarmouth%2C+gb
    Weather information for bathsheba, bb
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=bathsheba%2C+bb
    Weather information for holoby, ua
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=holoby%2C+ua
    Weather information for vega de alatorre, mx
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=vega+de+alatorre%2C+mx
    Weather information for namtsy, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=namtsy%2C+ru
    Weather information for el tarra, co
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=el+tarra%2C+co
    Weather information for poltavka, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=poltavka%2C+ru
    Weather information for paris, us
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=paris%2C+us
    Weather information for karaul, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=karaul%2C+ru
    Weather information for bagotville, ca
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=bagotville%2C+ca
    Weather information for camflora, ph
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=camflora%2C+ph
    Weather information for gasa, bt
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=gasa%2C+bt
    Weather information for encheng, cn
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=encheng%2C+cn
    Weather information for urusha, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=urusha%2C+ru
    Weather information for tual, id
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=tual%2C+id
    Weather information for meadow lake, ca
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=meadow+lake%2C+ca
    Weather information for grafing, de
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=grafing%2C+de
    Weather information for barahan, ph
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=barahan%2C+ph
    Weather information for khonuu, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=khonuu%2C+ru
    Weather information for daxian, cn
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=daxian%2C+cn
    Weather information for santo antonio do sudoeste, br
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=santo+antonio+do+sudoeste%2C+br
    Weather information for satitoa, ws
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=satitoa%2C+ws
    Weather information for rundu, na
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=rundu%2C+na
    Weather information for lleida, es
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=lleida%2C+es
    Weather information for tammisaari, fi
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=tammisaari%2C+fi
    Weather information for suba, ph
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=suba%2C+ph
    Weather information for rylsk, ru
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=rylsk%2C+ru
    Weather information for hithadhoo, mv
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=hithadhoo%2C+mv
    Weather information for trakai, lt
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=trakai%2C+lt
    Weather information for terrace bay, ca
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=terrace+bay%2C+ca
    Weather information for ouadda, cf
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=ouadda%2C+cf
    Weather information for mabaruma, gy
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=mabaruma%2C+gy
    Weather information for olden, no
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=olden%2C+no
    Weather information for russell, nz
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=russell%2C+nz
    Weather information for moyobamba, pe
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=moyobamba%2C+pe
    Weather information for katsuura, jp
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=katsuura%2C+jp
    Weather information for arbelaez, co
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=arbelaez%2C+co
    Weather information for port lincoln, au
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=port+lincoln%2C+au
    Weather information for suez, eg
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=suez%2C+eg
    Weather information for kwekwe, zw
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=kwekwe%2C+zw
    Weather information for amargosa, br
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=amargosa%2C+br
    Weather information for hay river, ca
    http://api.openweathermap.org/data/2.5/weather?appid=2ec88724f3fad131ccffe1dfb2efab45&units=metric&q=hay+river%2C+ca



```python
# Display dataframe with all info
random_cities_df

# Remove cities with no data available
new_random_cities_df = random_cities_df.dropna()
new_random_cities_df.head()
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
      <th>City</th>
      <th>Country</th>
      <th>Latitude</th>
      <th>Longitude</th>
      <th>Temperature</th>
      <th>Wind Speed</th>
      <th>Humidity</th>
      <th>Cloudiness</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>kyra</td>
      <td>ru</td>
      <td>49.58</td>
      <td>111.98</td>
      <td>-14.68</td>
      <td>4.21</td>
      <td>44.0</td>
      <td>20.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>inverell</td>
      <td>au</td>
      <td>-29.78</td>
      <td>151.12</td>
      <td>18.48</td>
      <td>1.86</td>
      <td>83.0</td>
      <td>56.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>glens falls</td>
      <td>us</td>
      <td>43.31</td>
      <td>-73.64</td>
      <td>-3.00</td>
      <td>3.10</td>
      <td>85.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>elko</td>
      <td>us</td>
      <td>40.83</td>
      <td>-115.76</td>
      <td>-3.00</td>
      <td>1.16</td>
      <td>73.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>tarata</td>
      <td>pe</td>
      <td>-17.47</td>
      <td>-70.03</td>
      <td>3.25</td>
      <td>1.11</td>
      <td>79.0</td>
      <td>20.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Save dataframe to csv file
new_random_cities_df.to_csv("WeatherInfoByWorldCities.csv")
```


```python
# Plot Temperature (F) vs. Latitude
temp_vs_lat = new_random_cities_df.plot(kind="scatter",
                                        x="Latitude",
                                        y="Temperature",
                                        color="blue",
                                        edgecolor="black",
                                        grid=True)
plt.title("City Latitude vs Max Temperature")
plt.xlabel("Latitude")
plt.ylabel("Max temperature (F)")
plt.savefig("LatVsTemp.png")
plt.show()
```


![png](output_7_0.png)



```python
# Plot Humidity (%) vs. Latitude
humid_vs_lat = new_random_cities_df.plot(kind="scatter",
                                         x="Latitude",
                                         y="Humidity",
                                         color="blue",
                                         edgecolor="black",
                                         grid=True)
plt.title("City Latitude vs Humidity")
plt.xlabel("Latitude")
plt.ylabel("Humidity (%)")
plt.savefig("LatVsHumidity.png")
plt.show()
```


![png](output_8_0.png)



```python
# Plot Cloudiness (%) vs. Latitude
cloud_vs_lat = new_random_cities_df.plot(kind="scatter",
                                         x="Latitude",
                                         y="Cloudiness",
                                         color="blue",
                                         edgecolor="black",
                                         grid=True)
plt.title("City Latitude vs Cloudiness")
plt.xlabel("Latitude")
plt.ylabel("Cloudiness (%)")
plt.ylim(-20, 120)
plt.savefig("LatVsCloudiness.png")
plt.show()
```


![png](output_9_0.png)



```python
# Plot Wind Speed (mph) vs. Latitude
windspeed_vs_lat = new_random_cities_df.plot(kind="scatter",
                                         x="Latitude",
                                         y="Wind Speed",
                                         color="blue",
                                         edgecolor="black",
                                         grid=True)
plt.title("City Latitude vs Wind Speed")
plt.xlabel("Latitude")
plt.ylabel("Wind Speed (mph)")
plt.ylim(-2, 14)
plt.savefig("LatVsWindSpeed.png")
plt.show()
```


![png](output_10_0.png)


Analysis
1. On the first graph (City Latitude vs Max Temperature), the latitude at 0 is equivalent to the equator. We can see that at 0, the max temperatures are at the highest. Anything going further from 0, the temperatures begin decreasing.
2. On the other graphs, it doesn't seem like there is any correlation between latitude and the other chosen variables. Cloudiness, humidity, and wind speed varies across all latitudes.
3. The only variable that is dependent on latitude is temperature.
