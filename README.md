# Code-Aster-MPI-in-Singularity-of-SM2022
How to build the MPI Version of Code Aster 16.4 within the Singularity Container of Salome-Meca 2022 

08.11.2023 Repository created

________________________________________________________________________________________________________
Download container built according to recipe below at  XXXXXXXXXXXXXXx


________________________________________________________________________________________________________

In the following tutorial we will show how to build the MPI Version of Code Aster 16.4 within the Singularity Container of Salome-Meca 2022. Many thanks to forum member EnKiNekiDela, who describes very clearly how to build the MPI version in his own Github repository at https://github.com/bursica/Code_Aster-MPI-in-Singularity-of-SM2022. However, in the recipe below a slightly different approach is shown.
Compared to our old recipe (there we downloaded a .zip of CA source code), this time we will download the Code_Aster Gitlab repository as a whole and choose the version in the code. Basically, this way we are able to even choose another version of Code_Aster (though with some restrictions regarding the chosen Salome-Meca .sif container. For info on which container to use for which CA version see https://gitlab.com/codeaster-opensource-documentation/opensource-installation-development/-/blob/main/devel/changelog.md). In the description below we will choose Code_Aster 16.4 which is a testing, rather than a stable version. The latest stable version as of writing these lines is 15.6, which is quite close to 15.4 in the SM2021 repository (https://github.com/emefff/Code-Aster-MPI-in-Singularity-of-SM2021). A big thanks of course goes to EDF's R&D-Team, the developers of Salome-Meca and Code_Aster 👍. 

This recipe and the resulting container were tested in Ubuntu 22.04 LTS. Please be aware, that some slight modifications might be necessary when using Ubuntu 22.04LTS (see Code_Aster forum https://code-aster.org/V2/spip.php?rubrique2 for these known issues).
________________________________________________________________________________________________________

If you do not have singularity installed, the version coming with Ubuntu 22.04LTS is fine.
The version used here was 'singularity-ce version 3.10.3-focal', which is the version provided by Ubunt repositories.
If you want to compile singularity yourself, please refer to https://github.com/sylabs/singularity/blob/main/INSTALL.md.

________________________________________________________________________________________________________
Download the necessary Singularity Container of Salome-Meca 2022 with 

wget -c https://www.code-aster.org/FICHIERS/singularity/salome_meca-lgpl-2022.1.0-1-20221225-scibian-9.sif





