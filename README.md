# Nextflow extension package for Google Cloud (XPACK-GOOGLE)

## Introduction

The Nextflow extension package for Google Cloud is a plugin provided by [Seqera Labs](https://www.seqera.io/) that allows the support for [Google Filestore](https://cloud.google.com/filestore) file system when deploying Nextflow pipelines with [Google LifeSciences](https://cloud.google.com/life-sciences) and [Google Cloud Batch](https://cloud.google.com/batch) computing services.

The plugin requires a license key to be used. If you are interested, please contact us for an evaluation license at [sales@seqera.io](mailto:sales@seqera.io).

## Prequisites

* Java 11 or later
* Nextflow `23.01.0-edge` or later
* Google LifeScience or Google Batch API enabled
* Google Filestore instance

## Quick start

### 0. instance preparation

- Install Java and the required NFS system dependecies

    ```
    sudo apt-get -y install openjdk-11-jdk-headless nfs-common
    ```

- Install Nextflow:

    ```
    export NXF_VER=23.01.0-edge
    curl get.nextflow.io | bash
    ./nextflow self-update
    ```

- Configure the Google crendentials defining the variable `GOOGLE_APPLICATION_CREDENTIALS`
in the launching environment, e.g.:

    ```
    export GOOGLE_APPLICATION_CREDENTIALS=$PWD/google.json
    ```

- Mount the shared file system in the launching environment:

    ```
    sudo mkdir /nfs
    sudo mount 10.195.15.250:/share1 /nfs/
    sudo chmod  go+rw /nfs
    ```

    In the above snippet replace the `10.195.15.250:/share1` string with your Filestore
    instance mount point. See the Google Filestore [documentation](https://cloud.google.com/filestore/docs/mounting-fileshares) for details.


### 1. License configuration 

Configure the Nextflow XPACK license definining the variable `NXF_XPACK_LICENSE` 
in the launching environment, e.g.:

    export NXF_XPACK_LICENSE=<license string>


### 2.1 Plugin configuration for Google Cloud Batch 

Create the `nextflow.config` file in the pipeline launching directory with the 
following settings:

    plugins {
      id 'xpack-google@1.0.0-beta.2'
    }

    google.location = 'europe-west2'
    google.batch.nfsVolumes.nfs1.target = '10.195.15.250:/share1'
    google.batch.nfsVolumes.nfs1.mountPath = '/nfs'
    
    process.executor = 'google-batch' 


In the above example replace the *region*, NFS *target* and *mountPath* with 
the values matching your Filestore instance configuration.

Note: more than one Filestore instance can be configured providing the corresponding 
`target` and `mountPath` values using a unique `nfsVolumes` names for each of them e.g. 
`nfs1`, `nfs2`, etc.


### 2.2 Plugin configuration for Google LifeSciences 

Create the `nextflow.config` file in the pipeline launching directory with the 
following settings:

    plugins {
      id 'xpack-google@1.0.0-beta.2'
    }

    google.region = 'europe-west2'
    google.lifeSciences.nfsVolumes.nfs1.target = '10.195.15.250:/share1'
    google.lifeSciences.nfsVolumes.nfs1.mountPath = '/nfs'
    
    process.executor = 'google-lifesciences' 

In the above example replace the *region*, NFS *target* and *mountPath* with 
the values matching your Filestore instance configuration.

Note: more than one Filestore instance can be configured providing the corresponding 
`target` and `mountPath` values using a unique `nfsVolumes` names for each of them e.g. 
`nfs1`, `nfs2`, etc.

### 3. Launch the pipeline execution


Launch the Nextflow pipeline execution specifying the as work directory a 
path in the shared file system, e.g.:

```
./nextflow run hello -w /nfs/scratch
```

## Compatibility table

| Nextflow version        | Xpack version   |
|---                      |---              |
| 23.01.0-edge (or later) | 1.0.0-beta.2    |
| 21.06.0-edge (or later) | 1.0.0-beta.1    |

## License  

Copyright 2021-2023, Seqera Labs, S.L. All Rights Reserved.
