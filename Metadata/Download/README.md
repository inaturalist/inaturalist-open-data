# Downloading Metadata

The metadata files are stored in the iNaturalist Open Dataset as follows:

```
- inaturalist-open-data
  - metadata/
    - inaturalist-open-data-latest.tar.gz
    - inaturalist-open-data-20241127.tar.gz
    - ...
  - observations.csv.gz
  - observers.csv.gz
  - photos.csv.gz
  - taxa.csv.gz
```

You can use the [AWS Command Line Interface (CLI)](https://aws.amazon.com/cli/) to download the complete bundle, or each file separately. For example to download the bundle you can use:

```
aws s3 --no-sign-request --region us-east-1 cp s3://inaturalist-open-data/metadata/inaturalist-open-data-latest.tar.gz inaturalist-open-data-latest.tar.gz
```

To download just the photos file you can use:

```
aws s3 --no-sign-request --region us-east-1 cp s3://inaturalist-open-data/photos.csv.gz photos.csv.gz
```
