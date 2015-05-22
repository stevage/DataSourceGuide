# Aus-Geo-CSV 1.0

**Aus Geo CSV** is a convention for publishing **point** or **region-mapped** geospatial data in CSV format to data.gov.au and other open data portals. Datasets in this format are supported by [TerriaJS](https://github.com/terriajs/terriajs) (and hence the [National Map](http://nationalmap.gov.au/)) and will be useful for other purposes too. A `State` column in a CSV file tagged `aus-geo-csv` can unambiguously be understood to refer to an Australian state, for example.

Datasets with line feature or explicit polygons (instead of references to standard polygon boundaries) are not covered by this standard, and should be provided as GeoJSON.

In this document:

* **Recommended**: The best field name for maximum reusability. High priority for support in TerriaJS (the software that runs the National Map). Sometimes several options are recommended, depending on your need for precision.
* **Accepted**: A field name which is reasonably reusable. Generally supported by TerriaJS.
* **Discouraged**: A field name which is ambiguous or not intuitive to a wide audience, but may be commonly used due to existing software. Possibly supported by TerriaJS but may be discontinued.

It is generally acceptable to include "discouraged" or "avoid" fields if there is also recommended or accepted fields as the recommended or accepted fields will be used.

**Document Status: initial use. This document will evolve, but it is unlikely that field names currently recommended will become deprecated.**

## General recommendations

Geospatial fields SHOULD be provided after all other fields.

The CSV format MUST:

* Consist only of one header row followed by data rows (no other metadata within the file)
* Use `,` as field delimiter
* Use `\r\n` (Windows) or `\n` (Linux, OSX) as end of line character
* Use double quotes around any value containing a comma, and double-double quotes to represent double quotes: `"like ""this"""`

It SHOULD be encoded in UTF-8. Headers are not considered to be case-sensitive.

Consider publishing the data as a [Tabular Data Package](http://dataprotocols.org/tabular-data-package/) by following those recommendations and including a `datapackage.json`. (Data Package use in Australia is experimental, for now.)

### Tags
In data.gov.au, datasets that conform to this standard SHOULD be tagged "`aus-geo-csv`". The dataset MUST NOT contain CSV files that do not conform (but may contain other non-CSV files) as these may be ambiguous and lead to confusing display in the National Map and other services.

###Quick summary
Tables should look like one of these:

```
ID,Population,LGA_code,State
1,100600,24600,VIC
```

or

```
ID,Population,Postcode,State
1,28000,3000,VIC
```

or

```
ID,Name,Lat,Lon
1,Bacchus Marsh Airport,-37.7313,144.4212
```

EITHER a latitude/longitude pair, OR one or more of the following region fields should be provided.


## Point data

To encode individual points with a latitude and longitude, two fields are required. Each MUST be a number in decimal degrees. Numbers SHOULD NOT be enclosed in double quotes.

##### Recommended field names
* `Lat`, `Lon` [the only format currently supported by TerriaJS]

##### Accepted field names
* `Latitude`, `Longitude`; 
* `Lat`, `Lng` [TerriaJS support imminent]

##### Discouraged
* `x`, `y`; 
* `WKT` (single column with data in `POINT(-37.8 144.9)` format); 
* `easting`, `northing`; 
* combined format: `(-37.8, 144.9)`; 
* GeoJSON

Locations SHOULD be given in the GDA94 datum ([EPSG:4283](http://spatialreference.org/ref/epsg/4283/)), but WGS84 is acceptable ([EPSG:4326](http://spatialreference.org/ref/epsg/4326/)). The difference is [generally less than one metre](http://www.geoproject.com.au/gda.faq.html). The datum chosen SHOULD be indicated in the metadata for the dataset. (There is currently no standard for this.)

## LGAs, postcodes and other regions 

### Postcode

A four digit Australian postcode.

##### Recommended field names 
* `Postcode` [supported by TerriaJS, currently displayed using POA boundaries]
* `POA` *(if referring to ABS "postal area" – see below)* [supported by TerriaJS]
* `ABS2011_POA`, `ABS2006_POA` (if a specific version of the ABS boundaries is required) 

For greater precision, additional fields `Suburb` and `State` MAY be provided. For example: `Postcode` 3068, `Suburb` Clifton Hill, `State` VIC.

(Note a POA "postal area" is [not quite the same as a postcode](http://www.abs.gov.au/websitedbs/censushome.nsf/home/factsheetspoa?opendocument&navpos=450).) 

### Local Government Area
#### By ID
##### Recommended field names
* `LGA_code` [TerriaJS support imminent]
* `ABS2013_LGA`, `ABS2011_LGA`,`ABS2006_LGA`. (These longer names allow different versions of the ABS boundaries. This distinction is not currently respected by TerriaJS)

##### Acceptable field name
* `lga` [currently supported by TerriaJS]

This MUST be the 5 digit code [described by the ABS](http://www.abs.gov.au/AUSSTATS/abs@.nsf/DetailsPage/1270.0.55.003July%202011). For example, Brisbane is 31000. Complete lists are available [here](http://www.abs.gov.au/AUSSTATS/abs@.nsf/DetailsPage/1270.0.55.003July%202011).

#### By name
This field SHOULD be used as a human-readable addition to `lga_code`. It is NOT recommended as the primary lookup (and is not currently supported as such by TerriaJS). 

##### Recommended field name
* `LGA_Shortname` [not currently supported by TerriaJS]

#####Acceptable field names
* `adm2` [not currently supported by TerriaJS]

The contents MUST be the short form of the LGA name, with no "City of", "Council" etc. For example: "Melbourne", "Greater Geelong". It SHOULD be capitalised like this. 

A separate `State` column (and/or `lga_code` column) MUST be provided, as LGA names are not unique across states.

### State

##### Recommended field names
* `State` [supported by TerriaJS]
* `ABS2011_STE`, `ABS2006_STE` (in the rare instance that precision of the exact ABS version of the state boundaries is required)

##### Discouraged field names
* `ste` [supported by TerriaJS]

The contents MUST be the two or three-letter form of the state or territory ("VIC", "NT"). It is not considered to be case-sensitive.

### ABS statistical regions

These statistical area types [defined by the ABS](http://www.abs.gov.au/websitedbs/d3310114.nsf/home/australian+statistical+geography+standard+%28asgs%29) are all currently supported by TerriaJS unless stated otherwise. In each case, the more precise `ABS2011_...` version SHOULD be used if you know which version of the ABS boundaries is appropriate. (TerriaJS does not currently support different versions.)

* `SA4`, `ABS2011_SA4`: "[Statistical area level 4](http://www.abs.gov.au/ausstats/abs@.nsf/0/B01A5912123E8D2BCA257801000C64F2)" (ABS)
* `SA3`, `ABS2011_SA3`: "[Statistical area level 3](http://www.abs.gov.au/ausstats/abs@.nsf/Latestproducts/E7369D1FCE596315CA257801000C64E5)" (ABS)
* `SA2`, `ABS2011_SA2`: "[Statistical area level 2](http://www.abs.gov.au/ausstats/abs@.nsf/Latestproducts/88F6A0EDEB8879C0CA257801000C64D9)" (ABS)
* `SA1`, `ABS2011_SA1`: "[Statistical area level 1](http://www.abs.gov.au/ausstats/abs@.nsf/0/7CAFD05E79EB6F81CA257801000C64CD)" (ABS) [not currently supported by TerriaJS for performance reasons]
* `CED`, `ABS2011_CED`: "[Commonwealth electoral division](http://www.abs.gov.au/ausstats/abs@.nsf/0/9C8331F55896F9C5CA2578D40012CF99?opendocument)" (ABS)
* `SED`, `ABS2011_SED`: "[State electoral division](http://www.abs.gov.au/ausstats/abs@.nsf/Latestproducts/94496C7EA68A1522CA2578D40012CFB8)" (ABS)
* `SSC` (`Suburb` acceptable, currently treated as a synonym): "[State suburbs](http://www.abs.gov.au/AUSSTATS/abs@.nsf/Previousproducts/2C6132C0B332C336CA2578D40012CF76)" (ABS)

### Other boundaries

* `CNT2` (`iso2` acceptable): "Two letter country codes" ([ISO 3166-1 Alpha 2](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2))
* `CNT3` (`iso3` acceptable): "Three letter country codes" ([ISO 3166-1 Alpha 3](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-3))

### Other boundaries not currently supported by TerriaJS
These are included here to support standardisation and future support by TerriaJS.

* `SOS`, `ABS2011_SOS`, `ABS2006_SOS`: Section of State (ABS) (http://www.abs.gov.au/ausstats/abs@.nsf/Lookup/2901.0Chapter23102011)
* `SOSR`, `ABS2011_SOSR`, `ABS2006_SOSR`: Section of State Range (ABS)
* `SLA`, `ABS2011_SLA`, `ABS2006_SLA`: Statistical Local Area (ABS)
* `PHN`, `DOH2015_PHN`, `DOH2014_PHN`: Primary Health Network (Department of Health)