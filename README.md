# Code-Aster-MPI-in-Singularity-of-SM2022
How to build the MPI Version of Code Aster 16.4 inside the Singularity Container of Salome-Meca 2022 

08.11.2023 Repository created

________________________________________________________________________________________________________
Download container built according to recipe below at https://drive.google.com/file/d/1mywsQ_0xRCTm1uu1ivUgJWUyL0Imb6yf/view


________________________________________________________________________________________________________

In the following tutorial we will show how to build the MPI Version of Code Aster 16.4 inside the Singularity Container of Salome-Meca 2022. Many thanks to forum member EnKiNekiDela, who describes very clearly how to build the MPI version in his own Github repository at https://github.com/bursica/Code_Aster-MPI-in-Singularity-of-SM2022. However, in the recipe below a slightly different approach is shown.
Compared to our old recipe (there we downloaded a .zip of CA source code), this time we will download the Code_Aster Gitlab repository as a whole and choose the version in the code. Basically, this way we are able to even choose another version of Code_Aster (though with some restrictions regarding the chosen Salome-Meca .sif container. For info on which container to use for which CA version see https://gitlab.com/codeaster-opensource-documentation/opensource-installation-development/-/blob/main/devel/changelog.md). In the description below we will choose Code_Aster 16.4 which is a testing, rather than a stable version. The latest stable version as of writing these lines is 15.6, which is quite close to 15.4 in the SM2021 repository (https://github.com/emefff/Code-Aster-MPI-in-Singularity-of-SM2021). A big thanks of course goes to EDF's R&D-Team, the developers of Salome-Meca and Code_Aster 👍. 

This recipe and the resulting container were tested in Ubuntu 22.04 LTS. Please be aware, that some slight modifications might be necessary when using Ubuntu 22.04LTS (see Code_Aster forum https://code-aster.org/V2/spip.php?rubrique2 for these known issues).
________________________________________________________________________________________________________

If you do not have singularity installed, the easiest way is to install a .deb package (for Ubuntu 22.04 this is the 'jammy'-package, although we used an older version here, see below):

https://github.com/sylabs/singularity/releases

If you want to compile singularity yourself follow the instructions here:

https://github.com/sylabs/singularity/blob/main/INSTALL.md

or here

https://docs.sylabs.io/guides/3.0/user-guide/installation.html#install-on-linux

To check the installed version 

```
singularity --version
```

The versions tested were 'singularity-ce version 3.10.3-focal' (also for the .sif in above link) and 'singularity-ce version 4.0.1-jammy'.
Both versions work.

________________________________________________________________________________________________________
Download the necessary Singularity Container of Salome-Meca 2022 with 

```
wget -c https://www.code-aster.org/FICHIERS/singularity/salome_meca-lgpl-2022.1.0-1-20221225-scibian-9.sif
```

and place it in your home folder ${HOME} (also ~/ or $HOME). The file size should be approx. 6.2GB. 

Alternatively, the container can be downloaded directly from the page https://code-aster.org/V2/spip.php?article303, however, we recommend using wget -c.

________________________________________________________________________________________________________
Now we will download the Code_Aster repository from Gitlab. Some of the below instructions can be found here: https://gitlab.com/codeaster-opensource-documentation/opensource-installation-development/-/blob/main/devel/compile.md#preparing-the-access-to-the-container but not all of them are necessary.

First we will create a folder for the Code_Aster repository with

```
mkdir -p ${HOME}/dev/codeaster
```

We clone the whole repository with

```
cd ${HOME}/dev/codeaster

git clone https://gitlab.com/codeaster/src.git

git clone https://gitlab.com/codeaster/devtools.git
```
________________________________________________________________________________________________________
Again, we need to create an overlay to the container, because we need space for additional data. This time, we need a little more space

```
cd ${HOME}

dd if=/dev/zero of=overlay.img bs=1M count=1500 && mkfs.ext3 overlay.img

singularity sif add --datatype 4 --partfs 2 --parttype 4 --partarch 2 --groupid 1 salome_meca-lgpl-2022.1.0-1-20221225-scibian-9.sif overlay.img
```
Check the filesize of the container, it should now be approx. 7.7 (that's the 6.2GB+1.5GB)

The whole building process should run well, we may remove the overlay.img

```
rm overlay.img
```

We are still in our ${HOME} directory.
________________________________________________________________________________________________________
Now the fun part begins. First we need to bind our ${HOME} directory to the now larger container and enter the container shell with

```
sudo singularity run --bind ${HOME}:${HOME} -w ${HOME}/salome_meca-lgpl-2022.1.0-1-20221225-scibian-9.sif shell
```

We are now inside the container shell which is easily visible by the prefix 'Singularity> ' (for clarity we leave the prefix in the appropriate code also). Our current directory is still the ${HOME} of our user, be aware we are ***root*** now (we used sudo). You can check your current directory any time with 'pwd'.

![Bildschirmfoto vom 2023-11-08 14-16-02](https://github.com/emefff/Code-Aster-MPI-in-Singularity-of-SM2022/assets/89903493/702dae4a-771c-40fa-a5ef-fa39c2723ed4)

We change to

```
Singularity> cd dev/codeaster/src/code_aster
```

Be careful, as we are ***root*** inside the container, the ${HOME} is not your /home/$USER directory anymore.

Inside Salome-Meca, we need information about the installed version of Code_Aster, therefore we create and edit the following file

```
Singularity> nano pkginfo.py
```

Inside nano, we type or copy-paste below text

```
pkginfo = [(16, 4, 0), 'n/a', 'n/a', '08/11/2023', 'n/a', 1, ['no source repository']]
```

and save this file (CTRL+O and ENTER, this nano speaks french 😊).
Note: modify as needed, if you install a different version etc.

The following lines build Code_Aster in the container. Do not just copy with the button, execute them in succession, one by one in the container shell.
If you get any errors, make sure you did not forget any of the previous steps. 

The cd command brings us into /home/$USER/dev/codeaster/src. Be careful, we cannot just type 'cd $USER/dev/codeaster/src' because we are ***root***. 
The git checkout command is very handy, basically at this step we choose the version we want. It could be a different version also, though make sure your Code_Aster version still matches the container's version.
The other command should not create problems. Use a larger number of cores (e.g. jobs=8) to your liking.

```
Singularity> cd ..

Singularity> git checkout tags/16.4.0

Singularity> export TOOLS="/opt/salome_meca/V2022.1.0_scibian_univ/tools"

Singularity> export ASTER_TESTING="${TOOLS}/Code_aster_testing-1640"

Singularity> ./waf_mpi configure --prefix=${ASTER_TESTING} --install-tests --jobs=4

Singularity> ./waf_mpi build --jobs=4

Singularity> ./waf_mpi install

Singularity> echo "vers : testing_mpi:/opt/salome_meca/V2022.1.0_scibian_univ/tools/Code_aster_testing-1640/share/aster" >> ${TOOLS}/Code_aster_frontend-202200/etc/codeaster/aster

Singularity> exit
```

We are now in our normal Linux shell, outside the container. 

This is it, the build is complete. In Asterstudy you'll find this MPI version in the 'Run Parameters' under 'testing_mpi (16.4.0)' which is exactly the text we told it to show above in the second-to-last line.


The container may be launched in the usual way (if you didn't prepare a symlink) with 

```
singularity run --app install salome_meca-lgpl-2022.1.0-1-20221225-scibian-9.sif
```

Here you may launch Salome_Meca in GPU mode with

```
./salome_meca-lgpl-2022.1.0-1-20221225-scibian-9.sif
```

or if you do not have a Nvidia GPU

```
./salome_meca-lgpl-2022.1.0-1-20221225-scibian-9.sif --soft
```

Alternatively, you can also enter the container shell with

```
./salome_meca-lgpl-2022.1.0-1-20221225-scibian-9.sif --shell
```

emefff@gmx.at












