# Contents
1. Pre-requisites
2. Open Source tools
   

<a name="open-source-tools"></a> 
## Pre-requisites

1. Windows machine - 4GB RAM, 100GB Hard disk
2. Oracle Virtual box - 7.0.6 (https://www.virtualbox.org/wiki/Downloads)
3. Linux ubuntu - 22.04.1 (https://ubuntu.com/download/desktop)
4. Install Git -  `sudo apt-get install git`
5. Install GCC compiler -  `sudo apt-get install gcc g++`
6. Install Python - `sudo apt-get install python3`
 
<a name="open-source-tools"></a> 
## Open Source tools

















<a name="magic"></a> 
1. Magic

Magic is a VLSI Layout tool. To begin installing the open source tool, go to http://opencircuitdesign.com/magic/ and download some additional system packages

```
# install M4 preprocessor
  sudo apt-get install m4
# install tcsh shell
  sudo apt-get install tcsh
# install csh shell
  sudo apt-get install csh
# install Xlib.h
  sudo apt-get install libx11-dev
# install tck/tk libraries
  sudo apt-get install tcl-dev tk-dev
# install OpenGL interface (magic -d OGL)
  sudo apt install mesa-common-dev libgl1-mesa-dev libglu1-mesa-dev freeglut3-dev
# install ncurses
  sudo apt-get install libncurses-dev

optional
# install cairo graphics interface (magic -d XR)
$ sudo apt-get install libcairo2-dev
```
After installing the additional packages, use these commands to install magic
```
$  git clone git://opencircuitdesign.com/magic
$  cd magic
$	./configure
$  make
$  sudo make install
```
To view the magic application, type `magic -d XR` in the terminal

<img width="922" alt="magic " src="https://user-images.githubusercontent.com/64173714/218270647-44fd6e14-a885-4431-8df3-e4a74a8e2aa9.png">

<a name="sky130-pdks"></a> 
2. SKY130 PDKs

Process Design Kits, or OpenPDKs, are open source libraries and tools for designing and manufacturing integrated circuits (ICs). They provide a detailed set of design rules, library components, and other resources required for the physical design of an integrated circuit.

To begin installing the open source tool, go to http://opencircuitdesign.com/open_pdks/ and the following steps
```
$  git clone git://opencircuitdesign.com/open_pdks
$  cd open_pdks
$	./configure --enable-sky130-pdk
$  make
$  sudo make install
```


<img width="922" alt="open_pdk" src="https://user-images.githubusercontent.com/64173714/217903616-ad0e3fc3-993c-4d41-bad9-7f43700431a5.png">

<a name="netgen"></a> 
3. Netgen

Netgen is an open-source tool for creating electrical circuits and layouts automatically. It can generate a wide range of electrical circuits, such as full-custom layouts, standard cell-based layouts, and gate arrays. It can also generate routing data and physical masks for use in the fabrication of the final IC.
To begin installing the open source tool, go to http://opencircuitdesign.com/netgen/ and the following steps

```
$  git clone git://opencircuitdesign.com/netgen
$  cd netgen
$	./configure
$  make
$  sudo make install
```
<img width="922" alt="netgen" src="https://user-images.githubusercontent.com/64173714/217904006-9be0048a-e810-40d4-8d72-75bdbd1a1089.png">

<a name="xschem"></a> 
4. Xschem

XSCHEM is an open-source schematic capture tool for designing electronic circuits. It is available at https://github.com/StefanSchippers/xschem, and installation instructions are provided. 

Please make sure we have the following packages installed
``` 
sudo apt-get install flex
sudo apt-get install bison
sudo apt-get install libxpm-dev 
sudo apt-get install libx11-xcb-dev
sudo apt-get install libjpeg-dev
sudo apt-get install gawk
sudo apt-get install mawk
sudo apt-get install xterm
sudo apt-get install tcl-tclreadline
sudo apt-get install libxpm4
sudo apt-get install libx11-xcb-dev
sudo apt-get install libxcb1
sudo apt-get install libxrender-dev
sudo apt-get install libxrender1
sudo apt-get install libx11-dev
```
After installing the additional packages, use these commands to install xschem

```
git clone https://github.com/StefanSchippers/xschem.git xschem
cd xschem
./configure 
make 
sudo make install
```
After installing xschem, we should install xschem_sky130 in order to obtain XSCHEM symbol libraries for the Google-Skywater 130nm process design kit. It is available at https://github.com/StefanSchippers/xschem_sky130, and installation instructions are provided. 

```
cd xschem_library
git clone https://github.com/StefanSchippers/xschem_sky130.git xschem_sky130
cd xschem_sky130/
```
<img width="922" alt="xschem " src="https://user-images.githubusercontent.com/64173714/217904558-a577c075-d8a2-420f-b2c8-8a90bf9e345e.png">

<a name="ngspice"></a> 
5. Ngspice

ngspice is an open-source electronic circuit simulator that can be used for circuit analysis and simulation.

The following packages might be needed to be installed on your system
```
sudo apt-get install adms
sudo apt-get install autoconf
sudo apt-get install libtool
sudo apt-get install libxaw7-dev
sudo apt-get install build-essential
sudo apt-get install libc6-dev
sudo apt update
sudo apt upgrade
sudo apt-get install manpages-dev man-db manpages-posix-dev
sudo apt-get install libreadline6-dev
sudo apt-get update -y
```
To install ngspice go to https://github.com/ngspice/ngspice and follow this commands
```
 git clone https://github.com/ngspice/ngspice.git ngspice
 cd ngspice
 ./autogen.sh --adms
 mkdir release
 cd release
 ../configure  --with-x --with-readline=yes --disable-debug
 make
 sudo make install
 ```
<img width="921" alt="ngspice" src="https://user-images.githubusercontent.com/64173714/217905232-11985213-ed63-4449-9146-add781f941f9.png">

<a name="verifying-the-installation-of-open_pdk"></a>
Verifying the installation of open_pdk

To create an initial working directory, copy the following files:
```
$ mkdir Lab1
$ cd Lab1
$ mkdir mag
$ mkdir netgen
$ mkdir xschem
$ cd xschem
$ cp /usr/local/share/pdk/sky130A/libs.tech/xschem/xschemrc .
$ cp /usr/local/share/pdk/sky130A/libs.tech/ngspice/spinit .spiceinit
$ cd ../mag
$ cp /usr/local/share/pdk/sky130A/libs.tech/magic/sky130A.magicrc .magicrc
$ cp /usr/local/share/pdk/sky130A/libs.tech/magic/sky130A.tech .sky130A.tech
$ cd ../netgen
$ cp /usr/local/share/pdk/sky130A/libs.tech/netgen//sky130A_setup.tcl .
```
