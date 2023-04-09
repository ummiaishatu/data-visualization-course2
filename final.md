---
follows: followup1
---

Put your final design and discussion here

{(aim|}
1- we can learn  where the authors in the dataset are locoated in the geomap. This map consist of the authors that are located in only spain. 2- we can also learn the histogram of these authors based on their title. 3- finally, the bubble chart explains where every version of the all the authors located in spain.  
{|aim)}

{(vistype|}
1- geographic map 2- histogram 3- bubble chart
{|vistype)}

```python{v hide}
import altair as alt
from vega_datasets import data
import numpy as numpy
import pandas as pandas

places = pandas.read_csv(r'pleiades-places-latest.csv')
lat_long_boundary = numpy.array([-7.25,30.38,2.10,60.12])
places = places[(places.reprLat >= lat_long_boundary[1]) & 
                   (places.reprLat <= lat_long_boundary[3]) &
                   (places.reprLong >= lat_long_boundary[0]) &
                   (places.reprLong <= lat_long_boundary[2])]

places.count()

places.dropna(inplace=True)

places.head()

#to view the tooltip on the geomap you have to separate it from the rest of the other maps 
url_topojson = 'https://raw.githubusercontent.com/deldersveld/topojson/master/countries/spain/spain-province.json'
topojson_remote = alt.topo_feature(url=url_topojson, feature='ESP_adm2')

base = alt.Chart(topojson_remote).mark_geoshape(
    fill='gray',
    stroke='white'
).encode(
).properties( 
   width=800, height=500
)

points = alt.Chart(places).mark_circle(size=90).encode(
    x='reprLat :Q',
    y='reprLong:Q',
    color='authors:N',
    tooltip=['creators:N', 'description:N','featureType:N']
)
histogram = alt.Chart(places).mark_bar().encode(
    alt.X('reprLat:Q', bin=True),
    y='reprLong:Q',
    color='title:N',
    tooltip=['creators:N', 'description:N','featureType:N','title:N']
).interactive()

bubble_chart = alt.Chart(places).mark_circle().encode(
    alt.X('reprLat:Q', bin=True),
    alt.Y('reprLong:Q', bin=True),
    size='count()',
    color='title:N',
    tooltip=['creators:N', 'description:N','featureType:N','title:N']
).interactive()


final = base + points 

alt.vconcat(final, histogram, bubble_chart)
```

![](visualization-7.png)


image/code here

{(vismapping|}
position(reprLong, reprLat) color-(histogram & bubble chart - title), geomap - authors, tooltips, interactive
{|vismapping)}

{(dataprep|}
None
{|dataprep)}

{(limitations|}
limitations- it only specifies color and does not have cool visualization effects, improvements- adding more visualtions types such as treemaps 
{|limitations)}
