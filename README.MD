# Running minoTour with Docker
minoTour is developed as a docker container, so docker must be installed and correctly set up if this is how you wish to run minotour.

Docker is a set of platform as a service products that use OS-level virtualisation to deliver software in packages called containers. 
Containers are isolated from one another and bundle their own software, libraries and configuration files;
they can communicate with each other through well-defined channels.

If Docker is not installed - instructions for installing can be found [here](https://docs.docker.com/install/).
Make sure Docker is installed and running at the end of the installation. You can check this by running the command below:

```shell
docker --version
```

Docker Engine version 19.03+ is required, with 20+ preferred.

## Running pre-built minotour

First, clone our Docker minotour project from [here](https://github.com/LooseLab/docker-compose-test)

```bash
git clone https://github.com/LooseLab/docker-compose-test.git
```

Change to the docker-compose-test folder
```bash
cd docker-compose-test
``` 

Start the images (-d means daemon, so the images run in the background)
```bash
docker-compose up -d
```

## First time setup
If the images are not present they will be downloaded.

After a few seconds, you should be able to access minoTour at <http://localhost:8100/>

To create an admin user:

```bash
docker exec -it web python manage.py createsuperuser
``` 

Or to create a standard user just use the GUI!


### Uploading references, primer schemes and Centrifuge indexes to minoTour

References less than 256Mb in size can be uploaded using the minoTour GUI. If your reference is bigger, you will need to add it on the command line.

Inside your docker-compose-test directory there exists a folder named minotour_files. This folder is mounted into the docker container, and thus minoTour
can access files inside this folder. To start with the folder should currently have a structure of:
 
    minotour_files
    |
    |-- primer_schemes
    |   |-- nCoV-2019
    |   |   |-- V1
    |   |   |-- V2
    |   |   |-- V3
    |   |   `-- V4
    |   |-- Nipah
    |   |   `-- V1
    |   |-- SARS-CoV-2 -> nCoV-2019
    |   |-- scripts
    |   `-- ZaireEbola
    |       |-- V1
    |       |-- V2
    |       `-- V3
    `-- references
        |-- minimap2_indexes
        `-- reference_files

## To add references over 256Mb
*__Note: You will need your API-KEY, found in your profile details after login__*

Copy the reference (or sym-link if that's your flavour) into the minotour_files/references/reference_files directory and then run the following command
```bash
docker exec web python manage.py add_reference -k <API-KEY> /var/lib/minotour/apps/data/references/reference_files/<REF_FILE_NAME>
```

## To add Primer schemes for the artic pipeline

For a scheme two files are required:
- <new_species>.scheme.bed
- <new_species>.reference.fasta

Where new species has the same name. Their is a management page for primer schemes in the right hand side bar of minoTour. Drag and drop the two files (or 
use the built in browser selector), give them a version name and upload them. And you're good to go!

## To add Centrifuge indexes


Create a centrifuge index folder in the minotour_files folder and add the .cf files for your index, for example - 
    
    minotour_files
    |
    |-- centrifuge_indexes
        |-- example.1.cf
        |-- example.2.cf
        |-- example.3.cf
        |-- example.4.cf

We do not distribute any indexes, a compressed pre-made index containing bacteria and viruses can can be dowloaded from [JHU](ftp://ftp.ccb.jhu.edu/pub/infphilo/centrifuge/data/p_compressed_2018_4_15.tar.gz).
 
Stop the containers currently running

```bash 
docker-compose stop
```

Set the `MT_CENTRIFUGE_INDEX` variable in the docker-compose.yml to `/var/lib/minotour/data/centrifuge_indexes/example` where example is the name of your index, excluding any numbers and file suffix.

Restart your containers -

```bash
docker-compose up -d
```

### Debugging
Please refer to [Debugging.md](Debugging.md)
