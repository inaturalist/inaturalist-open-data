# Dataset Metadata

<p align="center">
  <img src="https://github.com/user-attachments/assets/2f16935c-1166-4cc8-82c7-6ce0e33bd892" width="600px">
</p>

The iNaturalist Open Dataset S3 bucket contains four separate metadata files representing observations, observers, photos, and taxa (the organisms that are the subject of the observations). These files are updated monthly. These files are stored in two ways: as separate gzipped tab-separated CSV files, and as a single gzipped archive containing all four files. The files are located as follows:

```
- inaturalist-open-data
  - metadata/
    - inaturalist-open-data-latest.tar.gz
    - inaturalist-open-data-20210407.tar.gz
    - ...
  - observations.csv.gz
  - observers.csv.gz
  - photos.csv.gz
  - taxa.csv.gz
```

The archive bundles are stored in the `metadata/` directory, with the most recent version being stored at `metadata/inaturalist-open-data.gz` and a limited number of older snapshots included. The separate CSV files are stored at the root of the bucket (in part becuause of a requirement when importing data from S3 [into RDS](Import/RDS)).

The files are all tab-separated CSV files, not using any quotes around text columns, and beware there are single and double quotes in some fields, such as taxon names.

Read more about how to [download](Download) and [import](Import) the metadata into some commonly used database software for easier processing.

## Understanding the tables and working with the data
This section helps explain how to interpret the table columns. Note that the records in tables are not randomized so be aware if your analysis requires random samples.

### Observations
Column | Description
-------|------------
observation_uuid | A unique identifier associated with each observation also available at iNaturalist.org via URLs constructed like this https://www.inaturalist.org/observations/c075c500-b566-44aa-847c-95da8fb8b3c9
observer_id | The identifier of the associated iNaturalist user who recorded the observation
latitude | The latitude where the organism was encountered
longitude | The longitude where the organism was encountered
positional_accuracy | The uncertainty in meters around the latitude and longitude
taxon_id | The identifier of the associated axon the observation has been identified as
quality_grade | `Casual` observations are missing certain data components (e.g. latitude) or may have flags associated with them not shown here (e.g. `location appears incorrect`). Observations flagged as not wild are also considered Casual. All other observations are either `Needs ID` or `Research Grade`. Generally, Research Grade observations have more than one agreeing identifications at the species level, or if there are disagreements at least ⅔ of the identifications are in agreement a the species level
observed_on | The date at which the observation took place
anomaly_score | A measure of "unusualness" of the observation taxon in the observation location based on the [iNaturalist Geomodel](https://www.inaturalist.org/blog/99727-using-the-geomodel-to-highlight-unusual-observations). Values below 1.0 indicate the taxon is not "expected nearby" the observation location according to the Geomodel, with lower values meaning more unexpected.

### Observers
Column | Description
-------|------------
observer_id | A unique identifier associated with each observer also available on https://www.inaturalist.org via URLs constructed like this: https://www.inaturalist.org/users/1
login | A unique login associated with each observer
name | Personal name of the observer, if provided

### Photos
Column | Description
-------|------------
photo_uuid | A unique identifier associated with each photo
photo_id | A photo identifier used on iNaturalist and available on iNaturalist.org via URLs constructed like this https://www.inaturalist.org/photos/113756411
observation_uuid | The identifier of the associated observation
observer_id | The identifier of the associated observer who took the photo
extension | The image file format, e.g. `jpeg`
license | All photos in the dataset have open licenses (e.g. Creative Commons) and unlicensed (CC0 / public domain)
width | The width of the photo in pixels
height | The height of the photo in pixels
position | When observations have multiple photos the user can set the position in which the photos should appear. Lower numbers are meant to appear first

### Taxa
Column | Description
-------|------------
taxon_id | A unique identifier associated with each node in the iNaturalist taxonomy hierarchy. Also available on iNaturalist.org via URLs constructed like this https://www.inaturalist.org/taxa/47219
ancestry | The taxon_ids of ancestry of the taxon ordered from the root of the tree to the taxon concatenated together with `\`
rank_level | A number associated with the rank. Taxon rank_levels must be less than the rank level of their parent. For example, a taxon with rank genus and rank_level 20 cannot descend from a taxon of rank species and rank_level 10
rank | A constrained set of labels associated with nodes on the hierarchy. These include the standard Linnaean ranks: Kingdom, Phylum, Class, Order, Family, Genus, Species, and a number of internodes such as Subfamily
name | The scientific name for the taxon
active | When the taxonomy changes, generally taxa aren’t deleted on iNaturalist to avoid breaking links. Instead taxa are made inactive and observations are moved to new active nodes. Occasionally, observations linger on inactive taxa which are no longer active parts of the iNaturalist taxonomy
