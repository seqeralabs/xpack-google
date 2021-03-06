# Google extension package for Nextflow (XPACK-GOOGLE)

## Introduction

The Google extension package is a plugin provided by [Seqera Labs](https://www.seqera.io/) that allows the support for [Google Filestore](https://cloud.google.com/filestore) file system when deploying Nextflow pipelines with [Google LifeSciences](https://cloud.google.com/life-sciences) cloud computing service.

The plugin requires a license key to be used. If you are interested, please contact us for an evaluation license at [sales@seqera.io](mailto:sales@seqera.io).

## Prequisites

* Java 8 or later
* Nextflow `21.06.0-edge` or later
* Google LifeScience API enabled
* Google Filestore instance

## Quick start

### 0. instance preparation

- Install Java and the required NFS system dependecies

    ```
    sudo apt-get -y install openjdk-11-jdk-headless nfs-common
    ```

- Install Nextflow:

    ```
    export NXF_VER=21.06.0-edge
    curl get.nextflow.io | bash
    ./nextflow self-update
    ```

- Configure the Google crendentials defining the variable `GOOGLE_APPLICATION_CREDENTIALS`
in the lanching environment, e.g.:

    ```
    export GOOGLE_APPLICATION_CREDENTIALS=$PWD/google.json
    ```

- Mount the shared file system in the launching environment:

    ```
    sudo mkdir /nfs
    sudo mount 10.195.15.250:/tower1 /nfs/
    sudo chmod  go+rw /nfs
    ```

    In the above snippet replace the `10.195.15.250:/tower1` string with your Filestore
    instance mount point. See the Google Filestore [documentation](https://cloud.google.com/filestore/docs/mounting-fileshares) for details.


### 1. Nextflow configuration 

- Configure the Nextflow XPACK license definining the variable `NXF_XPACK_LICENSE` 
  in the launching environment, e.g.:

    ```
    export NXF_XPACK_LICENSE=<license string>
    ```

- Create the `nextflow.config` file in the pipeline launching directory with the 
  following settings:

    ```
    plugins {
      id 'xpack-google@1.0.0-beta.1'
    }

    google.region  = 'europe-west2'
    google.lifeSciences.nfsVolumes.nfs1.target = '10.195.15.250:/tower1'
    google.lifeSciences.nfsVolumes.nfs1.mountPath = '/nfs'
    
    process.executor = 'google-lifesciences' 
    ```

    In the above example replace the *region*, NFS *target* and *mountPath* with 
    the values matching your requirements.


### 2. Launch the pipeline execution


Launch the Nextflow pipeline execution specifying the as work directory a 
path in the shared file system, e.g.:

```
./nextflow run hello -w /nfs/scratch
```


## License  

Copyright 2021, Seqera Labs, S.L. All Rights Reserved.