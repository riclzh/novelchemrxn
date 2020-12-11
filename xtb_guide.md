---
layout: page
title: "XTB guide"
permalink: /xtb_guide/
---

Section|Content|
---|---|
1|[Preparation](#prep)|
2|[XTB](#xtb)|
3|[Running](#running)|

### Prep
1. This guide will serve to help you do [XTB](https://xtb-docs.readthedocs.io/en/latest/gfnff.html) calculations linked to [Gaussian software](http://gaussian.com/).
2. A script (xtb.sh) and two executables (genxyz and extderi) must be downloaded from [gau_xtb](http://sobereva.com/soft/gau_xtb/). The code is written by Dr [Tian Lu](http://sobereva.com/Tian_Lu.html). He has made many other interesting computational chemistry programs too.
3. The gau_xtb.zip file can be unzipped and transferred into Gadi. Remember to ```chmod +x files``` in order to make the executable files work.
3. Next download the precompiled version of [XTB](https://github.com/grimme-lab/xtb/releases/tag/v6.3.3) and transfer the compressed file into Gadi.
4. After ```tar xf xtb-200917.tar.xz``` to extract the tarball you will find that it should contain ```bin``` and ```share``` - if it doesn't you have downloaded the source files which you will need to compile yourself.
5. In home ```/~``` directory ```nano .bashrc``` and add these lines:
```
export PATH=$PATH:~/xtb-6.3.3/bin
export XTBPATH=~/xtb-6.3.3/share/xtb
export OMP_NUM_THREADS=4
export MKL_NUM_THREADS=4
export OMP_STACKSIZE=1000m
ulimit -s unlimited
```
6. Exit and ```. .bashrc```

### XTB
1. The calculations will use the parameter which is GFN2-xtb. This is a semi-empirical tight binding model which means it will take into account non-covalent interactions and is up a level from semi-empirical methods like PM6. 
2. Read more about the method [here](https://pubs.acs.org/doi/10.1021/acs.jctc.8b01176)
3. Since GFN2 is considered a semi-empirical qc method, it should be applicable to calculating large molecules like small proteins as the computational cost is lower than DFT methods like B3LYP. 
4. You can consider using this method to calculate and locate minimum of transition state structures first. It will give a good guess of the electronic structure before optimizing further with DFT. It is linked by gau_xtb program with Gaussian.

### Running
1. The script ```xtb.sh```, and executable files ```extderi``` and ```genxyz``` have to be in the working folder. 
2. In the keywords route the input gjf should have ```external='./xtb.sh'``` included. If frequency job is required, use the gaussian ```link1``` keyword. A typical optimization job:
```
%chk=mol.chk
%nprocshared=4
%mem=8GB
#p opt=nomicro external='./xtb.sh'

Mol opt by gfn2-xtb

0 1
 (xyz cartesian)
<blank space>
--link1--
%chk=mol.chk
%nprocshared=4
%mem=8GB
#p freq external='./xtb.sh' geom=allcheck
<blank space>
```
3. For TS and PES, change ```opt=nomicro``` to ```opt=(ts,calcfc,noeigen,nomicro)``` or ```opt=(modredundant,maxcyc=20,nomicro)```.

