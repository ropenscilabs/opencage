
<!-- README.md is generated from README.Rmd. Please edit that file -->

# opencage

[![CRAN\_Status\_Badge](http://www.r-pkg.org/badges/version/opencage)](http://cran.r-project.org/package=opencage)
[![Build
Status](https://travis-ci.org/ropensci/opencage.svg?branch=master)](https://travis-ci.org/ropensci/opencage)
[![AppVeyor Build
Status](https://ci.appveyor.com/api/projects/status/github/ropensci/opencage?branch=master&svg=true)](https://ci.appveyor.com/project/maelle/opencage)
[![codecov.io](https://codecov.io/github/ropensci/opencage/coverage.svg?branch=master)](https://codecov.io/github/ropensci/opencage?branch=master)
[![](https://badges.ropensci.org/36_status.svg)](https://github.com/ropensci/onboarding/issues/36)

Geocode with the OpenCage API, either from placename to longitude and
latitude (forward geocoding) or from longitude and latitude to placename
(reverse geocoding).

# Installation

Install the package with:

``` r
install.packages("opencage")
```

Or install the development version using
[remotes](https://remotes.r-lib.org/) with:

``` r
remotes::install_github("ropensci/opencage")
```

# Geocoding

The [OpenCage](https://opencagedata.com/) API supports forward and
reverse geocoding. Sources of OpenCage are open geospatial data
including OpenStreetMap, Yahoo\! GeoPlanet, Natural Earth Data, Thematic
Mapping, Ordnance Survey OpenSpace, Statistics New Zealand, Zillow,
MaxMind, GeoNames, the US Census Bureau and Flickr’s shapefiles plus a
whole lot more besides. Refer to [the full list of
credits](https://opencagedata.com/credits).

## API key

To use the package, you will need to register at
<https://opencagedata.com/users/sign_up> to get an API key. The “Free
Trial” plan provides up to 2,500 API requests a day.

The geocoding functions of the package will conveniently retrieve your
API key with `oc_key()` if it is saved in the environment variable
`"OPENCAGE_KEY"`. For ease of use, [save your API key as an environment
variable in
`.Renviron`](https://happygitwithr.com/github-pat.html#step-by-step).

## Forward geocoding

Forward geocoding is from placename to latitude and longitude tuple(s).

``` r
library("opencage")
output <- oc_forward(placename = "Sarzeau")
knitr::kable(output)
```

| confidence | formatted                                       | northeast\_lat | northeast\_lng | southwest\_lat | southwest\_lng | ISO\_3166\_1\_alpha\_2 | ISO\_3166\_1\_alpha\_3 | type         | city    | continent | country | country\_code | county | political\_union | postcode | state    | state\_district | post\_office | road            | suburb   | village |      lat |        lng |
| ---------: | :---------------------------------------------- | -------------: | -------------: | -------------: | -------------: | :--------------------- | :--------------------- | :----------- | :------ | :-------- | :------ | :------------ | :----- | :--------------- | :------- | :------- | :-------------- | :----------- | :-------------- | :------- | :------ | -------: | ---------: |
|          6 | 56370 Sarzeau, France                           |       47.56668 |     \-2.663120 |       47.48408 |     \-2.853616 | FR                     | FRA                    | city         | Sarzeau | Europe    | France  | fr            | Vannes | European Union   | 56370    | Brittany | Morbihan        | NA           | NA              | NA       | NA      | 47.52877 | \-2.764200 |
|          9 | Sarzeau, Rue de la Poste, 56370 Sarzeau, France |       47.52804 |     \-2.768803 |       47.52794 |     \-2.768903 | FR                     | FRA                    | post\_office | NA      | Europe    | France  | fr            | Vannes | European Union   | 56370    | Brittany | Morbihan        | Sarzeau      | Rue de la Poste | Kerjolis | Sarzeau | 47.52799 | \-2.768853 |

## Reverse geocoding

Reverse geocoding is from latitude and longitude to placename(s).

``` r
output2 <- oc_reverse(latitude = 51.5034070, longitude = -0.1275920)
knitr::kable(output2)
```

| confidence | formatted                                                                   | northeast\_lat | northeast\_lng | southwest\_lat | southwest\_lng | ISO\_3166\_1\_alpha\_2 | ISO\_3166\_1\_alpha\_3 | type       | attraction              | city   | continent | country        | country\_code | county      | county\_code | house\_number | neighbourhood | political\_union | postcode | road           | state   | state\_code | state\_district | suburb        |      lat |         lng |
| ---------: | :-------------------------------------------------------------------------- | -------------: | -------------: | -------------: | -------------: | :--------------------- | :--------------------- | :--------- | :---------------------- | :----- | :-------- | :------------- | :------------ | :---------- | :----------- | :------------ | :------------ | :--------------- | :------- | :------------- | :------ | :---------- | :-------------- | :------------ | -------: | ----------: |
|          9 | Prime Minister’s Office, 10 Downing Street, London SW1A 2AA, United Kingdom |       51.50365 |    \-0.1273038 |       51.50326 |    \-0.1278356 | GB                     | GBR                    | attraction | Prime Minister’s Office | London | Europe    | United Kingdom | gb            | Westminster | WSM          | 10            | St. James’s   | European Union   | SW1A 2AA | Downing Street | England | ENG         | Greater London  | Covent Garden | 51.50344 | \-0.1277082 |

Note that all coordinates sent to the OpenCage API must adhere to the
[WGS 84](https://en.wikipedia.org/wiki/World_Geodetic_System)
([EPSG:4326](http://epsg.io/4326)) [coordinate reference
system](https://en.wikipedia.org/wiki/Spatial_reference_system) in
decimal format. There is usually no reason to send more than six or
seven digits past the decimal as that then gets down to the [precision
of a centimetre](https://en.wikipedia.org/wiki/Decimal_degrees).

## Return type

Depending on what you specify as the `return` parameter, `oc_forward()`
and `oc_reverse()` will return either a list of tibbles (`df_list`, the
default), JSON lists (`json_list`), GeoJSON lists (`geojson_list`), or
the URL with which the API would be called (`url_only`).

Both `oc_forward_df()` and `oc_reverse_df()` return a single tibble.

## Multiple results

Forward geocoding typically returns multiple results. Regarding these
ambiguous results, the [OpenCage API
doc](https://opencagedata.com/api#ambiguous-results) states: “When
forward geocoding we may find multiple valid matches. Many places have
the same or similar names. In this case we return multiple results
\[[ranked by relevance](https://opencagedata.com/api#ranking)\]. The
`confidence`, coordinates, or \[`type` fields\] for each result should
be examined to determine whether each result from an ambiguous query is
sufficiently correct to warrant using a result or not. A good strategy
to reduce ambiguity is to use the optional `bounds`, `countrycode`,
and/or `proximity` parameters.” Multiple results might mean you get a
result for an airport and a road when querying a city name, or results
for cities with the same name in different countries.

When building queries, OpenCage’s [best
practices](https://opencagedata.com/api#bestpractices) can be very
useful.

## Specifying the target area

The `bounds`, `countrycode` and `proximity` arguments can make the query
more precise. They are only relevant and available for forward
geocoding.

  - `bounds`: The `bounds` parameter restricts the possible results to a
    defined bounding box. A bounding box is a named numeric vector with
    four coordinates specifiying its south-west and north-east corners:
    `(xmin, ymin, xmax, ymax)`. The bounds parameter can most easily be
    specified with the `oc_bbox()` helper, for example like `bounds =
    oc_bbox(-0.56, 51.28, 0.27, 51.68)`. OpenCage provides a
    ‘[bounds-finder](https://opencagedata.com/bounds-finder)’ to
    interactively determine bounds values.

Below is an example of the use of `bounds` where the rectangle given in
the second call does not include Europe so that we don’t get results for
Berlin in Germany.

``` r
results1 <- oc_forward(placename = "Berlin")
knitr::kable(results1)
```

| confidence | formatted                                    | northeast\_lat | northeast\_lng | southwest\_lat | southwest\_lng | ISO\_3166\_1\_alpha\_2 | ISO\_3166\_1\_alpha\_3 | type | city           | continent     | country | country\_code | political\_union | postcode | state         | state\_code | county            | town   |      lat |        lng |
| ---------: | :------------------------------------------- | -------------: | -------------: | -------------: | -------------: | :--------------------- | :--------------------- | :--- | :------------- | :------------ | :------ | :------------ | :--------------- | :------- | :------------ | :---------- | :---------------- | :----- | -------: | ---------: |
|          4 | 10117 Berlin, Germany                        |       52.67704 |       13.54886 |       52.35704 |       13.22886 | DE                     | DEU                    | city | Berlin         | Europe        | Germany | de            | European Union   | 10117    | Berlin        | BE          | NA                | NA     | 52.51704 |   13.38886 |
|          2 | Berlin, Germany                              |       52.67551 |       13.76116 |       52.33824 |       13.08835 | DE                     | DEU                    | city | Berlin         | Europe        | Germany | de            | European Union   | NA       | Berlin        | BE          | NA                | NA     | 52.50693 |   13.39748 |
|          5 | Berlin, NH 03570, United States of America   |       44.52844 |     \-71.12364 |       44.44506 |     \-71.39786 | US                     | USA                    | city | Berlin         | North America | USA     | us            | NA               | 03570    | New Hampshire | NH          | Coös County       | NA     | 44.46907 | \-71.18523 |
|          6 | Berlin, VT, United States of America         |       44.27197 |     \-72.51979 |       44.15478 |     \-72.68368 | US                     | USA                    | city | Berlin         | North America | USA     | us            | NA               | NA       | Vermont       | VT          | Washington County | NA     | 44.21008 | \-72.60340 |
|          7 | Berlin, CT 06037, United States of America   |       41.66149 |     \-72.70565 |       41.58149 |     \-72.78565 | US                     | USA                    | city | NA             | North America | USA     | us            | NA               | 06037    | Connecticut   | CT          | Hartford County   | Berlin | 41.62149 | \-72.74565 |
|          7 | Berlin, NJ, United States of America         |       39.80915 |     \-74.90796 |       39.77601 |     \-74.96611 | US                     | USA                    | city | Berlin         | North America | USA     | us            | NA               | NA       | New Jersey    | NJ          | Camden County     | NA     | 39.79123 | \-74.92905 |
|          7 | Berlin, MA 01503, United States of America   |       42.41828 |     \-71.58023 |       42.35059 |     \-71.67880 | US                     | USA                    | city | Berlin         | North America | USA     | us            | NA               | 01503    | Massachusetts | MA          | NA                | NA     | 42.38120 | \-71.63701 |
|          7 | Berlin, MD, United States of America         |       38.35518 |     \-75.18802 |       38.30841 |     \-75.23479 | US                     | USA                    | city | Berlin         | North America | USA     | us            | NA               | NA       | Maryland      | MD          | Worcester County  | NA     | 38.32262 | \-75.21769 |
|          7 | City of Berlin, WI, United States of America |       43.99797 |     \-88.92073 |       43.94761 |     \-88.98085 | US                     | USA                    | city | City of Berlin | North America | USA     | us            | NA               | NA       | Wisconsin     | WI          | Green Lake County | NA     | 43.96804 | \-88.94345 |
|          8 | Berlin, PA, United States of America         |       39.92738 |     \-78.93729 |       39.91442 |     \-78.96559 | US                     | USA                    | city | Berlin         | North America | USA     | us            | NA               | NA       | Pennsylvania  | PA          | Somerset County   | NA     | 39.92064 | \-78.95780 |

``` r
results2 <- oc_forward(placename = "Berlin", bounds = oc_bbox(-90, 38, 0, 45))
knitr::kable(results2)
```

| confidence | formatted                                    | northeast\_lat | northeast\_lng | southwest\_lat | southwest\_lng | ISO\_3166\_1\_alpha\_2 | ISO\_3166\_1\_alpha\_3 | type | city           | continent     | country | country\_code | county            | postcode | state         | state\_code | town   |      lat |        lng |
| ---------: | :------------------------------------------- | -------------: | -------------: | -------------: | -------------: | :--------------------- | :--------------------- | :--- | :------------- | :------------ | :------ | :------------ | :---------------- | :------- | :------------ | :---------- | :----- | -------: | ---------: |
|          5 | Berlin, NH 03570, United States of America   |       44.52844 |     \-71.12364 |       44.44506 |     \-71.39786 | US                     | USA                    | city | Berlin         | North America | USA     | us            | Coös County       | 03570    | New Hampshire | NH          | NA     | 44.46907 | \-71.18523 |
|          6 | Berlin, VT, United States of America         |       44.27197 |     \-72.51979 |       44.15478 |     \-72.68368 | US                     | USA                    | city | Berlin         | North America | USA     | us            | Washington County | NA       | Vermont       | VT          | NA     | 44.21008 | \-72.60340 |
|          7 | Berlin, CT 06037, United States of America   |       41.66149 |     \-72.70565 |       41.58149 |     \-72.78565 | US                     | USA                    | city | NA             | North America | USA     | us            | Hartford County   | 06037    | Connecticut   | CT          | Berlin | 41.62149 | \-72.74565 |
|          7 | Berlin, NJ, United States of America         |       39.80915 |     \-74.90796 |       39.77601 |     \-74.96611 | US                     | USA                    | city | Berlin         | North America | USA     | us            | Camden County     | NA       | New Jersey    | NJ          | NA     | 39.79123 | \-74.92905 |
|          7 | Berlin, MA 01503, United States of America   |       42.41828 |     \-71.58023 |       42.35059 |     \-71.67880 | US                     | USA                    | city | Berlin         | North America | USA     | us            | NA                | 01503    | Massachusetts | MA          | NA     | 42.38120 | \-71.63701 |
|          7 | Berlin, MD, United States of America         |       38.35518 |     \-75.18802 |       38.30841 |     \-75.23479 | US                     | USA                    | city | Berlin         | North America | USA     | us            | Worcester County  | NA       | Maryland      | MD          | NA     | 38.32262 | \-75.21769 |
|          7 | City of Berlin, WI, United States of America |       43.99797 |     \-88.92073 |       43.94761 |     \-88.98085 | US                     | USA                    | city | City of Berlin | North America | USA     | us            | Green Lake County | NA       | Wisconsin     | WI          | NA     | 43.96804 | \-88.94345 |
|          8 | Berlin, PA, United States of America         |       39.92738 |     \-78.93729 |       39.91442 |     \-78.96559 | US                     | USA                    | city | Berlin         | North America | USA     | us            | Somerset County   | NA       | Pennsylvania  | PA          | NA     | 39.92064 | \-78.95780 |

  - `countrycode`: The `countrycode` parameter restricts the results to
    the given country. The country code is a two letter code as defined
    by the [ISO 3166-1 Alpha 2](https://www.iso.org/obp/ui/#search/code)
    standard. E.g. “GB” for the United Kingdom, “FR” for France, “US”
    for United States. Multiple countrycodes per `placename` must be
    wrapped in a list. See example below.

<!-- end list -->

``` r
results3 <- oc_forward(placename = "Paris", countrycode = "US")
knitr::kable(results3)
```

| confidence | formatted                                                                      | northeast\_lat | northeast\_lng | southwest\_lat | southwest\_lng | ISO\_3166\_1\_alpha\_2 | ISO\_3166\_1\_alpha\_3 | type      | city  | continent     | country | country\_code | county         | postcode | state    | state\_code | bus\_stop | locality | road                      | road\_type | suburb        |      lat |         lng |
| ---------: | :----------------------------------------------------------------------------- | -------------: | -------------: | -------------: | -------------: | :--------------------- | :--------------------- | :-------- | :---- | :------------ | :------ | :------------ | :------------- | :------- | :------- | :---------- | :-------- | :------- | :------------------------ | :--------- | :------------ | -------: | ----------: |
|          7 | Paris, AR 72855, United States of America                                      |       35.30659 |     \-93.67552 |       35.26815 |     \-93.76197 | US                     | USA                    | city      | Paris | North America | USA     | us            | Logan County   | 72855    | Arkansas | AR          | NA        | NA       | NA                        | NA         | NA            | 35.29203 |  \-93.72992 |
|          5 | Paris, TX 75460, United States of America                                      |       33.73839 |     \-95.43541 |       33.62063 |     \-95.62794 | US                     | USA                    | city      | Paris | North America | USA     | us            | Lamar County   | 75460    | Texas    | TX          | NA        | NA       | NA                        | NA         | NA            | 33.66180 |  \-95.55551 |
|          7 | Paris, KY, United States of America                                            |       38.23827 |     \-84.23209 |       38.16492 |     \-84.30733 | US                     | USA                    | city      | Paris | North America | USA     | us            | Bourbon County | NA       | Kentucky | KY          | NA        | NA       | NA                        | NA         | NA            | 38.20980 |  \-84.25299 |
|          9 | Paris, South Las Vegas Boulevard, Paradise, NV 89158, United States of America |       36.11205 |    \-115.17261 |       36.11195 |    \-115.17271 | US                     | USA                    | bus\_stop | NA    | North America | USA     | us            | Clark County   | 89158    | Nevada   | NV          | Paris     | Paradise | South Las Vegas Boulevard | bus\_stop  | Hughes Center | 36.11200 | \-115.17266 |
|          7 | Paris, MO 65275, United States of America                                      |       39.48928 |     \-91.99168 |       39.46916 |     \-92.02148 | US                     | USA                    | city      | Paris | North America | USA     | us            | Monroe County  | 65275    | Missouri | MO          | NA        | NA       | NA                        | NA         | NA            | 39.48087 |  \-92.00128 |

``` r
results4 <- oc_forward(placename = c("Paris"), countrycode = list(c("CA", "US")))
knitr::kable(results4)
```

| confidence | formatted                                                                      | northeast\_lat | northeast\_lng | southwest\_lat | southwest\_lng | ISO\_3166\_1\_alpha\_2 | ISO\_3166\_1\_alpha\_3 | type      | city  | continent     | country | country\_code | county             | postcode | state    | state\_code | bus\_stop | locality | road                      | road\_type | suburb        | state\_district      | town  |      lat |         lng |
| ---------: | :----------------------------------------------------------------------------- | -------------: | -------------: | -------------: | -------------: | :--------------------- | :--------------------- | :-------- | :---- | :------------ | :------ | :------------ | :----------------- | :------- | :------- | :---------- | :-------- | :------- | :------------------------ | :--------- | :------------ | :------------------- | :---- | -------: | ----------: |
|          7 | Paris, AR 72855, United States of America                                      |       35.30659 |     \-93.67552 |       35.26815 |     \-93.76197 | US                     | USA                    | city      | Paris | North America | USA     | us            | Logan County       | 72855    | Arkansas | AR          | NA        | NA       | NA                        | NA         | NA            | NA                   | NA    | 35.29203 |  \-93.72992 |
|          5 | Paris, TX 75460, United States of America                                      |       33.73839 |     \-95.43541 |       33.62063 |     \-95.62794 | US                     | USA                    | city      | Paris | North America | USA     | us            | Lamar County       | 75460    | Texas    | TX          | NA        | NA       | NA                        | NA         | NA            | NA                   | NA    | 33.66180 |  \-95.55551 |
|          7 | Paris, KY, United States of America                                            |       38.23827 |     \-84.23209 |       38.16492 |     \-84.30733 | US                     | USA                    | city      | Paris | North America | USA     | us            | Bourbon County     | NA       | Kentucky | KY          | NA        | NA       | NA                        | NA         | NA            | NA                   | NA    | 38.20980 |  \-84.25299 |
|          9 | Paris, South Las Vegas Boulevard, Paradise, NV 89158, United States of America |       36.11205 |    \-115.17261 |       36.11195 |    \-115.17271 | US                     | USA                    | bus\_stop | NA    | North America | USA     | us            | Clark County       | 89158    | Nevada   | NV          | Paris     | Paradise | South Las Vegas Boulevard | bus\_stop  | Hughes Center | NA                   | NA    | 36.11200 | \-115.17266 |
|          7 | Paris, ON N3L 2M3, Canada                                                      |       43.23323 |     \-80.34428 |       43.15323 |     \-80.42428 | CA                     | CAN                    | city      | NA    | North America | Canada  | ca            | Brant County       | N3L 2M3  | Ontario  | ON          | NA        | NA       | NA                        | NA         | NA            | Southwestern Ontario | Paris | 43.19323 |  \-80.38428 |
|          8 | Paris, YT, Canada                                                              |       63.82042 |    \-138.62566 |       63.80519 |    \-138.67124 | CA                     | CAN                    | city      | Paris | North America | Canada  | ca            | Yukon, Unorganized | NA       | Yukon    | YT          | NA        | NA       | NA                        | NA         | NA            | NA                   | NA    | 63.81290 | \-138.64849 |
|          7 | Paris, MO 65275, United States of America                                      |       39.48928 |     \-91.99168 |       39.46916 |     \-92.02148 | US                     | USA                    | city      | Paris | North America | USA     | us            | Monroe County      | 65275    | Missouri | MO          | NA        | NA       | NA                        | NA         | NA            | NA                   | NA    | 39.48087 |  \-92.00128 |

  - `proximity`: The `proximity` parameter provides OpenCage with a hint
    to bias results in favour of those closer to the specified location.
    It is just one of many factors used for ranking results, however,
    and (some) results may be far away from the location or point passed
    to the `proximity` parameter. A point is a named numeric vector of a
    latitude, longitude coordinate pair in decimal format. The
    `proximity` parameter can most easily be specified with the
    `oc_points()` helper, for example like `proximity =
    oc_points(51.9526, 7.6324)`.

Below we provide a point near Lexington, Kentucky, USA. Note that the
French capital is ranked third, listed before other places in the US,
which are closer to the point provided. This illustrates how `proximity`
is only one of many factors influencing the ranking of results.

``` r
results5 <- oc_forward(placename = "Paris", proximity = oc_points(38.0, -84.6))
knitr::kable(results5)
```

| confidence | formatted                                                                      | northeast\_lat | northeast\_lng | southwest\_lat | southwest\_lng | ISO\_3166\_1\_alpha\_2 | ISO\_3166\_1\_alpha\_3 | type      | city  | continent     | country | country\_code | county             | state         | state\_code | hamlet | postcode | political\_union | state\_district      | bus\_stop | locality | road                      | road\_type | suburb        | town  |      lat |          lng |
| ---------: | :----------------------------------------------------------------------------- | -------------: | -------------: | -------------: | -------------: | :--------------------- | :--------------------- | :-------- | :---- | :------------ | :------ | :------------ | :----------------- | :------------ | :---------- | :----- | :------- | :--------------- | :------------------- | :-------- | :------- | :------------------------ | :--------- | :------------ | :---- | -------: | -----------: |
|          7 | Paris, KY, United States of America                                            |       38.23827 |     \-84.23209 |       38.16492 |    \-84.307326 | US                     | USA                    | city      | Paris | North America | USA     | us            | Bourbon County     | Kentucky      | KY          | NA     | NA       | NA               | NA                   | NA        | NA       | NA                        | NA         | NA            | NA    | 38.20980 |  \-84.252987 |
|          7 | Paris, IN 47230, United States of America                                      |       38.84422 |     \-85.61385 |       38.80422 |    \-85.653854 | US                     | USA                    | village   | NA    | North America | USA     | us            | Jennings County    | Indiana       | IN          | Paris  | 47230    | NA               | NA                   | NA        | NA       | NA                        | NA         | NA            | NA    | 38.82422 |  \-85.633854 |
|          6 | Paris, France                                                                  |       48.90216 |        2.46976 |       48.81558 |       2.224122 | FR                     | FRA                    | city      | Paris | Europe        | France  | fr            | Paris              | Ile-de-France | NA          | NA     | NA       | European Union   | Paris                | NA        | NA       | NA                        | NA         | NA            | NA    | 48.85670 |     2.351462 |
|          7 | Paris, IL 61944, United States of America                                      |       39.64998 |     \-87.64920 |       39.58149 |    \-87.721046 | US                     | USA                    | city      | Paris | North America | USA     | us            | Edgar County       | Illinois      | IL          | NA     | 61944    | NA               | NA                   | NA        | NA       | NA                        | NA         | NA            | NA    | 39.61115 |  \-87.696137 |
|          7 | Paris, AR 72855, United States of America                                      |       35.30659 |     \-93.67552 |       35.26815 |    \-93.761965 | US                     | USA                    | city      | Paris | North America | USA     | us            | Logan County       | Arkansas      | AR          | NA     | 72855    | NA               | NA                   | NA        | NA       | NA                        | NA         | NA            | NA    | 35.29203 |  \-93.729917 |
|          5 | Paris, TX 75460, United States of America                                      |       33.73839 |     \-95.43541 |       33.62063 |    \-95.627940 | US                     | USA                    | city      | Paris | North America | USA     | us            | Lamar County       | Texas         | TX          | NA     | 75460    | NA               | NA                   | NA        | NA       | NA                        | NA         | NA            | NA    | 33.66180 |  \-95.555513 |
|          9 | Paris, South Las Vegas Boulevard, Paradise, NV 89158, United States of America |       36.11205 |    \-115.17261 |       36.11195 |   \-115.172711 | US                     | USA                    | bus\_stop | NA    | North America | USA     | us            | Clark County       | Nevada        | NV          | NA     | 89158    | NA               | NA                   | Paris     | Paradise | South Las Vegas Boulevard | bus\_stop  | Hughes Center | NA    | 36.11200 | \-115.172661 |
|          7 | Paris, ON N3L 2M3, Canada                                                      |       43.23323 |     \-80.34428 |       43.15323 |    \-80.424281 | CA                     | CAN                    | city      | NA    | North America | Canada  | ca            | Brant County       | Ontario       | ON          | NA     | N3L 2M3  | NA               | Southwestern Ontario | NA        | NA       | NA                        | NA         | NA            | Paris | 43.19323 |  \-80.384281 |
|          8 | Paris, YT, Canada                                                              |       63.82042 |    \-138.62566 |       63.80519 |   \-138.671236 | CA                     | CAN                    | city      | Paris | North America | Canada  | ca            | Yukon, Unorganized | Yukon         | YT          | NA     | NA       | NA               | NA                   | NA        | NA       | NA                        | NA         | NA            | NA    | 63.81290 | \-138.648486 |

## Specifying what is returned

  - `language`: If you would like to get your results in a specific
    language, you can pass an [IETF language
    tag](https://en.wikipedia.org/wiki/IETF_language_tag), such as “es”
    for Spanish or “pt-BR” for Brazilian Portuguese, to the `language`
    parameter. OpenCage will attempt to return results in that language.
    If it is not specified, “en” (English) will be assumed by the API.

<!-- end list -->

``` r
results6 <- oc_forward(placename = "Berlin", country = "DE", language = "de")
knitr::kable(results6)
```

| confidence | formatted                 | northeast\_lat | northeast\_lng | southwest\_lat | southwest\_lng | ISO\_3166\_1\_alpha\_2 | ISO\_3166\_1\_alpha\_3 | type | city   | continent | country     | country\_code | political\_union | postcode | state  | state\_code |      lat |      lng |
| ---------: | :------------------------ | -------------: | -------------: | -------------: | -------------: | :--------------------- | :--------------------- | :--- | :----- | :-------- | :---------- | :------------ | :--------------- | :------- | :----- | :---------- | -------: | -------: |
|          4 | 10117 Berlin, Deutschland |       52.67704 |       13.54886 |       52.35704 |       13.22886 | DE                     | DEU                    | city | Berlin | Europe    | Deutschland | de            | European Union   | 10117    | Berlin | BE          | 52.51704 | 13.38886 |
|          2 | Berlin, Deutschland       |       52.67551 |       13.76116 |       52.33824 |       13.08835 | DE                     | DEU                    | city | Berlin | Europe    | Deutschland | de            | European Union   | NA       | Berlin | BE          | 52.50693 | 13.39748 |

  - `limit` specifies the maximum number of results that should be
    returned. Integer values between 1 and 100 are allowed, the default
    is 10 for `oc_forward()` and 1 for `oc_forward_df()`. Reverse
    geocoding always returns at most one result.

  - `min_confidence`, an integer value between 0 and 10, indicates the
    precision of the returned result as defined by its geographical
    extent, (i.e. by the extent of the result’s bounding box). See the 
    for details. Only results with at least the requested confidence
    will be returned.

  - `no_annotations`: OpenCage supplies additional information about the
    result location in the annotations field. The annotations include,
    among others, country information, time of sunset and sunrise, or
    the location in different geocoding formats, like Maidenhead,
    Mercator projection (EPSG 3857), geohash or what3words. Some
    annotations, like the Irish Transverse Mercator (ITM) or the US
    Federal Information Processing Standards (FIPS) code will only be
    shown when appropriate. `no_annotations` is `TRUE` by default, which
    means that the output will not contain annotations.

  - `no_dedupe` is `FALSE` by default. When TRUE the output will not be
    deduplicated.

For more information about the output and the query parameters, see the
package documentation, the [API doc](https://opencagedata.com/api) and
[OpenCage FAQ](https://opencagedata.com/faq).

## Caching

The underlying data at OpenCage is updated about once a day. Note that
this package uses [memoise](https://github.com/r-lib/memoise) with no
timeout argument so that results are cached inside an active R session.

``` r
system.time(oc_reverse(latitude = 10, longitude = 10))
```

    ##    user  system elapsed 
    ##    0.03    0.00    1.08

``` r
system.time(oc_reverse(latitude = 10, longitude = 10))
```

    ##    user  system elapsed 
    ##    0.05    0.00    0.04

To clear the cache of all results, you need to call
`memoise::forget(opencage:::oc_get_memoise)`.

``` r
memoise::forget(opencage:::oc_get_memoise)
```

    ## [1] TRUE

``` r
system.time(oc_reverse(latitude = 10, longitude = 10))
```

    ##    user  system elapsed 
    ##    0.03    0.00    0.97

## Privacy

All geocoding functions have a parameter `no_record`. It is `FALSE` by
default.

  - When `no_record` is `FALSE` a log of the query is made by OpenCage.
    These logs are used for debugging and in order to improve the
    service. [According](https://opencagedata.com/faq#legal) to
    OpenCage, all logs are automatically deleted after six months.

  - When `no_record` is `TRUE`, OpenCage still records that a request
    was made (e.g. to see whether you exceeded your quota), but not the
    specific content of your query. Please use if you have concerns
    about privacy and want OpenCage to have no record of your query.
    More information about privacy can be found on OpenCage’s [GDPR
    page](https://opencagedata.com/gdpr).

## Addresses

The geocoding functions also have an `abbr` parameter, `FALSE` by
default. When it is `TRUE` the addresses in the `formatted` field of the
results are abbreviated (e.g. “Main St.” instead of “Main Street”). For
more details see [this blog
post](http://blog.opencagedata.com/post/160294347883/shrtr-pls).

## Return query text

`oc_forward()` and `oc_reverse()` have an `add_request` argument,
indicating whether the request is returned again with the results. If
the `return` value is a `df_list`, the `placename` or
`latitude,longitude` is added as a column to the results. `json_list`
results will contain all request parameters, including the API key
used\! For `geojson_list` results `add_request` is currently ignored by
OpenCage.

## Meta

  - Please [report any issues or
    bugs](https://github.com/ropensci/opencage/issues).
  - License: GPL
  - Get citation information for `opencage` in R doing `citation(package
    = 'opencage')`
  - Please note that this project is released with a [Contributor Code
    of Conduct](CONDUCT.md). By participating in this project you agree
    to abide by its terms.

[![ropensci\_footer](http://ropensci.org/public_images/github_footer.png)](http://ropensci.org)
