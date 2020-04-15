
Download Data from open http://opengeodb.org: http://www.fa-technik.adfc.de/code/opengeodb/

import into MySQL-Database

Create new Database Table:

```
CREATE TABLE `zip_coordinates` (
  `zc_id` int(11) NOT NULL AUTO_INCREMENT,
  `zc_loc_id` int(11) NOT NULL,
  `zc_zip` varchar(10) NOT NULL,
  `zc_location_name` varchar(255) NOT NULL,
  `zc_lat` double NOT NULL,
  `zc_lon` double NOT NULL,
  PRIMARY KEY (`zc_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

Import location data to zip_coordinates:

```
INSERT INTO zip_coordinates (zc_loc_id, zc_zip, zc_location_name, zc_lat, zc_lon)
SELECT gl.loc_id, plz.text_val, name.text_val, coord.lat, coord.lon
FROM geodb_textdata plz
LEFT JOIN geodb_textdata name     ON plz.loc_id = name.loc_id
LEFT JOIN geodb_locations gl      ON plz.loc_id = gl.loc_id
LEFT JOIN geodb_hierarchies tier  ON plz.loc_id = tier.loc_id
LEFT JOIN geodb_coordinates coord ON plz.loc_id = coord.loc_id
WHERE plz.text_type  = 500300000 /* Postleitzahl */
AND   name.text_type = 500100000 /* Name */
AND   tier.id_lvl1 = 104
AND   tier.id_lvl2 = 105 /* Bundesrepublik Deutschland */
AND   name.text_locale = "de" /* deutschsprachige Version */
AND   gl.loc_type IN ( 100600000 /* pol. Gliederung */, 100700000 /* Ortschaft */ )
```

Export zip_coordinates as JSON through MySQL Workbench

In your new JSON File replace the coordinate properties with an object:

```
$ sed -i \
    's/"zc_lat":(.+), "zc_lon":(.+)},\n/"loc":{ type:"Point", coordinates: [\1,\2] }},\n/g' \
  geodb_zipcodes.json
```
