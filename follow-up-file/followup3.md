---
follows: coursework
---

Put your 3rd design concept here

{(aim|}
1- we can learn  where the authors in the dataset are locoated in the geomap. This map consist of the authors that are located in only UK. 2- we can also learn the negative chart to show which reprLatLong is negative or positive. 3- finally, the multi selection scatter plot, you can select the colors of any creator and the data of the author will be selected based on that color.
{|aim)}

{(vistype|}
1- geo map 2-negative chart 3- multi selction scatter plot 
{|vistype)}
```python{v hide}
#country -- United Kingdom
import pandas as pandas
places = pandas.read_csv(r'pleiades-places-latest.csv')

import numpy as numpy

lat_long_boundary = numpy.array([-5.87,40.1,5.76,60.11])
places = places[(places.reprLat >= lat_long_boundary[1]) & 
                   (places.reprLat <= lat_long_boundary[3]) &
                   (places.reprLong >= lat_long_boundary[0]) &
                   (places.reprLong <= lat_long_boundary[2])]


places.dropna(inplace=True)
places.count()
places.head()

import altair as altair

#to view the tooltip in the any of the map you have to separate them and run them. 
url_topojson = 'https://raw.githubusercontent.com/martinjc/UK-GeoJSON/master/json/administrative/gb/topo_lad.json'
topojson_remote = altair.topo_feature(url=url_topojson, feature='lad')


base = altair.Chart(topojson_remote).mark_geoshape(
    fill='gray',
    stroke='white'
    #alt.View(strokeWidth=0)
).encode(
).properties( 
   width=500, height=500
)
points = altair.Chart(places).mark_circle(size=100).encode(
    altair.Latitude('reprLat:Q'),
    altair.Longitude('reprLong:Q'),
    color='authors:N',
    tooltip=['authors:N','creators:N','description:N']
)

negative_chart = altair.Chart(places).mark_rule().encode(
    x=altair.Longitude('reprLat:Q'),
    y=altair.Latitude('reprLong:Q', axis=altair.Axis(labels=False, ticks=False, title=None)),
    color=altair.condition(
        altair.datum.nonfarm_change > 0,
        altair.value("steelblue"),
        altair.value("orange")
    )
).properties(
    width=600
).interactive()

multi = altair.selection_multi(on='mouseover')

multi_chart = altair.Chart(places).mark_square(size=100).encode(
    x='reprLat:Q',
    y='reprLong:Q',
    color=altair.condition(multi, 'created:N', altair.value('lighgray')),
    tooltip=['creators:N', 'description:N','featureType:N','created:N']

).add_selection(
    multi
).interactive()

final = base + points 
altair.vconcat(final, negative_chart, multi_chart)
```

![](visualization-6.png)
image/code here

{(vismapping|}
position(reprLat, reprLong) color-authors/created, tooltips, multiSelction, interactive, datum_nonfarm_change, labels, ticks, 
{|vismapping)}

{(dataprep|}
none
{|dataprep)}

{(limitations|}
limitations- it only specifies color and does not have cool visualization effects, improvements- adding more visualtions types such as treemaps 
{|limitations)}
