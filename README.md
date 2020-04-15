# geodistance-mongo-example
how to setup a mongo collection and search places by distance (in Germany)

Inspired by:
- https://docs.mongodb.com/manual/tutorial/geospatial-tutorial/
- http://opengeodb.org/wiki/OpenGeoDB_-_Umkreissuche

Original MySQL-Data from OpenGeoDB (opengeodb.org):
http://www.fa-technik.adfc.de/code/opengeodb/dump/

## Steps

Import collection to MongoDB:

```mongoimport --db geodb --collection zipcodes --file geodb_zipcodes.json --jsonArray```

Create Index:

```db.zipcodes.createIndex( {loc: "2dsphere"} ) ```

Query by distance:

```
db.getCollection('zipcodes').find({ 
       loc: { $geoWithin: { $centerSphere: [ [<lat>,<lng>], <km> / 1.6 / 3963.2 ] } } 
   })
```
      
Example to find every town within a 10km radius around Frankfurt(Main)

```
db.getCollection('zipcodes').find({ 
       loc: { $geoWithin: { $centerSphere: [ [50.1167,8.68333], 10 / 1.6 / 3963.2 ] } } 
   })
```

_Note_: just remove "/ 1.6" if you want to use miles instead of km
