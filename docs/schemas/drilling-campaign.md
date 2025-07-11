import SchemaUri from '@theme/SchemaUri';
import FlatProperties from './_generated/flatmd/objects/drilling-campaign-1.0.0.md';

# drilling-campaign

<SchemaUri uri="schema/objects/drilling-campaign/1.0.0/drilling-campaign.schema.json" />

## Overview

### Objective

This documentation provides a comprehensive guide to the JSON schema for drilling campaign geoscience objects. It is designed to assist both novice and experienced geoscientists in understanding, implementing, and troubleshooting JSON schemas for their drilling data.

### Background

The drilling campaign geoscience object is meant to be used for a set of planned drillholes and their interim drilling results, not necessarily for individual planned drill holes. Each object is a snapshot of data at some point-in-time. The purpose of the drilling campaign  is to provide a flexible way to organize and store the data across a set of planned drill holes that would be used in production contexts, like planning a .

### Scope

- Planned drillholes

### Audience

-	Developers
-	Geoscientists
-	GIS professionals

## Schema structure

The JSON schema for downhole collection geoscience objects is structured to capture essential data elements relevant to downhole data. Below is a high-level overview of the schema structure:

- Base Object Properties – The root component for all geoscience objects containing common attributes such as name, description, and a unique identifer.
- Base Spatial Data Properties – A set of properties common to all spatial objects such as bounding box and coordinate reference system.
- Location – The geographic reference of each drill hole in the downhole collection, described in XYZ notation (northing, easting, elevation). The XY coordinates (northing, easting) should be relative to the coordinate reference system defined in the base spatial data properties.
- Path – The trajectory of each drill hole in the downhole collection from the “top” of the hole to the “bottom” of the hole. The path is represented by a series of depth, azimuth, and dip values along the course of the drill hole. This is the raw survey data that is typically used in a desurveying algorithm to compute the geometry of the drill hole in three-dimensional space.
- Collections – The downhole data stored in a series of related collections. Each collection is made up of attributes that are related to an interval or depth. Collections are analogous to Tables in a database and attributes are analogous to columns in a table.
- Metadata – Information about the schema version, data source, units, and other metadata.

<FlatProperties />

::mermaid[_generated/uml/downhole-collection-1.3.0.mmd]

## Schema definitions

NOTE: To keep things simple, only the required properties are defined. For a full list of available properties, refer to the individual component schemas.

### Base spatial data properties

|   Property	|   Value | Example |
| ------------- | ------- | ------- |
|   name	    |   The human-readable name of the drilling campaign object. This is the value that users will see when they browse an Evo workspace or list out objects through the Evo API.	 | `"Summer 24 Drill Program"` |
|   uuid	    |   The universally unique identifier of the drilling campaign object. 	|   `dd037871-4279-4954-bd43-3ead9d40a56e`    |
| bounding_box	|   The geographic bounds of the drill hole locations contained in the drilling campaign object. This is used for spatial search in the Evo web portal.   | `{"min_y": 0, "max_y": 10, "min_x": 0, "max_x": 10, "min_z": 0, "max_z": 10}` |
|   coordinate_reference_system |   The coordinate reference system (CRS) that all location data is in. This applies to the bounding box as well as the drill hole locations. See ["Common data types"](https://developer.seequent.com/docs/api/fundamentals/common-data-types/#coordinate-reference-systems) for more information.   |   `{"epsg_code": 32617}` |

## Drilling campaign properties

|   Property	| Value | Example |
| ------------- | ------- | ------- |
|   schema        |   The specific version of the schema that the drilling campaign object will use. This will be used by the Geoscience Object Service to validate the properties of the object.	|   `"/objects/drilling-campaign/1.0.0/drilling-campaign.schema.json"` |
|   depth_unit   |	The unit of measure that all depth values are in. This applies to all depth/from/to values across all collections.  |	`"m"` |
|   hole_id |   A category attribute that provides the main index order for the drill holes in the downhole collection. Category attributes are made up of a table and values. The values represent the index order of each drill hole and provide the main lookup for other attributes or properties. Each drill hole is represented by an integer index value, and the actual drill hole string value is included in the lookup table.    |

### Planned

|   Property	|   Value |
| ------------- | ------- |
|   locations |	The geographic location of each drill hole in the drilling campaign. Each location is represented as X, Y, Z values (northing, easting, elevation). The coordinates array has 3 columns appropriately named “x”, “y”, and “z” and each value is a float. The row index where the coordinates appear in the array must match the row index where the drill holes appear in the hole_id property. For example, the values at index 4 of the coordinates array are the coordinates for the drill hole at index 4 in the hole_id array.   |
|   depths   |   The total depth values of each drill hole in the drilling campaign. The columns are “final”, “target”, and “current”. These are the final/target/current depth values for every drill hole. Each depth value is a float and the row index in the distances must match the row index of the hole_id array.   |
|   path    |   The trajectory of each drill hole in the drilling campaign. The columns are “distance” (length of the segment), "inclination" (angle up from vertical down), “azimuth”, “dip” (angle down from horizontal), "toolface", "lift rate", "drift rate". These are the parameters that define each segment of the planned drillhole. There is no reference to the hole id directly, this is handled by the holes property that provides the offset and count of rows for each drill hole.|
|   holes   |	The row offset and counts for the drill hole path data. Each drill hole in the drilling campaign appears once, using the hole index provided by the hole_id lookup. The offset is the starting row for each drill hole, and the count is the number of rows for each drill hole.  |

#### Planned drillholes example

As a detailed example, let’s say you have 5 drill holes that you wish to include in a drilling campaign object. A good starting point would be to first establish the `hole_id` attribute, which provides the indexes and lookup for all subsequent properties.
The `hole_id` table is a key : value pair where the key is some unique identifier and the value is the actual drill hole identifier. This is the main lookup table for the hole IDs and provides a more efficient storage model for large datasets. The key established here will be used across various properties in the drilling campaign as a sort of normalization of the data. The lookup table would look something like this:

![](_img/drilling-campaign-00.png)

The `hole_id` values establish the index (or order) that data in other properties will appear. Using the keys used in the lookup table, the values would look like this:

![](_img/drilling-campaign-01.png)

The drill hole with a key of [1] (which resolves to “phase_001”) is at index [0], drill hole [2] (which resolved to “phase_002”) is at index [1], etc.

With this index order established, we can now populate some of the other properties. The coordinates would then look like this:

![](_img/drilling-campaign-02.png)

Remember: the coordinates should be in the coordinate system established by the base `coordinate_reference_system` property.

Using the key indexes, we know which coordinate values are for which drill holes:

![](_img/drilling-campaign-03.png)

Where the coordinates at index [0] are the coordinate values for the drill hole at index [0] in the values array, which is the drill hole with a key of [1], which resolves to “DH-01”.

We can also populate the drill holes distances properties using the same index/order:

![](_img/drilling-campaign-04.png)

Where the distances at index [0] are the depth values for the drill hole at index [0] in the values array, which is the drill hole with a key of [1], which resolves to “DH-01”.

For the drill hole path, there could be multiple rows for each drill hole, so a single index will not apply. The path data for multiple drill holes would look like:

![](_img/drilling-campaign-05.png)

Remember: the distance values should be in the unit of measure established by the base ”distance_units” property.

Remember: the dip convention adopted by the Geoscience Object Service is that “positive dip points down”. Make sure your dip values honour this convention.

The holes array will then provide the instructions how to locate the data for each drill hole, based on the following logic:

![](_img/drilling-campaign-06.png)

The data for the drill hole with hole index [1], which resolves to ”phase_001“ based on the lookup, starts at index 0 of the path array and goes for a count of 1. The data for phase_002 (drill hole [2]) starts at index 1 and goes for a count of 3, and so on.

The final holes values would then be:

![](_img/drilling-campaign-07.png)

This provides all the logic required to marry the various attributes back to the drill hole IDs.

### Planned results

The main result collections to choose from are “interval” and “distance”. The difference between them is whether the data is based on a from/to (interval) or a single depth (distance) value.

NOTE: There are other collection options for specific scenarios (e.g. structures), but these will not be described here. Please refer to the schema for additional information on the other collection types.

Both the interval and distance collections are made up of 3 main components:

- The from-to interval values (for interval) OR the distance values (for distance)
- The attributes (the columns) related to the intervals/depths
- The index/offset/counts, like the path, that provides the instructions how to parse out the data and link it to the drill holes in the campaign

### Interim

The properties of a interim drilling data are substantially the same as those of the planned drillholes, with the only difference being the path description.

|   Property	|   Value |
| ------------- | ------- |
|   locations |	The geographic location of each drill hole in the drilling campaign. Each location is represented as X, Y, Z values (northing, easting, elevation). The coordinates array has 3 columns appropriately named “x”, “y”, and “z” and each value is a float. The row index where the coordinates appear in the array must match the row index where the drill holes appear in the hole_id property. For example, the values at index 4 of the coordinates array are the coordinates for the drill hole at index 4 in the hole_id array.   |
|   depths   |   The total depth values of each drill hole in the drilling campaign. The columns are “final”, “target”, and “current”. These are the final/target/current depth values for every drill hole. Each depth value is a float and the row index in the distances must match the row index of the hole_id array.   |
|   path    |   The trajectory of each drill hole in the drilling campaign. The columns are “distance” (length of the segment), “azimuth”, “dip” (angle down from horizontal). This is the raw downhole survey data. There is no reference to the hole id directly, this is handled by the holes property that provides the offset and count of rows for each drill hole.|
|   holes   |	The row offset and counts for the drill hole path data. Each drill hole in the drilling campaign appears once, using the hole index provided by the hole_id lookup. The offset is the starting row for each drill hole, and the count is the number of rows for each drill hole.  |

## Troubleshooting common validation errors
Common validation errors and their solutions:

- Error: "Required field missing" - Ensure that all required fields are present in the JSON object.
- Error: "Invalid data type" - Check that the data types of the fields match the schema definition.
- Error: "Out of range value" - Verify that numerical values are within the acceptable range defined in the schema.

## Glossary of key terms

- JSON - JavaScript Object Notation, a lightweight data interchange format.
- Schema - A structured framework for defining the format and content of data.
- Geoscience - The scientific study of the Earth, including its composition, structure, and processes.
- Component
- Element
- Object
- Spatial Object
- Bounding Box
- Coordinate Reference System (CRS)
- Parquet File
