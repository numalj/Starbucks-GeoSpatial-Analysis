# Starbucks Ideal Store location - GeoSpatial Analysis

In this project I used **geoPandas** and **geoDataFrames** along with **joins** and **buffers** to figure out the best Starbucks locations in the California area to upgrade to Reserve Roastery.

Data: A combination of Location and Demographic data was used in the analysis.

The full jupyter notebook can be viewed [here](https://github.com/numalj/Starbucks-GeoSpatial-Analysis/blob/master/StarbucksRoasteryLocation.ipynb)

# Introduction

Starbucks are looking to find the next store to turn into a Starbucks Reserve Roastery. These roasteries are much larger than a typical Starbucks store and have several additional features, including various food and wine options, along with upscale lounge areas. You'll investigate the demographics of various counties in the state of California, to determine potentially suitable locations.

# Data Cleaning

5 store locations were identified with missing Longitude/Latitude coordinates. These were filled in by doing a geocode lookup using the address. The **OpenStreetMap Nominatim geocoder** was used.

```
def get_geocode(address):
    point = geocode(address, provider='nominatim').geometry[0]
    return pd.Series({'Longitude': point.x, 'Latitude': point.y})
```

### Map all Starbucks locations in the California Area
Just to get an idea of the distribution of locations, I've mapped them below. There are a total of 2821 locations.
![AllLocations](/images/m2.png)

## Identify Promising Counties
Promising counties were identified based on the following criteria:
* High median income (median income > $100,000)
* Low median age (median age < 38.5 years)
* Population Density > 285 people/sqkm

The population density was calculated by dividing the population by the area in square km of the county. 

The identified most suitable counties out of the 58 counties were: 
![selectedCounties](/images/sel_counties.png)

## Identify promising stores

Next the stores that fall within these counties were identified.
<br>
To do this the **.sjoin()** method was used with a 'within' operation 
Example of locations that fall within the Los Angeles County

![Locations in Los Angeles](/images/m2.png)

## Narrow down based on nearby Locations

Since there are a lot of possible stores we want to narrow things down. When upgrading to a Reserve Roastery, we want to avoid having regular starbucks locations nearby. This criteria can be used to narrow down our choices.

To do this we create a buffer around each store location and see how many stores fall within this buffer.

### Change CRS
Had to change the CRS for this. The folium maps use epsg=4326, but for distance measuring in meters used epsg=3857

#### Subset of buffers
![Subset of Buffers](/images/m3.png)

By avoiding locations that had no neighbours and more than 3 locations in a 1km radius, I narrowed down the list

## Conclusion
Further analysis can be done to pick ideal locations, for example rank locations based on population density and income.

Based on the above analysis I have identified a list of store locations that are in regions of

* High median income (median income > $100,000)
* Low median age (median age < 38.5 years)
* Population Density > 285 people/sqkm
* Locations that only had more than 1 but less than 3 other Starbucks in the vicinity of 2km
