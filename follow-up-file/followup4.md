---
follows: coursework
---

Put your 3rd design concept here

{(aim|}
1- we can learn  where the authors in the dataset are locoated in the geomap. This map consist of the authors that are located in only italy. 2- we can also learn the bar chart of these authors based on their creators.
{|aim)}

{(vistype|}
1- geo map 2- barchat
{|vistype)}
```python{v hide}
#country -- italy
import pandas as pandas

locations = pandas.read_csv(r'pleiades-locations-latest.csv')
names = pandas.read_csv(r'pleiades-names-latest.csv')
places = pandas.read_csv(r'pleiades-places-latest.csv')

import numpy as numpy

lat_long_boundary = numpy.array([9.77,30.3,12.14,45.4])
locations = locations[(locations.reprLat >= lat_long_boundary[1]) & 
                   (locations.reprLat <= lat_long_boundary[3]) &
                   (locations.reprLong >= lat_long_boundary[0]) &
                   (locations.reprLong <= lat_long_boundary[2])]

locations.count()
locations.head()

import altair as altair

#to view the tooltip in the geographic map you have to separate the two maps. 
url_topojson =  'https://raw.githubusercontent.com/deldersveld/topojson/master/countries/italy/italy-regions.json'
topojson_remote = altair.topo_feature(url=url_topojson, feature='ITA_adm1')


base = altair.Chart(topojson_remote).mark_geoshape(
    fill='gray',
    stroke='white'
    #alt.View(strokeWidth=0)
).encode(
).properties( 
   width=800, height=600
)
locations.dropna(inplace=True)
points = altair.Chart(locations).mark_circle(size=100).encode(
    altair.Latitude('reprLat:Q'),
    altair.Longitude('reprLong:Q'),
    color='authors',
    tooltip=['creators', 'description','featureType']
)

legend = altair.Chart(locations).mark_geoshape(
    filled=False,
    strokeWidth=2
).encode(
    altair.Color(
        'authors:N',
        legend=altair.Legend(
            title=None,
            orient='bottom-right',
            offset=0
        )
    )
)



locations.dropna(inplace=True)
barChart = altair.Chart(locations).mark_bar(size=100).encode(
    x='reprLat:Q',
    y='reprLong:Q',
    color='creators:N',
    column='creators:N'

).interactive()

final = base + points + legend
altair.vconcat(final , barChart)
```
![](visualization-2.png)

image/code here

{(vismapping|}
position(reprLong, reprLat) color-(authors, creators), tooltips, interactive, column, legend
{|vismapping)}

{(dataprep|}
none
{|dataprep)}

{(limitations|}
limitations- it only specifies color and does not have cool visualization effects, improvements- adding more visualtions types such as treemaps 
{|limitations)}