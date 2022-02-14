# iNaturalist Open Data

The iNaturalist Open Dataset is one of the world’s largest public datasets of photos of living organisms, containing over 70 million photos. Understanding how to make use of these photos requires a basic understanding of the **iNaturalist observations** that accompany these photos.

[iNaturalist](https://www.inaturalist.org) is a community science platform where participants record observations representing encounters with individual organisms. Each observation can have one or more photos as evidence of the encounter. All observations are associated with a single user who recorded the observation. Much of the activity on the platform relates to identifying the single taxon that the organism represents. In the cartoon below, an observer creates an observation with three associated photos representing a specific butterfly taxon.

<p align="center">
  <img src="https://user-images.githubusercontent.com/48566/114926381-46340380-9dfe-11eb-8379-a77a550d1822.png" width="300px">
</p>

The iNaturalist Open Dataset is structured as a "bucket" of images stored using the Simple Storage Server (S3) provided by Amazon Web Service (AWS). Each photo has multiple versions resized to different maximum dimensions so users can download the size most useful to their use case.

To summarize the contents of the iNaturalist Open Dataset, we include data for four tab-separated CSV files representing observations, observers, photos, and taxa (the organisms that are the subject of the observations). These files are a snapshot of iNaturalist observations and will be generated monthly. But iNaturalist is continuously changing as people add and remove observations and photos, or as new identifications change our understanding of the taxa the observations represent. That means there may be photos in the Open Dataset not represented as rows in these tables. There may also be rows in these tables pointing to images that are no longer in the bucket having been deleted or moved.

The overall structure of the S3 bucket looks like this:

```
- inaturalist-open-data
  - photos/
    - PHOTO_ID/
      - original.jpg
      - large.jpg
      - ...
  - metadata/
    - inaturalist-open-data-latest.tar.gz
    - inaturalist-open-data-20210407.tar.gz
    - ...
  - observations.csv.gz
  - observers.csv.gz
  - photos.csv.gz
  - taxa.csv.gz
```

The available image sizes and maximum dimensions are:

```
original - 2048px
large - 1024px
medium - 500px
small - 240px
thumb - 100px
square - exactly 75x75px, cropped to be square
```

<p align="center">
  <img src="https://user-images.githubusercontent.com/48566/114919407-344e6280-9df6-11eb-8138-8dee7d626673.png" width="600px">
</p>


The **photo_id** and **extension** can be used to fetch the appropriate photo files from among the tens of millions in the iNaturalist Open Dataset. The file path in the S3 bucket is `s3://inaturalist-open-data/photos/[photo_id]/medium.[extension]`, and the file URL is `https://inaturalist-open-data.s3.amazonaws.com/photos/[photo_id]/medium.[extension]`.

If the photos in this dataset are used in any way that requires attributing the photographer, the **license**, **observer name**, and **observer login** can be used to create an attribution statement. Unless the photo license specifies the photo is in the public domain, all photographers retain copyright of their photos, and the license under which the photo is shared dictates how the photo can be used. Please ensure that any use of these photos is in compliance with their [Creative Commons](https://creativecommons.org/licenses/) license terms.

Photos with a CC0 license can be attributed as "[observer name, or observer login], no rights reserved (CC0)". For example "Name, no rights reserved (CC0)", or "Login, no rights reserved (CC0)". Photos with other Creative Commons licenses can be attributed as "© [observer name, or observer login], some rights reserved ([license abbreviation])". For example "© Name, some rights reserved (CC-BY)", or "© Login, some rights reserved (CC-BY-NC)"
