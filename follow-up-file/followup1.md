---
follows: coursework
---

Put your initial followup design concept here

## Making maps

For this, the [map tutorials](https://github.com/gicentre/litvis/blob/main/documents/tutorials/geoTutorials/) are very helpful. The idea here is to show settlements as points on a map.

### Map itself

To get map background, you need a shape file which contains the different
areas you want to draw on screen. In this case we're looking for a world map
so the different "shapes" are different contries. If you look at the json
file, you'll have a shape for each contry which consists of a set of points
describing the outline of each country. Note that I'm "stealing" the map data
from the [vega site](https://vega.github.io).

```elm {l}
mapData = dataFromUrl "https://vega.github.io/vega/data/world-110m.json"
                      [ topojsonFeature "countries" ]
```

Then we need to create a vega-lite layer to show the geographic data.
`maFill` is a function to set the fill of marks to a constant.

```elm {l}
mapSpec : Spec
mapSpec =
  asSpec
    [ mapData, geoshape [ maFill "#4c566a" ] ]
```

### Settlement positions

There are a number of feature types in the dataset so we need to set up a
vega lite filter to only show those types. Think of this operation by itself
as a kind of function that runs over the dataset.

```elm {l}
settlementTrans =
  transform
    << filter (fiExpr "datum.featureType == 'settlement'")
```

Then we can bring in the settlements which you can put on the map by basically making
a scatterplot but using the latitude and longitude.

```elm {l}
settlementEnc =
  encoding
    << position Latitude [ pName "reprLat" ]
    << position Longitude  [ pName "reprLong" ]
```

And then we can put it all together. Ideally this map should zoom or at least
cut off north and south america and antarctica but that's left as an exercise
to the reader :)

{(aim|}
1- we can learn  where the authors in the dataset are locoated in the geomap. This map consist of the authors that are located in only germany. 2- we can also learn an opacity area Chart based on the authors. 3- finally, the ordering timeperiods of each author on a bar chart . 
{|aim)}

{(vistype|}
1- geo map 2- opacity area chart 3- ordering marks chart 
{|vistype)}

```elm {v}
map : Spec
map =
  let
    settlementSpec =
      asSpec
        [ locs
        , settlementTrans []
        , settlementEnc []
        , point [ maStroke "", maFill "#a3be8c", maSize 2.9 ]
        ]
  in
  toVegaLite [width 700, height 500, layer [ mapSpec, settlementSpec ] ]
```

```python{v hide}
#country -- germany

import altair as alt
from vega_datasets import data
import numpy as numpy
import pandas as pandas

locations = pandas.read_csv(r'pleiades-locations-latest.csv')

lat_long_boundary = numpy.array([5.9,51.57,12.10,56.12])
locations = locations[(locations.reprLat >= lat_long_boundary[1]) & 
                   (locations.reprLat <= lat_long_boundary[3]) &
                   (locations.reprLong >= lat_long_boundary[0]) &
                   (locations.reprLong <= lat_long_boundary[2])]

locations.count()
locations.head()

#to view the tooltip for the geomap you have to separate it from the remaininf maps 
url_topojson = 'https://raw.githubusercontent.com/deldersveld/topojson/master/countries/germany/germany-regions.json'
topojson_remote = alt.topo_feature(url=url_topojson, feature='DEU_adm2')

base = alt.Chart(topojson_remote).mark_geoshape(
    fill='gray',
    stroke='white'
).encode(
).properties( 
   width=800, height=500
)

points = alt.Chart(locations).mark_square(size=100).encode(
    alt.Latitude('reprLat:Q'),
    alt.Longitude('reprLong:Q'),
    color='authors',
    tooltip=['creators', 'description','featureType']
)

legend = alt.Chart(locations).mark_geoshape(
    filled=False,
    strokeWidth=2
).encode(
    alt.Color(
        'id:N',
        legend=alt.Legend(
            title=None,
            orient='bottom-right',
            offset=0
        )
    )
)

opacity_area_chart = alt.Chart(locations).mark_area(
    opacity=0.3,
).encode(
    alt.X('reprLat:Q', bin=alt.Bin(maxbins=100)),
    alt.Y('reprLong:Q', stack=None),
    alt.Color('authors:N'),
    tooltip=['creators', 'description','featureType']
).interactive()

ordering_marks = alt.Chart(locations).mark_bar().encode(
    alt.X('reprLat:N', bin=alt.Bin(maxbins=100)),
    alt.Y('reprLong:Q', stack=None),
    alt.Color('timePeriods:N'),
    order = alt.Order("timePeriods", sort="ascending"),
    tooltip=['creators', 'description','featureType', 'timePeriods']
).interactive()

final = base + points + legend


alt.vconcat(final, opacity_area_chart , ordering_marks)
```
![](visualization-5.png)

{(vismapping|}
position(repLat, repLong), (interactive, ordering in ascending order , tooltip color- timePeriods / authors)
{|vismapping)}

{(dataprep|}
none
{|dataprep)}

{(limitations|}
limitations- it only specifies color and does not have cool visualization effects, improvements- adding more visualtions types such as treemaps 
{|limitations)}
