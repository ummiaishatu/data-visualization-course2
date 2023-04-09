---
follows: coursework
---

Put your 2nd followup design concept here

{(aim|}
1- we can learn  where the authors in the dataset are locoated in the geomap. This map consist of the authors that are located in only greece. 2- we can also learn the rule chart of these authors based on their currentVersion. 3- finally, the bar chart explains where every creator of the all the authors located in spain.  
{|aim)}

{(vistype|}
1- geo map 2-bar chart 3-rule Chart
{|vistype)}
```python{v hide}
#country -- greece

import pandas as pandas
import numpy as numpy
import altair as alt

places = pandas.read_csv(r'pleiades-places-latest.csv')

lat_long_boundary = numpy.array([20.17,36.02,26.0,44.9])
places = places[(places.reprLat >= lat_long_boundary[1]) & 
                   (places.reprLat <= lat_long_boundary[3]) &
                   (places.reprLong >= lat_long_boundary[0]) &
                   (places.reprLong <= lat_long_boundary[2])]

places.dropna(inplace=True)
places.count()

rule_chart = alt.Chart(places).mark_rule().encode(
    x='reprLong:Q',
    y='reprLat:Q',
    color='currentVersion:N',
    #tooltip=['creators:N', 'description:N']
).interactive()

#to view tooltip you have to seperate the two maps. 
url_topojson =  'https://raw.githubusercontent.com/jhjanicki/Ukraine/master/data/greece.topojson'
topojson_remote = alt.topo_feature(url=url_topojson, feature='greecetopo')


base = alt.Chart(topojson_remote).mark_geoshape(
    fill='gray',
    stroke='white'
).encode(
).properties( 
   width=800, height=500
)
places.dropna(inplace=True)
points = alt.Chart(places).mark_square(size=100).encode(
    alt.Latitide('reprLat:Q'),
    alt.Longitude('reprLong:Q'),
    color='authors:N',
    tooltip=['creators:N', 'description:N']
)


geo = base + points

alt.vconcat(geo,rule_chart)


#second visualization
spec = alt.Chart(places).mark_bar().encode(
    x=alt.X('reprLat:Q',bin=True),
    y = alt.Y('reprLong:Q',bin=True),
    color='creator:N',
    tooltip=['creators:N', 'description:N']
    
).interactive().to_dict() 

alt.Chart.from_dict(spec).encode(color='authors:N')
```
![](visualization-4.png)
![](visualization-3.png)

{(vismapping|}
position(repLat,repLong) (interactive , tooltip color- timePeriods / authors, to_dict())
{|vismapping)}

{(dataprep|}
none
{|dataprep)}

{(limitations|}
limitations- it only specifies color and does not have cool visualization effects, improvements- adding more visualtions types such as treemaps 
{|limitations)}
