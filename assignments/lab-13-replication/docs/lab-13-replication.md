Lab-13 Replication
================
Branson Fox
(May 03, 2021)

## Introduction

This notebook replicates Lab-13 - Interactive Mapping of Structures at
Risk.

## Dependencies

This notebook requires the packages we used and discussed in lecture-05.

``` r
# spatial packages
library(leaflet)      # interactive maps
library(mapview)      # preview spatial data
```

    ## GDAL version >= 3.1.0 | setting mapviewOptions(fgb = TRUE)

``` r
library(sf)           # tools for spatial data
```

    ## Linking to GEOS 3.8.1, GDAL 3.1.4, PROJ 6.3.1

``` r
# other packages
library(here)         # file path management
```

    ## here() starts at /Users/chris/GitHub/slu-soc5650/content/module-5-leaflet/assignments/lab-13-replication

``` r
library(RColorBrewer) # color palettes
```

## Load Data

This notebook requires the data stored in `data/`. Remember that we use
`st_read()` to read shapefiles, and `here()` to specify where these
files are located.

``` r
mobileHomes <- st_read(here("data", "METRO_STRUCTURE_PctMobileHome", "METRO_STRUCTURE_PctMobileHome.shp"),
                       stringsAsFactors = FALSE)
```

    ## Reading layer `METRO_STRUCTURE_PctMobileHome' from data source `/Users/chris/GitHub/slu-soc5650/content/module-5-leaflet/assignments/lab-13-replication/data/METRO_STRUCTURE_PctMobileHome/METRO_STRUCTURE_PctMobileHome.shp' using driver `ESRI Shapefile'
    ## Simple feature collection with 12 features and 24 fields
    ## Geometry type: POLYGON
    ## Dimension:     XY
    ## Bounding box:  xmin: 637438.5 ymin: 4208959 xmax: 836287.1 ymax: 4349723
    ## Projected CRS: NAD83 / UTM zone 15N

## Part 1: Data Exploration

### Question 1

We use the `str()` function to list the variables in our data.

``` r
str(mobileHomes)
```

    ## Classes 'sf' and 'data.frame':   12 obs. of  25 variables:
    ##  $ STATEFP   : chr  "29" "29" "29" "29" ...
    ##  $ COUNTYFP  : chr  "189" "071" "183" "510" ...
    ##  $ COUNTYNS  : chr  "00758549" "00758490" "00758546" "00767557" ...
    ##  $ AFFGEOID  : chr  "0500000US29189" "0500000US29071" "0500000US29183" "0500000US29510" ...
    ##  $ GEOID     : chr  "29189" "29071" "29183" "29510" ...
    ##  $ NAME      : chr  "St. Louis" "Franklin" "St. Charles" "St. Louis City" ...
    ##  $ LSAD      : chr  "06" "06" "06" "25" ...
    ##  $ ALAND     : num  1.32e+09 2.39e+09 1.45e+09 1.60e+08 1.70e+09 ...
    ##  $ AWATER    : num  39385817 20729139 83314211 10670040 42081592 ...
    ##  $ OBJECTID_1: num  1147 1141 1151 1145 510 ...
    ##  $ STATE     : chr  "MO" "MO" "MO" "MO" ...
    ##  $ CWA       : chr  "LSX" "LSX" "LSX" "LSX" ...
    ##  $ COUNTYNAME: chr  "St. Louis" "Franklin" "St. Charles" "St. Louis City" ...
    ##  $ FIPS      : int  29189 29071 29183 29510 17163 17119 17027 17187 17133 17083 ...
    ##  $ TIME_ZONE : chr  "C" "C" "C" "C" ...
    ##  $ FE_AREA   : chr  "ec" "ec" "ec" "ec" ...
    ##  $ LON       : num  -90.4 -91.1 -90.5 -90.2 -90 ...
    ##  $ LAT       : num  38.6 38.5 38.8 38.7 38.4 ...
    ##  $ MobileHome: num  0.4 12.5 3.3 0.2 6 3.3 10.3 2.5 2.9 7.5 ...
    ##  $ ErrorMargi: num  0.1 1.2 0.3 0.1 0.4 0.3 1.4 1.2 1.1 1.6 ...
    ##  $ Shape_Leng: num  207713 222926 240603 64486 177900 ...
    ##  $ Shape_Area: num  1.35e+09 2.39e+09 1.50e+09 1.80e+08 1.71e+09 ...
    ##  $ Shape_Le_1: num  2.252 2.279 2.642 0.652 1.896 ...
    ##  $ Shape_Ar_1: num  0.1403 0.2486 0.1591 0.0177 0.1802 ...
    ##  $ geometry  :sfc_POLYGON of length 12; first list element: List of 1
    ##   ..$ : num [1:453, 1:2] 697472 697478 697473 697481 697468 ...
    ##   ..- attr(*, "class")= chr [1:3] "XY" "POLYGON" "sfg"
    ##  - attr(*, "sf_column")= chr "geometry"
    ##  - attr(*, "agr")= Factor w/ 3 levels "constant","aggregate",..: NA NA NA NA NA NA NA NA NA NA ...
    ##   ..- attr(*, "names")= chr [1:24] "STATEFP" "COUNTYFP" "COUNTYNS" "AFFGEOID" ...

Some of these variables may seem confusing, but the more you work with
spatial data, they become intuitive. For example `STATEFP` is the state
FIPS code. Missouri is 29 and Illinois is 17. The important variable in
these data is `MobileHome` which estimates the percent of homes that are
mobile homes.

### Question 2

Now, we will create an interactive preview of the data using the
`mapview()` function.

``` r
mapView(mobileHomes)
```

![](lab-13-replication_files/figure-gfm/p1-q2-1.png)<!-- -->

We now have an interactive preview of the data.

## Part 2: Interactive Mapping

### Question 3

Now, we will use leaflet to create an interactive map. But first, we
should use the following function to choose a basemap.

``` r
names(providers)
```

    ##   [1] "OpenStreetMap"                      
    ##   [2] "OpenStreetMap.Mapnik"               
    ##   [3] "OpenStreetMap.DE"                   
    ##   [4] "OpenStreetMap.CH"                   
    ##   [5] "OpenStreetMap.France"               
    ##   [6] "OpenStreetMap.HOT"                  
    ##   [7] "OpenStreetMap.BZH"                  
    ##   [8] "OpenSeaMap"                         
    ##   [9] "OpenPtMap"                          
    ##  [10] "OpenTopoMap"                        
    ##  [11] "OpenRailwayMap"                     
    ##  [12] "OpenFireMap"                        
    ##  [13] "SafeCast"                           
    ##  [14] "Thunderforest"                      
    ##  [15] "Thunderforest.OpenCycleMap"         
    ##  [16] "Thunderforest.Transport"            
    ##  [17] "Thunderforest.TransportDark"        
    ##  [18] "Thunderforest.SpinalMap"            
    ##  [19] "Thunderforest.Landscape"            
    ##  [20] "Thunderforest.Outdoors"             
    ##  [21] "Thunderforest.Pioneer"              
    ##  [22] "Thunderforest.MobileAtlas"          
    ##  [23] "Thunderforest.Neighbourhood"        
    ##  [24] "OpenMapSurfer"                      
    ##  [25] "OpenMapSurfer.Roads"                
    ##  [26] "OpenMapSurfer.Hybrid"               
    ##  [27] "OpenMapSurfer.AdminBounds"          
    ##  [28] "OpenMapSurfer.ContourLines"         
    ##  [29] "OpenMapSurfer.Hillshade"            
    ##  [30] "OpenMapSurfer.ElementsAtRisk"       
    ##  [31] "Hydda"                              
    ##  [32] "Hydda.Full"                         
    ##  [33] "Hydda.Base"                         
    ##  [34] "Hydda.RoadsAndLabels"               
    ##  [35] "MapBox"                             
    ##  [36] "Stamen"                             
    ##  [37] "Stamen.Toner"                       
    ##  [38] "Stamen.TonerBackground"             
    ##  [39] "Stamen.TonerHybrid"                 
    ##  [40] "Stamen.TonerLines"                  
    ##  [41] "Stamen.TonerLabels"                 
    ##  [42] "Stamen.TonerLite"                   
    ##  [43] "Stamen.Watercolor"                  
    ##  [44] "Stamen.Terrain"                     
    ##  [45] "Stamen.TerrainBackground"           
    ##  [46] "Stamen.TerrainLabels"               
    ##  [47] "Stamen.TopOSMRelief"                
    ##  [48] "Stamen.TopOSMFeatures"              
    ##  [49] "TomTom"                             
    ##  [50] "TomTom.Basic"                       
    ##  [51] "TomTom.Hybrid"                      
    ##  [52] "TomTom.Labels"                      
    ##  [53] "Esri"                               
    ##  [54] "Esri.WorldStreetMap"                
    ##  [55] "Esri.DeLorme"                       
    ##  [56] "Esri.WorldTopoMap"                  
    ##  [57] "Esri.WorldImagery"                  
    ##  [58] "Esri.WorldTerrain"                  
    ##  [59] "Esri.WorldShadedRelief"             
    ##  [60] "Esri.WorldPhysical"                 
    ##  [61] "Esri.OceanBasemap"                  
    ##  [62] "Esri.NatGeoWorldMap"                
    ##  [63] "Esri.WorldGrayCanvas"               
    ##  [64] "OpenWeatherMap"                     
    ##  [65] "OpenWeatherMap.Clouds"              
    ##  [66] "OpenWeatherMap.CloudsClassic"       
    ##  [67] "OpenWeatherMap.Precipitation"       
    ##  [68] "OpenWeatherMap.PrecipitationClassic"
    ##  [69] "OpenWeatherMap.Rain"                
    ##  [70] "OpenWeatherMap.RainClassic"         
    ##  [71] "OpenWeatherMap.Pressure"            
    ##  [72] "OpenWeatherMap.PressureContour"     
    ##  [73] "OpenWeatherMap.Wind"                
    ##  [74] "OpenWeatherMap.Temperature"         
    ##  [75] "OpenWeatherMap.Snow"                
    ##  [76] "HERE"                               
    ##  [77] "HERE.normalDay"                     
    ##  [78] "HERE.normalDayCustom"               
    ##  [79] "HERE.normalDayGrey"                 
    ##  [80] "HERE.normalDayMobile"               
    ##  [81] "HERE.normalDayGreyMobile"           
    ##  [82] "HERE.normalDayTransit"              
    ##  [83] "HERE.normalDayTransitMobile"        
    ##  [84] "HERE.normalDayTraffic"              
    ##  [85] "HERE.normalNight"                   
    ##  [86] "HERE.normalNightMobile"             
    ##  [87] "HERE.normalNightGrey"               
    ##  [88] "HERE.normalNightGreyMobile"         
    ##  [89] "HERE.normalNightTransit"            
    ##  [90] "HERE.normalNightTransitMobile"      
    ##  [91] "HERE.reducedDay"                    
    ##  [92] "HERE.reducedNight"                  
    ##  [93] "HERE.basicMap"                      
    ##  [94] "HERE.mapLabels"                     
    ##  [95] "HERE.trafficFlow"                   
    ##  [96] "HERE.carnavDayGrey"                 
    ##  [97] "HERE.hybridDay"                     
    ##  [98] "HERE.hybridDayMobile"               
    ##  [99] "HERE.hybridDayTransit"              
    ## [100] "HERE.hybridDayGrey"                 
    ## [101] "HERE.hybridDayTraffic"              
    ## [102] "HERE.pedestrianDay"                 
    ## [103] "HERE.pedestrianNight"               
    ## [104] "HERE.satelliteDay"                  
    ## [105] "HERE.terrainDay"                    
    ## [106] "HERE.terrainDayMobile"              
    ## [107] "FreeMapSK"                          
    ## [108] "MtbMap"                             
    ## [109] "CartoDB"                            
    ## [110] "CartoDB.Positron"                   
    ## [111] "CartoDB.PositronNoLabels"           
    ## [112] "CartoDB.PositronOnlyLabels"         
    ## [113] "CartoDB.DarkMatter"                 
    ## [114] "CartoDB.DarkMatterNoLabels"         
    ## [115] "CartoDB.DarkMatterOnlyLabels"       
    ## [116] "CartoDB.Voyager"                    
    ## [117] "CartoDB.VoyagerNoLabels"            
    ## [118] "CartoDB.VoyagerOnlyLabels"          
    ## [119] "CartoDB.VoyagerLabelsUnder"         
    ## [120] "HikeBike"                           
    ## [121] "HikeBike.HikeBike"                  
    ## [122] "HikeBike.HillShading"               
    ## [123] "BasemapAT"                          
    ## [124] "BasemapAT.basemap"                  
    ## [125] "BasemapAT.grau"                     
    ## [126] "BasemapAT.overlay"                  
    ## [127] "BasemapAT.highdpi"                  
    ## [128] "BasemapAT.orthofoto"                
    ## [129] "nlmaps"                             
    ## [130] "nlmaps.standaard"                   
    ## [131] "nlmaps.pastel"                      
    ## [132] "nlmaps.grijs"                       
    ## [133] "nlmaps.luchtfoto"                   
    ## [134] "NASAGIBS"                           
    ## [135] "NASAGIBS.ModisTerraTrueColorCR"     
    ## [136] "NASAGIBS.ModisTerraBands367CR"      
    ## [137] "NASAGIBS.ViirsEarthAtNight2012"     
    ## [138] "NASAGIBS.ModisTerraLSTDay"          
    ## [139] "NASAGIBS.ModisTerraSnowCover"       
    ## [140] "NASAGIBS.ModisTerraAOD"             
    ## [141] "NASAGIBS.ModisTerraChlorophyll"     
    ## [142] "NLS"                                
    ## [143] "JusticeMap"                         
    ## [144] "JusticeMap.income"                  
    ## [145] "JusticeMap.americanIndian"          
    ## [146] "JusticeMap.asian"                   
    ## [147] "JusticeMap.black"                   
    ## [148] "JusticeMap.hispanic"                
    ## [149] "JusticeMap.multi"                   
    ## [150] "JusticeMap.nonWhite"                
    ## [151] "JusticeMap.white"                   
    ## [152] "JusticeMap.plurality"               
    ## [153] "Wikimedia"                          
    ## [154] "GeoportailFrance"                   
    ## [155] "GeoportailFrance.parcels"           
    ## [156] "GeoportailFrance.ignMaps"           
    ## [157] "GeoportailFrance.maps"              
    ## [158] "GeoportailFrance.orthos"            
    ## [159] "OneMapSG"                           
    ## [160] "OneMapSG.Default"                   
    ## [161] "OneMapSG.Night"                     
    ## [162] "OneMapSG.Original"                  
    ## [163] "OneMapSG.Grey"                      
    ## [164] "OneMapSG.LandLot"

For the replication, I’ll use `Stamen.TonerLite`. If you would like to
preview the basemaps provided by leaflet, you can do so
[here](https://leaflet-extras.github.io/leaflet-providers/preview/).

### Question 4

Now, we will use leaflet to symbolize the percentage of mobile homes in
each county. We will discuss projections more in depth later, but for
now, we will just know that leaflet requires our data to be in
`WGS 1984`. To re-project our data, we use the `st_transform()`
function:

``` r
mobileHomes84 <- st_transform(mobileHomes, crs = 4326)
```

With our data are now re-projected, we’ll make our map:

``` r
# first, define a color ramp
ramp <- colorNumeric("YlGnBu", mobileHomes84$MobileHome)

# then, we will add it to our leaflet code
mobileHomes84 %>%
  leaflet() %>%
  addProviderTiles(providers$Stamen.TonerLite) %>%
  addPolygons(
    color = "#444444",
    weight = 1,
    opacity = 1.0,
    smoothFactor = 0.5,
    fillOpacity = 0.5,
    fillColor = ~ramp(MobileHome),
    highlightOptions = highlightOptions(color = "white", weight = 2, bringToFront = TRUE),
    popup = paste("<b>County:</b> ", mobileHomes84$NAME, "<br>",
                  "<b>Percentage of Mobile Homes:</b> ", mobileHomes84$MobileHome)) %>%
  addLegend(pal = ramp, values = ~MobileHome, opacity = .5, title = "Percentage of Mobile Homes")
```

![](lab-13-replication_files/figure-gfm/p2-q4-1.png)<!-- -->

## Appendix

If you were going to work through this iteratively, here are some
logical ways to start with a simple map and make it more complex. First,
create a simple interactive leaflet map:

``` r
mobileHomes84 %>%
  leaflet() %>%
  addProviderTiles(providers$Stamen.TonerLite) %>%
  addPolygons()
```

![](lab-13-replication_files/figure-gfm/p2-leaflet-1.png)<!-- -->

Now, we will add additional formatting to our leaflet code to symbolize
mobile homes:

``` r
# first, define a color ramp
ramp <- colorNumeric("YlGnBu", mobileHomes84$MobileHome)

# then, we will add it to our leaflet code
mobileHomes84 %>%
  leaflet() %>%
  addProviderTiles(providers$Stamen.TonerLite) %>%
  addPolygons(
    color = "#444444",
    weight = 1,
    opacity = 1.0,
    smoothFactor = 0.5,
    fillOpacity = 0.5,
    fillColor = ~ramp(MobileHome),
    highlightOptions = highlightOptions(color = "white", weight = 2, bringToFront = TRUE))
```

![](lab-13-replication_files/figure-gfm/p2-leaflet2-1.png)<!-- -->

Next, we will add pop-up labels:

``` r
# first, define a color ramp
ramp <- colorNumeric("YlGnBu", mobileHomes84$MobileHome)

# then, we will add it to our leaflet code
mobileHomes84 %>%
  leaflet() %>%
  addProviderTiles(providers$Stamen.TonerLite) %>%
  addPolygons(
    color = "#444444",
    weight = 1,
    opacity = 1.0,
    smoothFactor = 0.5,
    fillOpacity = 0.5,
    fillColor = ~ramp(MobileHome),
    highlightOptions = highlightOptions(color = "white", weight = 2, bringToFront = TRUE),
    popup = paste("<b>County:</b> ", mobileHomes84$NAME, "<br>",
                  "<b>Percentage of Mobile Homes:</b> ", mobileHomes84$MobileHome))
```

![](lab-13-replication_files/figure-gfm/p2-leaflet3-1.png)<!-- -->

And finally we will add a legend to complete our interactive map:

``` r
# first, define a color ramp
ramp <- colorNumeric("YlGnBu", mobileHomes84$MobileHome)

# then, we will add it to our leaflet code
mobileHomes84 %>%
  leaflet() %>%
  addProviderTiles(providers$Stamen.TonerLite) %>%
  addPolygons(
    color = "#444444",
    weight = 1,
    opacity = 1.0,
    smoothFactor = 0.5,
    fillOpacity = 0.5,
    fillColor = ~ramp(MobileHome),
    highlightOptions = highlightOptions(color = "white", weight = 2, bringToFront = TRUE),
    popup = paste("<b>County:</b> ", mobileHomes84$NAME, "<br>",
                  "<b>Percentage of Mobile Homes:</b> ", mobileHomes84$MobileHome)) %>%
  addLegend(pal = ramp, values = ~MobileHome, opacity = .5, title = "Percentage of Mobile Homes")
```

![](lab-13-replication_files/figure-gfm/leaflet4-1.png)<!-- -->
