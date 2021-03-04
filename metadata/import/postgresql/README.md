# Importing Into A Local PostgreSQL Database

Prerequisites:
  - [PostgreSQL](https://www.postgresql.org/) is installed and running
  - (optional) if you intend to do geospatial queries, [PostGIS](https://postgis.net/) must be installed
  - The [metadata](../../../Metadata) files have been [downloaded](../../Download)
<br/>

First create a new database:
```bash
createdb -T template_postgis inaturalist-open-data
```
<br/>

Download the [structure.sql](../../structure.sql) and apply it to the new database:
```bash
psql inaturalist-open-data < structure.sql
```
<br/>

Import the CSV files into the tables (adjust the paths according to you local enironment):
```sql
COPY observations FROM 'observations.csv' DELIMITER E'\t' QUOTE E'\b' CSV HEADER;
COPY photos FROM 'photos.csv' DELIMITER E'\t' QUOTE E'\b' CSV HEADER;
COPY taxa FROM 'taxa.csv' DELIMITER E'\t' QUOTE E'\b' CSV HEADER;
COPY users FROM 'users.csv' DELIMITER E'\t' QUOTE E'\b' CSV HEADER;
```
<br/>

Create some indices that may help improve the speed of some queries:
```sql
CREATE INDEX index_photos_photo_uuid ON photos USING btree (photo_uuid);
CREATE INDEX index_photos_observation_uuid ON photos USING btree (observation_uuid);
CREATE INDEX index_taxa_taxon_id ON taxa USING btree (taxon_id);
CREATE INDEX index_users_user_id ON users USING btree (user_id);
CREATE INDEX index_observations_user_id ON observations USING btree (user_id);
CREATE INDEX index_observations_taxon_id ON taxa USING btree (taxon_id);
```
<br/>

At this point the database should be set up, populated with data, and indexed. You can test by querying it. For example here's a query that will fetch information about observations of animals:

```sql
SELECT
  t.taxon_id,
  t.name,
  'https://www.inaturalist.org/observations/' || o.observation_uuid as observation_url,
  u.login,
  'https://inaturalist-open-data.s3.amazonaws.com/photos/' || p.photo_id || '/medium.' || p.extension as photo_url
FROM taxa t
JOIN observations o ON (t.taxon_id = o.taxon_id)
JOIN photos p ON (o.observation_uuid=p.observation_uuid)
JOIN users u ON (o.user_id = u.user_id)
WHERE t.ancestry LIKE '48460/1/%'
LIMIT 10;
```