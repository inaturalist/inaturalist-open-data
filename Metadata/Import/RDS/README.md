# Importing Into An AWS RDS PostgreSQL Database

**Prerequisites**:
  - A [PostgreSQL](https://www.postgresql.org/) client is installed
  - An [AWS RDS PostgreSQL database has been created](Setup) and is currently running
  - The RDS database has enabled the `s3Import` feature via an [IAM Role](Role)
<br/>

As an alternative to downloading and using the metadata locally, the files can be directly imported into an Amazon RDS database. This approach requires an AWS account and making use of the Amazon RDS and IAM services. With a PostgreSQL client you can connect to the remote RDS database without needing to download the metadata and manage local databases, at the cost of using these services. Read how to [set up and connect to an AWS RDS PostgreSQL database](Setup).

After the RDS database is setup and running, download [structure.sql](../../structure.sql) and apply it to the new database. From a terminal run (substituting with your own database name and user name):
```bash
psql -h dbname.XXXXXX.rds.amazonaws.com -U postgres dbname < structure.sql
```
<br/>

Now connect to the database with PostgreSQL. From a terminal run:
```bash
psql -h dbname.XXXXXX.rds.amazonaws.com -U postgres dbname
```
<br/>

Make sure PostGIS has been initialized. From the PostgreSQL console run:
```sql
CREATE EXTENSION postgis;
```
<br/>

Enable the [AWS S3 extension for RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/PostgreSQL.Procedural.Importing.html#USER_PostgreSQL.S3Import.ARNRole). From the PostgreSQL console run:
```sql
CREATE EXTENSION aws_s3 CASCADE;
```
<br/>

If the RDS database has successfully enabled `s3Import` from with an [IAM Role](Role), the following command should populate the tables created above with the latest iNaturalist Open Dataset snapshot. From the PostgreSQL console run:
```sql
SELECT aws_s3.table_import_from_s3('photos', '', '(format csv, header true, delimiter E''\t'', quote E''\b'')',
  aws_commons.create_s3_uri('inaturalist-open-data', 'photos.csv.gz', 'us-east-1')) as s;

SELECT aws_s3.table_import_from_s3('observations', '', '(format csv, header true, delimiter E''\t'', quote E''\b'')',
  aws_commons.create_s3_uri('inaturalist-open-data', 'observations.csv.gz', 'us-east-1')) as s;

SELECT aws_s3.table_import_from_s3('users', '', '(format csv, header true, delimiter E''\t'', quote E''\b'')',
  aws_commons.create_s3_uri('inaturalist-open-data', 'users.csv.gz', 'us-east-1')) as s;

SELECT aws_s3.table_import_from_s3('taxa', '', '(format csv, header true, delimiter E''\t'', quote E''\b'')',
  aws_commons.create_s3_uri('inaturalist-open-data', 'taxa.csv.gz', 'us-east-1')) as s;
```
<br/>

Create some indices that may help improve the speed of some queries. From the PostgreSQL console run:
```sql
CREATE INDEX index_photos_photo_uuid ON photos USING btree (photo_uuid);
CREATE INDEX index_photos_observation_uuid ON photos USING btree (observation_uuid);
CREATE INDEX index_taxa_taxon_id ON taxa USING btree (taxon_id);
CREATE INDEX index_users_user_id ON users USING btree (user_id);
CREATE INDEX index_observations_user_id ON observations USING btree (user_id);
CREATE INDEX index_observations_taxon_id ON taxa USING btree (taxon_id);
```
<br/>

At this point the database should be set up, populated with data, and indexed. For example, this query should return 10 photos of animals and details from their associated observations. From the PostgreSQL console run:

```sql
SELECT
  t.taxon_id,
  t.name taxon_name,
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

### Geospatial Queries, PostGIS
To make geospatial queries on observations, PostGIS must be installed and the database created with the `template_postgis` template. Create and populate a PostGIS geometry with the latitude and longitude columns in observations table. This `UPDATE` will likely to take longer to run than it took to import the data. From the PostgreSQL console run:
```sql
ALTER TABLE observations ADD COLUMN geom public.geometry;

UPDATE observations SET geom = ST_GeomFromText('POINT(' || longitude || ' ' || latitude || ')', 4326);

```
<br/>

After that finishes, geospatial queries can be made with the new `observations.geom` column. For example, this query should return 10 observations within a bounding box. From the PostgreSQL console run:
```sql
SELECT COUNT(*) FROM observations WHERE ST_Within(geom, 'POLYGON((-162 18, -153 18, -153 23, -162 23, -162 18))'::geography::geometry);
```

