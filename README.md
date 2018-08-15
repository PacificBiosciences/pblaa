<h1 align="center">Long Amplicon Analysis</h1>
<p align="center">C++ applications to generate high-quality, phased consensus sequences from pooled amplicons</p>

***
## Availability
The latest pre-release, developers-only linux binaries can be installed via [bioconda](https://bioconda.github.io/).

    conda install pblaa

These binaries are not ISO compliant.
For research only.
Not for use in diagnostics procedures.

Official support is only provided for official and stable
[SMRT Analysis builds](http://www.pacb.com/products-and-services/analytical-software/)
provided by PacBio.
No support for source builds.
No support via mail to developers.

### [Long Amplicon Analysis](doc/PBLAA.md)

`laa` takes a random subset of reads, filtered according to user-adjustable
 parameters, and attempts to generated a high-quality phased consensus sequence
 corresponding to each unique species of molecule present. More information available [here](doc/PBLAA.md).

### [Long Amplicon Analysis with Guided Clustering](doc/PBLAA.md)

`laagc` performs the same procedure as LAA, but utilizes a preliminary
 "pre-clustering" step to group reads according to their similarity to a list
 of user-supplied sequences, enabling faster, higher quality results from
 more complex samples than regular Long Amplicon Analysis. More information available [here](doc/PBLAA.md).

## Help

Support is only provided for official and stable
[SMRT Analysis builds](http://www.pacb.com/products-and-services/analytical-software/)
provided by PacBio and not for source builds.

## License
[PacBio software license](LICENSE)

DISCLAIMER
----------
THIS WEBSITE AND CONTENT AND ALL SITE-RELATED SERVICES, INCLUDING ANY DATA, ARE PROVIDED "AS IS," WITH ALL FAULTS, WITH NO REPRESENTATIONS OR WARRANTIES OF ANY KIND, EITHER EXPRESS OR IMPLIED, INCLUDING, BUT NOT LIMITED TO, ANY WARRANTIES OF MERCHANTABILITY, SATISFACTORY QUALITY, NON-INFRINGEMENT OR FITNESS FOR A PARTICULAR PURPOSE. YOU ASSUME TOTAL RESPONSIBILITY AND RISK FOR YOUR USE OF THIS SITE, ALL SITE-RELATED SERVICES, AND ANY THIRD PARTY WEBSITES OR APPLICATIONS. NO ORAL OR WRITTEN INFORMATION OR ADVICE SHALL CREATE A WARRANTY OF ANY KIND. ANY REFERENCES TO SPECIFIC PRODUCTS OR SERVICES ON THE WEBSITES DO NOT CONSTITUTE OR IMPLY A RECOMMENDATION OR ENDORSEMENT BY PACIFIC BIOSCIENCES.
