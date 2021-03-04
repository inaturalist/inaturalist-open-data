# Dataset Metadata

<p align="center">
  <img src="https://user-images.githubusercontent.com/48566/110021775-2981b780-7cf9-11eb-99bd-2b428c89ae1f.png" width="600px">
</p>

The iNaturalist Open Dataset S3 bucket contains four separate metadata files representing observations, observers, photos, and taxa (the organisms that are the subject of the observations). These files are updated weekly (**TODO: what day**). These files are stored in two ways: as separate gzipped tab-separated CSV files, and as a single gzipped archive containing all four files. The files are located as follows:

```
- inaturalist-open-data
  - metadata/
    - inaturalist-open-data.gz
    - inaturalist-open-data-2020-03-01.gz
    - ...
  - observations.csv.gz
  - photos.csv.gz
  - taxa.csv.gz
  - users.csv.gz
```

The archive bundles are stored in the `metadata/` directory, with the most recent version being stored at `metadata/inaturalist-open-data.gz` and a limited number of older snapshots included. The separate CSV files are stored at the root of the bucket (in part becuause of a requirement when importing data from S3 [into RDS](Import/RDS)).

The files are all tab-separated CSV files, not using any quotes around text columns, and beware there are single and double quotes in some fields, such as taxon names.

Read more about how to [download](Download) and [import](Import) the metadata into some commonly used database software for easier processing.